# Multi-threaded Map-Reduce in Rust

A command-line program that demonstrates the map-reduce pattern using native OS threads in Rust. Written for the Operating Systems course (CS 344) at Oregon State University.

## What it does

The program generates a vector of integers, partitions them into chunks, sums each chunk concurrently on its own thread, then reduces the per-thread sums into a final total.

It runs this pipeline twice per execution:
1. **Fixed 2-partition version** — splits the data in half and processes each half on its own thread.
2. **General N-partition version** — splits the data into `num_partitions` roughly equal-sized partitions (any remainder elements are distributed one-per-partition) and spawns one thread per partition to run the map step concurrently.

Each thread's result is collected via `JoinHandle`, then passed to a single-threaded reduce step that sums the intermediate results into the final answer.

## Usage

```
rustc main.rs
./main <num_partitions> <num_elements>
```

**Example:**

```
./main 5 150
```

Generates 150 elements (0 through 149), partitions them into 5 roughly equal chunks, sums each chunk on a separate thread, and prints the intermediate sums and final total.

## What I implemented

The starter code provided the single-threaded version (`generate_data`, `map_data`, `reduce_data`, and a 2-way partition helper). My additions:

- Converted the 2-partition map step to run on two concurrent threads using `thread::spawn`, cloning each partition into its thread closure and joining on the results.
- Implemented `partition_data(num_partitions, v)` to split a vector into `num_partitions` chunks of equal size, distributing any remainder elements so that some partitions get exactly one extra element.
- Wired up the general N-thread version in `main()`: partition into N chunks, spawn one thread per chunk, collect all `JoinHandle` results, and reduce them to a final sum.

## Notes

This was a class assignment focused on learning Rust's threading primitives (`thread::spawn`, `JoinHandle`, ownership/cloning across thread boundaries) rather than production code — there's no error handling beyond basic argument validation, and the partition logic favors clarity over elegance.
