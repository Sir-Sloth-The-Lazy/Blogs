---
title: "Memory Management"
description: "How operating systems manage memory — virtual memory, paging, segmentation, and the memory hierarchy."
date: 2025-03-01
subject: "Systems"
order: 1
---

## The Memory Problem

Every running process needs memory. It needs memory for its code, its data, its stack, and its heap allocations. But physical RAM is finite, and multiple processes compete for it simultaneously.

The operating system's **memory manager** solves this problem by abstracting physical memory into a uniform, isolated address space for each process.

## Virtual Memory

Virtual memory creates the illusion that each process has its own private, contiguous address space. In reality, the process's memory is mapped to scattered physical locations through a **page table**.

### Address Translation

When a program accesses memory at a virtual address, the hardware's **Memory Management Unit (MMU)** translates it to a physical address:

```
Virtual Address → Page Table → Physical Address
     0x4000    →   PTE #4   →    0x7A000
```

This translation happens on every memory access, so it must be extremely fast. The **Translation Lookaside Buffer (TLB)** — a small cache inside the CPU — stores recent translations to avoid costly page table lookups.

### Page Faults

When a process accesses a page that isn't currently in physical memory, a **page fault** occurs. The OS then:

1. Suspends the process
2. Locates the page on disk (swap space)
3. Finds a free physical frame (evicting another page if necessary)
4. Loads the page into the frame
5. Updates the page table
6. Resumes the process

This mechanism enables processes to use more memory than physically available — the OS transparently moves pages between RAM and disk.

## Paging

Paging divides both virtual and physical memory into fixed-size blocks:

- **Pages**: fixed-size blocks of virtual memory (typically 4 KB)
- **Frames**: fixed-size blocks of physical memory (same size as pages)

### Page Table Structure

A flat page table for a 32-bit address space with 4 KB pages requires 2²⁰ entries. For 64-bit systems, this is impractical. Modern systems use **multi-level page tables**:

```
Level 4 (PML4)  →  Level 3 (PDPT)  →  Level 2 (PD)  →  Level 1 (PT)
   9 bits            9 bits             9 bits            9 bits
```

Each level contains pointers to the next, and unused regions don't need their lower-level tables allocated at all.

### Page Replacement Algorithms

When physical memory is full and a new page must be loaded, the OS must choose a victim page to evict:

- **LRU (Least Recently Used)**: Evict the page that hasn't been accessed for the longest time
- **FIFO (First In, First Out)**: Evict the oldest page
- **Clock Algorithm**: An approximation of LRU using reference bits
- **Working Set**: Keep pages that are part of the process's current working set

## The Memory Hierarchy

Modern systems have multiple levels of memory, each trading capacity for speed:

| Level | Type | Latency | Typical Size |
|-------|------|---------|--------------|
| L1 Cache | SRAM | ~1 ns | 64 KB |
| L2 Cache | SRAM | ~4 ns | 256 KB |
| L3 Cache | SRAM | ~12 ns | 8 MB |
| RAM | DRAM | ~100 ns | 16 GB |
| SSD | Flash | ~100 μs | 512 GB |
| HDD | Magnetic | ~10 ms | 2 TB |

### Locality of Reference

The memory hierarchy works because programs exhibit **locality**:

- **Temporal locality**: Recently accessed data is likely to be accessed again soon
- **Spatial locality**: Data near recently accessed locations is likely to be accessed soon

Cache-friendly code that respects these principles can be orders of magnitude faster than code that doesn't.

## Summary

Memory management is one of the most critical responsibilities of an operating system. Virtual memory provides isolation and flexibility, paging enables efficient physical memory usage, and understanding the memory hierarchy is essential for writing performant software.
