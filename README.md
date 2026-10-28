# DurableKV — Persistent Key-Value Storage Engine

**A Redis-Inspired Storage Engine with Disk-Backed Command Queuing, Explicit Execution, and Crash-Recovery Support**

## Overview

DurableKV is a persistent key-value storage engine that demonstrates how data operations can be separated from command submission and execution. It provides a web-based console for entering Redis-style commands, a queue for pending operations, and a data view for inspecting stored key-value pairs.

Unlike a conventional command interface that immediately applies every operation, DurableKV records submitted commands in a disk-backed command log and executes queued operations when the user explicitly triggers **Run queued**. This design provides a foundation for examining persistence, pending-operation recovery, and storage reliability.

The application runs locally and presents its interface at `http://localhost:8081` in the demonstrated setup.

## Problem Statement

Storage systems must preserve submitted operations and manage data updates reliably, including when interruptions occur during execution. If commands are submitted but have not yet been applied to the stored data, a system needs a way to retain those operations and make them available after a restart.

DurableKV addresses this problem by separating command logging from command execution. Submitted commands are saved to disk, queued until explicitly executed, and made available for recovery according to the implemented recovery mechanism.

## Objectives

- Implement a Redis-inspired command interface for key-value operations.
- Persist queued commands before executing them.
- Separate command submission from execution.
- Store and display key-value data.
- Preserve pending commands across application restarts.
- Support recovery of previously stored data and pending commands.
- Provide persistence-related operations and command utilities.
- Evaluate functionality, persistence behavior, and recovery through testing.

## Key Features

### 1. Interactive Command Console

The console accepts commands such as:

```text
SET name pooja
GET name
DEL name
```

It displays command output and indicates whether an operation has been queued or executed.

### 2. Disk-Backed Command Queue

When a command is submitted, the application records it in its command log before execution. Pending operations are displayed in a separate queue.

For example:

```text
SET 112 sukanya
DEL 112
```

These commands can remain queued until the user selects **Run queued**.

### 3. Explicit Command Execution

The **Run queued** action processes pending commands. This allows the user to observe the difference between a command being recorded and its effects being applied to the data.

### 4. Persistent Key-Value Data

The data panel displays stored keys and their corresponding values. The interface also provides a filter for searching displayed keys or values.

### 5. Restart Recovery

The interface reports recovered data and pending commands when the application starts. The command log provides a record of operations that may still need execution after an interruption.

The recovery guarantees depend on the implementation's command-log durability, execution tracking, and recovery logic.

### 6. Redis-Style Command Support

The interface displays the following command shortcuts:

| Command | Purpose |
|---|---|
| `SET` | Store a value under a key. |
| `GET` | Retrieve a value by key. |
| `DEL` | Delete a key. |
| `EXISTS` | Check whether a key exists. |
| `KEYS` | List keys. |
| `DBSIZE` | Report the number of stored keys. |
| `INCR` | Increment a numeric value. |
| `DECR` | Decrement a numeric value. |
| `APPEND` | Append text to a value. |
| `STRLEN` | Return a string's length. |
| `FIND` | Search for matching data according to the application's implementation. |
| `FLUSHALL` | Clear stored data according to the application's implementation. |
| `SAVE` | Trigger the application's save operation. |
| `BGSAVE` | Trigger the application's background-save operation, if implemented. |
| `BGREWRITEAOF` | Trigger the application's append-only-log rewrite operation, if implemented. |

These descriptions identify the intended command purposes. Exact behavior, supported syntax, and persistence semantics should be verified against the implementation; displaying a command shortcut alone does not establish that its full Redis behavior is supported.

## System Architecture

```text
              User / Client
                   |
                   v
          +-------------------+
          |  Web Interface    |
          |  Command Console  |
          +-------------------+
                   |
                   v
          +-------------------+
          | Command Parsing   |
          | and Validation    |
          +-------------------+
                   |
                   v
          +-------------------+
          | Disk-Backed       |
          | Command Queue     |
          +-------------------+
                   |
             Run Queued
                   |
                   v
          +-------------------+
          | Command Execution |
          +-------------------+
                   |
                   v
          +-------------------+
          | Key-Value Storage |
          +-------------------+
                   |
                   v
          +-------------------+
          | Data View and     |
          | Recovery          |
          +-------------------+
```

*This diagram illustrates the conceptual workflow. The exact internal modules and data flow should match the final source code.*

## How It Works

Consider the command:

```text
SET name pooja
```

Here, `name` is the key and `pooja` is the value.

1. **Command submission:** The user enters the command in the console and presses Enter.
2. **Command logging:** The submitted command is saved to disk and added to the pending queue.
3. **Pending state:** The command remains queued without being executed immediately.
4. **Execution:** The user selects **Run queued**, causing the application to process pending commands.
5. **Data update:** The command updates the key-value store according to the implemented storage logic.
6. **Recovery:** After a restart, the application restores previously persisted data and identifies pending commands according to its recovery mechanism.

This workflow makes command durability and execution state visible through the interface.

## Crash-Recovery Design

Crash recovery is a central design objective of DurableKV.

| System state | Intended behavior |
|---|---|
| Command submitted | Record the command in the persistent command log. |
| Command pending | Keep the command available for later execution. |
| Command executed | Apply the operation to the key-value data. |
| Application restarted | Recover stored data and pending commands according to the recovery logic. |
| Interrupted execution | Determine which operations remain pending and process them according to the implementation. |

A durable command log helps preserve operations that have not yet been executed. However, complete crash consistency also depends on reliably flushing log writes, identifying completed operations, and preventing unintended duplicate effects during recovery.

## User Interface

The web interface is organized into the following areas:

- **Status header:** Displays the connection state, stored-key count, and queued-command count.
- **Console:** Accepts commands and displays their results.
- **Queued Commands:** Shows pending commands and provides the execution control.
- **Data panel:** Displays stored keys and values with a filtering option.
- **Command shortcuts:** Provides quick access to data and persistence operations.
- **Utilities:** Includes demo-data loading, test-command generation, and Help.

### Interface Screenshot

Add your actual project screenshot to the repository, for example at `screenshots/durablekv-dashboard.png`, and display it here:

```markdown
![DurableKV web interface](screenshots/durablekv-dashboard.png)
```

## Technology Stack

Document the exact technologies used in the implementation.

| Component | Technology |
|---|---|
| Application name | DurableKV |
| Command interface | Redis-inspired command syntax |
| Web interface | Specify the actual frontend technology |
| Backend | Specify the actual backend language and framework |
| Command persistence | Disk-backed command log |
| Data persistence | Specify the actual secondary-storage mechanism |
| Local interface | `http://localhost:8081` in the demonstrated setup |

**Redis clarification:** DurableKV is presented as a Redis-inspired implementation. It should not be described as running the official Redis server unless the application actually integrates with one. The presence of Redis-style commands does not establish that the underlying Redis server or its complete command semantics are being used.

## Getting Started

### Prerequisites

Install or configure the dependencies required by the actual project implementation. The required language runtime, libraries, and startup procedure depend on the backend and frontend technologies used.

### Run the Application

1. Obtain the project source code.
2. Install the project's documented dependencies.
3. Start the backend and frontend using the project's actual startup commands.
4. Open `http://localhost:8081` in a browser if the application is configured to use that address.
5. Enter a command such as `SET name pooja`.
6. Confirm that the command appears in the pending queue.
7. Select **Run queued** and verify the result in the data panel.
8. Restart the application and test persistence and recovery.

Add the verified installation and startup commands to this section before publishing the repository. Do not use generic commands that do not match the actual source code.

## Testing and Validation

The following tests are recommended for verifying the system.

| Test | Expected result |
|---|---|
| Submit a `SET` command | The command is recorded and added to the queue. |
| Inspect a pending command | It remains pending until execution is triggered. |
| Run queued commands | The application processes the pending operations. |
| Retrieve an existing key | The corresponding value is returned. |
| Update a key | Subsequent retrieval reflects the update. |
| Delete a key | The deleted key is no longer present. |
| Restart with pending commands | Persisted pending commands remain available for recovery. |
| Restart after execution | Previously persisted data is restored. |
| Submit invalid input | The application handles the invalid command appropriately. |
| Queue a large number of commands | The application can be evaluated for processing time and correctness. |

Record actual test results, screenshots, test inputs, and observed behavior in the repository. Expected results should not be presented as completed test results until they have been verified.

## Performance Evaluation

DurableKV can be evaluated using the following metrics:

- Command submission and logging latency
- Command execution time
- Key retrieval latency
- Throughput for repeated operations
- Recovery time after an application restart or simulated crash
- Number of pending commands recovered
- Performance with increasing numbers of stored keys
- Command-log size before and after any supported rewrite operation

Each reported measurement should include the test environment, workload, number of operations, measurement method, and actual result. Performance targets should be listed separately from measured values.

## Scope and Limitations

The project focuses on persistent key-value operations, disk-backed command queuing, explicit execution, and recovery of pending commands.

The following capabilities must be evaluated against the actual implementation before they are claimed as guaranteed features:

- Atomic execution of commands
- Recovery from abrupt power loss
- Prevention of duplicate execution during recovery
- Concurrent command processing
- Background persistence and append-only-log rewriting
- Advanced indexing and compaction
- Full compatibility with the Redis server command specification

## Future Enhancements

- Improve execution tracking and recovery validation.
- Add automated tests for crashes at different execution stages.
- Strengthen command-log durability and error handling.
- Add performance benchmarks for large command queues.
- Improve the data view and execution-status reporting.
- Optimize storage and command-log management.
- Expand command compatibility and automated regression testing.

## Development Documentation

Organize project evidence by development gate according to the college's requirements:

- **Gate 0:** Problem statement, objectives, scope, and feasibility.
- **Gate 1:** Functional requirements, non-functional requirements, use cases, and acceptance criteria.
- **Gate 2:** Approved design and development evidence.
- **Gate 3:** Implementation and integration evidence.
- **Gate 4:** Testing, recovery validation, and performance evaluation.
- **Gate 5:** Final demonstration, validated results, and project report.

Confirm the required deliverables for each gate using the applicable course guidelines.

## References

- [Redis Commands Reference](https://redis.io/docs/latest/commands/) — reference for Redis command syntax and intended command behavior.
- [Redis `SET` Command](https://redis.io/docs/latest/commands/set/) — reference for key-value assignment syntax.
- [Redis Persistence Commands](https://redis.io/docs/latest/commands/) — reference for commands such as `SAVE`, `BGSAVE`, and `BGREWRITEAOF`.

These references provide technical context for the Redis-inspired command interface and do not imply that DurableKV uses the official Redis server.

## Contributors

Add the project team members, their responsibilities, and the project guide's name.

## Academic Project

Developed as a college mini project to explore persistent key-value storage, command logging, explicit execution, and crash-recovery concepts.
