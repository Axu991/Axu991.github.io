---
title: "FreeRTOS: A Practical Introduction to Real-Time Task Scheduling"
date: 2026-10-27
description: "How FreeRTOS schedules tasks on microcontrollers — preemptive priority scheduling, task states, context switching, inter-task communication, and memory allocation strategies."
tags: ["Embedded", "FreeRTOS", "RTOS", "STM32", "ESP32"]
categories: ["Engineering Notes"]
---

## What is FreeRTOS?

FreeRTOS is a free, open-source real-time operating system kernel designed for microcontrollers and small microprocessors. It manages multiple tasks on a single CPU, deciding which task runs, when it runs, and for how long. The core is small — typically a few hundred lines of C plus architecture-specific assembly — and it runs directly on the hardware without an MMU.

Official documentation: [freertos.org](https://www.freertos.org). Source code: [github.com/FreeRTOS/FreeRTOS-Kernel](https://github.com/FreeRTOS/FreeRTOS-Kernel).

## Why an RTOS?

On a bare-metal loop, the application calls functions sequentially in `main()`. Adding sensors, networking, and user input means either a giant `switch-case` state machine or cooperative multitasking where every function must yield. Both become fragile as the system grows: a slow I2C read blocks everything else.

An RTOS lets the developer write independent tasks that run concurrently. The kernel handles timing, priorities, and blocking. A control loop can run at a fixed 1 kHz rate while a log task handles UART output at its own pace, without the programmer writing explicit timing glue.

## Task scheduling: preemptive priority

FreeRTOS uses **preemptive priority-based scheduling**:

- The scheduler always runs the **highest-priority ready task**.
- If a lower-priority task is running and a higher-priority task becomes ready, the kernel immediately context-switches to the higher task.
- Tasks of equal priority take turns in time slices (round-robin), if `configUSE_TIME_SLICING` is enabled.

This means the developer assigns priorities based on how time-critical each task is. A motor control loop gets the highest priority; a display refresh gets the lowest.

A direct consequence: a high-priority task that never blocks will starve every lower-priority task. Every time-critical task must call a blocking API — `vTaskDelay()`, `xSemaphoreTake()`, `ulTaskNotifyTake()`, or a queue receive — to release the CPU when it has nothing to do.

## Task states

At any moment, a task is in one of four states:

| State | Meaning |
|---|---|
| **Running** | Currently executing on the CPU. Only one task runs at a time. |
| **Ready** | Runnable, waiting for CPU time because a higher-priority task holds it. |
| **Blocked** | Waiting for an event — a delay to expire, a semaphore, a queue message, or a notification. |
| **Suspended** | Explicitly suspended via `vTaskSuspend()`. Does not participate in scheduling until resumed. |

A task in Blocked or Suspended state consumes zero CPU. This is how an RTOS achieves low power: idle tasks enter sleep, and the kernel wakes them only when an event arrives.

## Context switching

Each task has its own **stack**. When the scheduler swaps tasks:

1. The current task's CPU registers (program counter, stack pointer, general-purpose registers) are pushed onto that task's stack.
2. The stack pointer is switched to the next task's stack.
3. The saved registers are popped from the new task's stack.
4. Execution resumes exactly where that task left off.

This happens entirely in hardware-assisted assembly (PendSV on ARM Cortex-M). The application code sees a continuous call stack and does not know a switch occurred. Context switch cost is typically a few microseconds on a 100 MHz Cortex-M4.

## A practical task layout

A typical motor-control or robot firmware might look like:

| Task | Priority | Responsibility |
|---|---|---|
| `ControlTask` | Highest (e.g. 5) | Reads IMU, runs PID / RL policy, writes motor commands at 1 kHz. |
| `CommTask` | Medium (3) | Receives commands over UART/CAN, sends telemetry. |
| `LogTask` | Low (1) | Formats and writes logs to SD card or USB. |
| `Idle` | 0 (auto) | Kernel-created; can run low-power hooks or clean up. |

The control task blocks on a hardware timer semaphore each cycle. The communication task blocks on a UART queue. The log task blocks on a log queue. When nothing needs attention, the idle task runs and the microcontroller can enter sleep.

## Inter-task communication

Tasks rarely work in isolation. FreeRTOS provides several primitives:

- **Queues** — thread-safe FIFO buffers. One task writes, another reads. Data is copied, not passed by reference, which avoids race conditions.
- **Semaphores** — binary or counting. Used to signal an event from an ISR to a task, or to limit access to a shared resource.
- **Mutexes** — like a binary semaphore but with priority inheritance. If a low-priority task holds a mutex that a high-priority task wants, the low task temporarily gets the high priority until it releases the mutex. This reduces priority inversion.
- **Task notifications** — a lightweight, per-task signal that avoids allocating a queue. Faster and uses less RAM than a queue when only one event source exists.
- **Event groups** — set or clear bits; a task can wait for multiple conditions simultaneously.

## Memory management

The kernel itself needs RAM for task stacks, TCBs (task control blocks), queues, and semaphores. FreeRTOS ships with several heap implementations:

- **heap_1** — allocate only, never free. Simplest, used when tasks are created once and never deleted.
- **heap_2** — allocate and free, but does not coalesce adjacent free blocks. Deprecated.
- **heap_4** — allocate and free with coalescing. The most common choice for dynamic systems.
- **heap_5** — like heap_4 but can span multiple non-contiguous memory regions.

For safety-critical or predictable systems, tasks can also be created statically: all memory is declared at compile time, and the kernel never calls `malloc`.

## Interrupts and the kernel

ISRs must be short. They cannot call most blocking APIs directly. FreeRTOS provides `FromISR` variants (e.g. `xQueueSendFromISR()`) that are safe to call from interrupt context. At the end of an ISR, a `portYIELD_FROM_ISR()` macro triggers a context switch if a higher-priority task was woken by the ISR.

## Further reading

- [FreeRTOS Official Website](https://www.freertos.org) — API reference and kernel docs.
- [FreeRTOS User Guide (PDF)](https://www.freertos.org/Documentation/1615a2c3-c6c0-4f9e-826b-4d620a0e067d/FreeRTOS_User_Guide.pdf) — official manual.
- [FreeRTOS-Kernel on GitHub](https://github.com/FreeRTOS/FreeRTOS-Kernel) — source code and porting layers.
- [Mastering the FreeRTOS Real Time Kernel](https://www.freertos.org/Documentation/1615a2c3-c6c0-4f9e-826b-4d620a0e067d/FreeRTOS_User_Guide.pdf) — the standard textbook, freely available from the kernel maintainer.
