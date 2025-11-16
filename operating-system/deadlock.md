# Deadlock

## Overview

A deadlock is a situation in a concurrent system where a set of processes are blocked because each process holds resources and waits for resources held by other processes in the set. No process can proceed, and the system is stuck until external action is taken.

Deadlocks occur in operating systems, databases, distributed systems, and concurrent applications whenever processes compete for limited resources.

## Necessary Conditions (Coffman Conditions)

Four conditions are necessary for a deadlock to occur; these are known as the Coffman conditions. Breaking any one of them prevents deadlock:

- **Mutual Exclusion:** Some resources are non-shareable (only one process at a time). If all resources are sharable, deadlock cannot occur.
- **Hold and Wait:** Processes hold at least one resource and wait to acquire additional resources held by others.
- **No Preemption:** Resources cannot be forcibly taken from a process; they must be released voluntarily.
- **Circular Wait:** A circular chain of processes exists, where each process waits for a resource held by the next process in the chain.

## Example

Consider two processes P1 and P2 and two resources R1 and R2. If P1 holds R1 and waits for R2 while P2 holds R2 and waits for R1, neither can proceed — a deadlock.

## Detection

Detection algorithms identify deadlocks at runtime. Two common approaches:

- **Resource Allocation Graph (RAG):** Model processes and resources as a directed graph. An edge from resource to process indicates allocation; an edge from process to resource indicates a request. A cycle indicates a possible deadlock when resources have single instances; with multiple instances, cycles may not imply deadlock.
- **Wait-For Graph:** Simplifies the RAG by collapsing resource nodes. Nodes are processes; an edge P_i -> P_j means P_i waits for a resource held by P_j. A cycle in the wait-for graph implies deadlock.

Detection algorithms periodically analyze these graphs (or use variations of the OS detection algorithm) and report which processes are deadlocked.

## Prevention

Prevention aims to ensure that at least one Coffman condition cannot hold. Common strategies:

- **Eliminate Mutual Exclusion:** Make resources shareable if possible (not always feasible for resources like printers or locks).
- **Eliminate Hold-and-Wait:** Require processes to request all required resources at once and block if not all are available. This can lead to low resource utilization and starvation.
- **Enable Preemption:** Allow the system to preempt resources from processes (save state, roll back, and reassign). Practical for some resource types but complex for others.
- **Break Circular Wait:** Impose an ordering (partial or total) on resource types and require processes to request resources in increasing order. This prevents cycles.

Prevention can be conservative and may hurt concurrency or throughput, so it is used selectively.

## Avoidance

Avoidance techniques allow the system to make resource-allocation decisions that steer clear of unsafe states (states that could lead to deadlock). The classic example is the **Banker's Algorithm**:

- Each process declares the maximum number of instances of each resource type it may need.
- The system simulates allocations and ensures that granting a request results in a safe state — a state where there is at least one sequence of process completions that allows all processes to finish.

Banker's algorithm works for systems with a fixed number of resource types and known maximum needs, but it requires advance information and incurs runtime overhead.

## Recovery

If deadlock is detected, the system must recover. Common recovery techniques:

- **Process Termination:** Abort one or more processes involved in the deadlock until the deadlock is broken. Strategies include aborting the lowest-cost process or aborting processes until the system is deadlock-free.
- **Resource Preemption:** Temporarily take resources away from some processes and give them to others. This may require saving process state and rolling back to a safe point.

Recovery decisions trade correctness, complexity, and system disruption: killing processes may lose work; preemption and rollback require checkpoint mechanisms.

## Deadlock vs Starvation

- **Deadlock:** A circular wait where none of the processes can proceed.
- **Starvation (Livelock):** A process waits indefinitely because scheduling or resource allocation repeatedly favors others. Starvation can occur without a deadlock.

## Practical Considerations

- Many modern systems prefer avoidance or detection with targeted recovery rather than strict prevention, because prevention can be too restrictive.
- Database systems use locking protocols (e.g., two-phase locking) and deadlock detection with wait-for graphs; they often resolve deadlocks by aborting transactions.
- In distributed systems, detecting and recovering from deadlocks is harder due to lack of a global view and message delays; algorithms such as distributed wait-for graph constructions are used.

## Quick Reference

- To **prevent** deadlock: break at least one Coffman condition (ordering resources, avoiding hold-and-wait, allowing preemption).
- To **avoid** deadlock: use algorithms (like Banker's) that only grant safe allocations.
- To **detect & recover**: detect cycles in allocation/wait-for graphs, then abort or preempt as needed.

## Summary

Deadlock is a fundamental concurrency problem. Understanding the Coffman conditions, detection methods, avoidance algorithms, and recovery strategies enables system designers to choose a pragmatic approach tailored to their application's performance and correctness needs.

## Further Reading

- Silberschatz, Galvin, and Gagne — Operating System Concepts (deadlock chapter)
- Tanenbaum — Modern Operating Systems (deadlock and resource allocation)

