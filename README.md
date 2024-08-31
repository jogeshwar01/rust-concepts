


# 📚 Rust Concepts

Reference - [Crust of Rust ***by Jon Gjengset***](https://www.youtube.com/watch?v=rAl-9HwD858&list=PLqbS7AVVErFiWDOAVrPt7aYmnuuOLYvOa)

## 🚀 Async/Await

- **`async`/`await`**
- **`select!`**
- **`tokio::spawn_blocking`** - When dealing with expensive operations.
- **Fuse Futures** - Safely pull completed futures.

### Futures and Concurrency

- **`join`** - Wait for multiple futures concurrently.
- **`try_future`** - A future whose output is a `Result`.
- **`tokio::spawn`** - Passes another static future to the executor (runtime).
- **Tracing** - Helps log errors in threads/future spawns.

### Sharing Across Spawns

- **`Arc<Mutex<T>>`** - Used for safe sharing across spawns.

### Miscellaneous

- **State Machine Type** - Implementing `Future`.
- **Async Function in Traits** - Doesn't work by default, use `async_trait` crate.
- **Async Mutexes vs Standard Mutexes** - If the critical section is short and has no await points, use standard mutexes; they are faster. Use async mutexes only if deadlock is possible.
- **Thread vs `tokio::spawn`** - Threads aren't cooperatively scheduled.
- **Compute-Heavy Tasks** - Use `rayon` instead of futures for compute-heavy tasks.
- **No IO in Program** - No real reason to use async if your program doesn't do IO.

## 🔧 Functions, Closures, and Traits

- **Function Items vs Function Pointers**
- **`Fn`, `FnMut`, `FnOnce`** - Why do we need these three? Closures.
- **Closure Environment** - Use `move` in a closure to move references into it. If the variable's lifetime ends, the closure won't.
- **Borrowing in Closures** - Use a reference like `&x`.
- **Box<dyn Fn()>**
- **`const fn`**

## 🔄 Iterators

- **Iterator** - A trait.
- **`while let Some(e) = iter.next()`** - Loop while the iterator yields `Some`.
- **`into_iter()`**

### Iterator Methods

1. **`flatten`** - Recurses only one level.
2. **`count()`** - Get the length.
3. **Check** - `and_then()`.
4. **DoubleEndedIterator**
    - **`rev()`**
    - **`next_back()`**

## 🔒 Smart Pointers and Interior Mutability

- **Box** - Allocates on the heap.
- **Rc** - Reference counting, not thread-safe.
- **Arc** - Atomic Rc, thread-safe.

### Interior Mutability

1. **Cell**
   - Can never get a reference to the value inside the cell.
   - `Cell` doesn’t implement `Sync`, so it cannot be shared across threads.
2. **RefCell**
   - Mutable memory location with safe, dynamically checked (runtime) borrow rules.
   - Not `Sync`, similar to `Cell`.
3. **Rc**
   - Never provides mutability, only multiple shared references.
   - Not thread-safe.


- **`Mutex`** - Similar to `RefCell` but only has `borrow_mut`. Thread-safe.
- **`Arc`** - Thread-safe `Rc`.
- **`Cow`** - Clone on write.

## 📬 Channels

- **Condvar** - Conditional variable used to notify receiver threads to wake up when a send happens.
- **`std::collections::VecDeque`** - A double-ended queue.
- **`sync_channel`** - `SynSender`, sends can block.

### Channel Flavors

1. **Sync/Bounded** - Sends can block, limited capacity.
2. **Async/Unbounded** - Sends cannot block, unlimited capacity.
3. **Rendezvous** - Sync with capacity = 0. Used for thread synchronization.
4. **Oneshot** - In practice, only one call to `send()` is allowed.

## 🔖 Lifetime Annotations

- **Anonymous Lifetimes**
- **`'static`** - Lives for the entire duration of the program.
- **`.collect()`** - Collect iterator to `Vec`.
- **`.take()`** - Takes the value out of the `Option`, leaving a `None` in its place.

### Lifetime Details

- **`str` vs `String`**
   - `str` is similar to `[char]`, can be anywhere: stack, heap, or static memory.
   - `&str` stores the start of a string and its length.
   - `String` is similar to `Vec<char>`, heap-allocated, and dynamically expandable/contractible.
   - Conversion between `String` and `&str` involves memory operations.

- **Iterate Over Chars in `&str`**
   - Efficiently iterate and map using `.char_indices().find()`.

## 🛠 Declarative Macros

- **`cargo install cargo-expand`**
- **`cargo expand`**
- **Macro Identifiers** - `$x: ident` to access identifiers in macros.
- **Macro Syntax** - Use `{{ }}`, `()`, or `[]` for macro bodies.

### Additional Notes

- **`macro_rules!`** - Procedural macro, not declarative.
- **`#[macro_export]`** - Exports macros, similar to `pub` for functions.

## 📏 Dispatch and Fat Pointers

- **Monomorphization** - Compile code for each type in generics.
- **Static Dispatch** - Use `impl Trait` or `where T: Trait`.
- **Trait Objects** - Dynamic dispatch using `Box<dyn Trait>` or `&dyn Trait`.

### Sized Trait

- **`Sized` Trait** - Assumed for all types by default. The compiler needs to know all sizes to generate efficient machine code.

### Fat Pointers

- **Vtable** - Virtual dispatch table.
- **Trait Object Limitations** - Multiple traits, associated types, static trait methods, etc.

## 🗂 Collections

### 1. `Vec`

- **`Vec`** - A dynamically resizable array.
  - **Length** - The current number of elements.
  - **Capacity** - The number of elements that can be stored without reallocating.
  - **`with_capacity(capacity: usize)`** - Pre-allocate memory.

### 2. `VecDeque`

- **`VecDeque`** - A double-ended queue, also known as a ring buffer.
  - Acts as a stack or queue.
  - More efficient than `Vec` when inserting at the start, but less efficient overall.
  - Cannot be converted directly into a slice.

### 3. `LinkedList`

- **`LinkedList`** - A doubly linked list.
  - Can't modify `prev` and `next` pointers directly.

### 4. Set Types

- **`HashSet` and `BTreeSet`**
  - Essentially just keys of a map with zero-sized values.
  - Similar to keys of `HashMap` and `BTreeMap`.

### 5. Maps

- **`HashMap` & `BTreeMap`**
  - **HashMap** - Efficient for operations but requires keys to be hashable. No order.
  - **BTreeMap** - Does not require keys to be hashable, but they must be orderable. Keys are sorted.

### 6. Binary Heap

- **`BinaryHeap`** - A max-heap, also known as a priority queue.
  - Efficient compared to `BTreeMap` or `BTreeSet` for certain operations.
  - Supports both max and min heaps.

### 7. `Option` and `Result`

- **Option and Result**
  - **Option** - Represents an optional value.
  - **Result** - Represents success or failure.

### Additional Crates

- **Crossbeam Crate** - For concurrent data structures.
- **Flurry/EvMap** - For concurrent hashmaps.
- **Hashbrown** - Another efficient hashmap implementation.
- **IndexMap** - Maintains insertion order of keys.

