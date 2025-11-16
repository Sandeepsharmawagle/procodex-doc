# Array

## Overview

An array is a linear data structure that stores a collection of elements (values) of the same type in contiguous memory locations. Arrays provide indexed access to elements and are one of the most fundamental and widely used data structures in programming and algorithms.

## Types

- One-dimensional arrays: a simple list of elements accessed by a single index.
- Multi-dimensional arrays: arrays of arrays (e.g., 2D matrices) accessed by multiple indices.
- Dynamic arrays (resizable): abstract arrays that can grow/shrink at runtime (examples: `ArrayList` in Java, `vector` in C++, Python lists under the hood).
- Sparse arrays: optimized representations for arrays where most elements are empty or zero.

## Properties

- **Contiguous memory layout:** Elements are stored in consecutive memory addresses. This enables constant-time index computation.
- **Fixed element type:** All elements typically share the same type (in statically-typed languages).
- **Fixed or dynamic size:** Static arrays have fixed size determined at allocation; dynamic arrays resize when needed.
- **Indexing:** Direct access to the i-th element via an index (zero-based in most languages).

## Core Operations and Time Complexity

- **Access (read/write by index):** O(1) — compute base address + index offset.
- **Traversal:** O(n) — visiting all elements.
- **Search (unsorted):** O(n) — linear search.
- **Search (sorted):** O(log n) — binary search (requires random access).
- **Insert/Delete at end (dynamic arrays):** Amortized O(1) for append; worst-case O(n) when resizing occurs.
- **Insert/Delete at arbitrary position:** O(n) — may require shifting elements.

Notes:
- Dynamic arrays use geometric resizing (e.g., double capacity) to keep amortized insertion cost low.

## Memory Layout and Indexing

If `A` is an array with base address `B` and each element has size `s`, then element `A[i]` is at address `B + i * s`. For multi-dimensional arrays, languages use either row-major or column-major ordering to map multiple indices into linear memory.

## Implementation Variants

- **Static array:** Allocated with fixed size (stack or static memory). Fast and simple but inflexible.
- **Heap-allocated array:** Allocated at runtime on the heap; may still be fixed-size but allows larger allocations.
- **Resizable/dynamic array:** Maintains capacity and size; when size reaches capacity, allocate a larger block and copy existing elements.

Pseudo-steps for dynamic array append:

1. If `size < capacity`, place element at `data[size++]`.
2. Else, allocate new buffer (commonly `capacity * 2`), copy elements, free old buffer, then insert.

## Practical Examples

- C (static array): `int a[10];` — contiguous block of 10 integers.
- C dynamic (heap): `int *a = malloc(n * sizeof(int));` — manages memory manually.
- C++: `std::vector<int>` — dynamic array with amortized O(1) push_back.
- Java: `int[] arr = new int[n];` and `ArrayList<Integer>` for dynamic behavior.
- Python: `list` is a dynamic array under the hood (amortized resizing).

## Use Cases

- When you need constant-time random access by index.
- Implementing other data structures (hash tables, heaps, strings, matrices).
- Cache-friendly processing and numerical computing (arrays map well to CPU caches and vectorized operations).

## Pitfalls and Considerations

- **Out-of-bounds access:** Accessing indices < 0 or >= size causes undefined behavior (in low-level languages) or exceptions.
- **Resizing cost:** Reallocations can be expensive; choose capacity growth factors deliberately.
- **Memory vs speed trade-offs:** Large contiguous blocks may be hard to allocate in memory-constrained environments.
- **Copying overhead:** When resizing, copying elements can be costly for large objects.

## Arrays vs Linked Lists

- **Arrays:** O(1) random access, O(n) insert/delete at arbitrary positions, contiguous memory.
- **Linked lists:** O(n) access by index, O(1) insert/delete given a node reference, non-contiguous memory.

Choose arrays when you need fast indexed access and predictable memory layout; choose linked lists when frequent insertions/removals in the middle matter and random access is not required.

## Common Interview/Practice Problems

- Two-sum / sliding window techniques
- Merge intervals / merge sorted arrays
- Rotate array / reverse subarray
- Matrix traversal and manipulation (2D arrays)

## Summary

Arrays are a core data structure with simple semantics and powerful performance characteristics. Understanding arrays, their variants, and trade-offs is essential for writing efficient programs and implementing higher-level data structures.

## References

- Cormen, Leiserson, Rivest, Stein — Introduction to Algorithms
- Good introductory language texts or docs (C, C++, Java, Python) for language-specific array behavior

