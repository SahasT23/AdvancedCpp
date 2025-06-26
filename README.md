# AdvancedCpp

Memory Management


2.1 Overview

Effective memory management in C++ is crucial for preventing leaks, undefined behaviour, and perfor-
mance degradation, especially in complex applications.

2.2 Key Concepts

• Stack vs. Heap Allocation:

– Stack: Automatic allocation for local variables; fast but limited (typically 1–8 MB). Stack overflow

occurs with excessive recursion or large objects.

– Heap: Dynamic allocation via new, delete; slower due to manual management, prone to fragmen-

tation. Use std::pmr::memory_resource for polymorphic allocators.

– Strategies: Prefer stack for small, short-lived objects; use heap for large or persistent data.

• Smart Pointers:

– std::unique_ptr: Exclusive ownership, non-copyable, movable; enforces RAII for deterministic

cleanup.

– std::shared_ptr: Reference-counted shared ownership; atomic counter updates ensure thread

safety.

– std::weak_ptr: Non-owning reference to shared_ptr; breaks cyclic dependencies.

– Custom deleters: Use with std::unique_ptr or std::shared_ptr for resource-specific cleanup (e.g., file
handles).

• Memory Alignment:

– Use alignas to align data for SIMD operations or hardware requirements.

– Check alignment with alignof; misaligned access causes performance penalties or crashes on some

architectures.

• Custom Allocators:

– Implement std::allocator-compatible allocators for containers like std::vector.

– Example: Stack-based allocators for temporary buffers; arena allocators for object pools.


– Use std::pmr for polymorphic memory resources in C++17+.

• Memory Fragmentation:

– External fragmentation: Mitigate with slab allocators.

– Internal fragmentation: Minimise with small-object optimisations.

2.3 Pointer Types and Issues

• Raw Pointers (T*): Direct memory access; no ownership semantics. Risks include dangling pointers,

null dereferences, and memory leaks.

• Const Pointers:

– const T*: Pointer to constant data; data cannot be modified.

– T* const: Constant pointer; pointer cannot be reassigned.

– const T* const: Both pointer and data are constant.

• Function Pointers: Syntax: void (*func)(int); used for callbacks or dynamic dispatch. Risks: Type mismatches, null dereferences.

• Void Pointers (void*): Generic pointers; require explicit casting. Risks: Type safety violations, align-
ment issues.

2

• Pointer to Pointer (T**): Used in dynamic 2D arrays or passing pointers by reference. Risks: Complex lifetime management, double-free errors.

• Member Pointers: Point to class members (data or functions); syntax: int T::*. Used in reflection-like systems. Risks: Invalid object access.

• Array Pointers (T(*)[]): Distinct from pointer-to-array; used in C-style APIs. Risks: Buffer overflows,
decay to T*.

• Smart Pointer Variants:

– std :: unique_ptr < T [] >: Array-specialised unique pointer; uses delete[].

– std::shared_ptr with aliasing constructor: Shares ownership of subobjects.

• Pointer Arithmetic: Common in low-level code; risks include out-of-bounds access, undefined be-
haviour. Use std::span for safer bounds checking.

• Common Issues:

– Dangling pointers: Accessing freed memory.

– Null dereferences: Accessing nullptr or uninitialised pointers.

– Double-free: Deallocating memory twice, causing undefined behaviour.

– Memory leaks: Forgetting to deallocate heap memory.

– Type punning: Reinterpreting memory via void* or reinterpret_cast, violating strict aliasing rules.

3 C++ Compiler Mechanics

3.1 Compilation Pipeline

• Preprocessing: Expands macros (#define), includes headers (#include), and processes conditionals
(#ifdef). Generates preprocessed source.

• Lexical Analysis: Tokenises source code into identifiers, keywords, and operators.

• Parsing: Builds abstract syntax tree (AST) from tokens; validates syntax.

• Semantic Analysis: Checks type correctness, resolves overloads, and enforces language rules (e.g.,
access specifiers).

• Code Generation: Translates AST to intermediate representation (IR), e.g., LLVM IR, then to target-
specific assembly.

• Optimisation: Applies transformations like constant propagation, loop invariant code motion, and
dead code elimination.

• Linking: Combines object files, resolves external symbols, and produces executable. Static vs. dynamic
linking affects runtime performance.

3.2 Optimisation Techniques

• Inline Expansion: Replaces function calls with function body; use inline or rely on compiler heuristics.

• Loop Optimisations: Unrolling, vectorisation (SIMD), and fusion to enhance cache locality and parallelism.

• Constexpr Evaluation: Use constexpr for compile-time computations; extend with consteval (C++20) for guaranteed compile-time execution.

• Move Semantics: Use std::move and rvalue references to eliminate unnecessary copies.

• Profile-Guided Optimisation (PGO): Collect runtime data to optimise hot paths.

• Link-Time Optimisation (LTO): Whole-program analysis during linking for cross-module inlining.

• Branch Prediction: Minimise branches with [[likely]] and [[unlikely]] (C++20).

• Cache Optimisation: Align data structures; use std::hardware_destructive_interference_size for cache-line awareness.
3


4 C++ Standard Library

4.1 Overview

The C++ Standard Library provides a rich set of tools for efficient programming, including containers, algorithms, utilities, and more. Below is an exhaustive exploration of key components and their advanced usage.

4.2 Containers

• Sequence Containers:

– std::vector: Dynamic array; O(1) random access, O(n) insertion/deletion at middle. Use reserve to
preallocate capacity.

– std::deque: Double-ended queue; O(1) push/pop at ends, amortised O(1) random access.

– std::list: Doubly-linked list; O(1) insertion/deletion, O(n) random access. Use for frequent modifica-
tions.

– std::forward_list: Singly-linked list; minimal memory overhead, no reverse iteration.

– std::array: Fixed-size array; stack-allocated, O(1) access, no dynamic resizing.

• Associative Containers:

– std::set/std::multiset: Sorted unique/multiple keys; O(log n) operations via red-black trees.

– std::map/std::multimap: Sorted key-value pairs; O(log n) operations.

– std::unordered_set/std::unordered_map: Hash-based; O(1) average-case operations. Custom hash
functions via std::hash.

• Container Adaptors:

– std::stack: LIFO; backed by std::deque or std::vector.

– std::queue: FIFO; similar backing.

– std::priority_queue: Heap-based; O(log n) push/pop, O(1) top.

4.3 Algorithms

• Sorting: std::sort, std::stable_sort, std::partial_sort; custom comparators for flexibility.

• Searching: std::binary_search, std::lower_bound, std::upper_bound for sorted ranges; std::find for unsorted.

• Transformations: std::transform, std::copy, std::move for range operations.

• Numeric: std::accumulate, std::inner_product, std::partial_sum for mathematical operations.

• Parallel Execution: Use std::execution::par or std::execution::par_unseq (C++17) for parallel algorithms.

4.4 Iterators

• Types: Input, output, forward, bidirectional, random-access, contiguous (C++20).

• Utilities: std::begin/std::end, std::next, std::prev, std::distance.

• Ranges (C++20): Use std::ranges::sort, std::ranges::views::filter for expressive, safe range operations.

4.5 Memory Utilities

• std::allocator: Custom memory allocation for containers.

• std::make_unique/std::make_shared: Safe, exception-safe smart pointer creation.

• std::pmr::memory_resource: Polymorphic allocators for flexible memory management (C++17).

4

4.6 String Handling

• std::string: Dynamic string; supports concatenation, substring, and searching.

• std::string_view: Non-owning string view; O(1) substring operations, ideal for parsing.

• std::to_string/std::from_string: Numeric-string conversions (C++20).

• std::format: Type-safe string formatting (C++20); e.g., std::format("Value: ", x).

4.7 Utilities

• std::optional: Represents optional values; avoids null checks.

• std::variant: Type-safe union; supports visitor pattern.

• std::any: Type-safe container for any type; useful for dynamic typing.

• std::bitset: Fixed-size bit manipulation; efficient for flags or bitmasks.

• std::chrono: Time utilities; e.g., std::chrono::steady_clock for monotonic time.

• std::filesystem: File system operations (C++17); e.g., std::filesystem::path.

4.8 Usage Guidelines

• Prefer std::string_view for read-only string operations to avoid copies.

• Use std::ranges for modern, expressive algorithms.

• Leverage std::optional/std::variant for robust type handling.

• Minimise allocator overhead with std::pmr::monotonic_buffer_resource for temporary allocations.

5 Multithreading and Concurrency

5.1 Core Concepts

• Threads: std::thread for independent execution; manage join/detach carefully.

• Mutexes: std::mutex, std::recursive_mutex; use std::lock_guard or std::unique_lock for RAII.

• Condition Variables: std::condition_variable for thread synchronisation; use with std::unique_lock.

• Atomic Operations: std::atomic for lock-free operations; supports std::memory_order for fine-grain control.

• Thread Pools: Implement via std::async or custom pools for task distribution.

5.2 Advanced Techniques

• Lock-Free Programming: Use std::atomic with compare_exchange_strong for wait-free algorithms.

• Futures and Promises: std::future/std::promise for asynchronous results; std::shared_future for multi-
ple consumers.

• Parallel Algorithms: std::execution::par for parallel std::sort, std::transform, etc.

• Read-Write Locks: std::shared_mutex for concurrent reads, exclusive writes.

• Task-Based Parallelism: Use std::packaged_task for flexible task scheduling.

5.3 Common Pitfalls

• Deadlocks: Prevent with lock ordering or std::scoped_lock.

• Race Conditions: Use std::atomic or mutexes for shared data.

• Thread Overhead: Reuse threads via pools to avoid creation/destruction costs.

• False Sharing: Pad data structures to avoid cache-line contention.
5

6 Object-Oriented Programming (Advanced)

6.1 Overview

Advanced OOP in C++ leverages language features to create flexible, maintainable, and high-performance
systems. This section explores sophisticated techniques for designing robust class hierarchies and
managing object interactions.

6.2 Core Principles

• Encapsulation: Restrict access via private/protected; expose interfaces via public methods or getters/setters.

• Inheritance: Use for “is-a” relationships; prefer composition for “has-a” relationships.

• Polymorphism: Achieve via virtual functions and interfaces; ensure proper destructor virtualisation.

• Abstraction: Define abstract base classes with pure virtual functions (virtual void func() = 0).

6.3 Advanced OOP Techniques

• CRTP (Curiously Recurring Template Pattern):

– Structure: template<typename Derived> class Base ; class Derived : Base<Derived> ;.

– Use Case: Static polymorphism; e.g., compile-time method dispatching without virtual function
overhead.

– Example: Type-safe method chaining or static interfaces.

– Advantages: Zero runtime cost; enables compile-time type checking.

– Pitfalls: Complex syntax; requires careful design to avoid code bloat.

• Mixin Pattern:

– Structure: Combine small, reusable classes via multiple inheritance or CRTP.

– Use Case: Add orthogonal functionality (e.g., logging, serialisation) to classes.

– Example: class LoggerMixin protected: void log() ; class MyClass : public Base, public LoggerMixin;.

– Advantages: Modular design; avoids deep inheritance hierarchies.

– Pitfalls: Diamond problem in multiple inheritance; use virtual inheritance sparingly.

• Virtual Function Optimisation:

– Use final to prevent overriding; enables devirtualisation.

– Use override to ensure correct override semantics.

– Avoid deep virtual hierarchies to minimise vtable overhead.

– Consider std::function or std::variant for type erasure instead of virtual functions.

• Type Erasure:

– Structure: Hide concrete types behind a uniform interface; e.g., std::any or custom type-erased
wrappers.

– Use Case: Heterogeneous containers (e.g., storing different shapes in a std::vector).

– Example: class Shape virtual void draw() = 0; ; struct AnyShape std::unique_ptr<Shape> ptr; ;.

– Advantages: Flexibility; decouples interface from implementation.

– Pitfalls: Heap allocation overhead; requires careful lifetime management.

• Policy-Based Design:

– Structure: Use template parameters to inject behaviour; e.g., template<typename Policy> class
Object ;.

6

– Use Case: Customise class behaviour (e.g., memory allocation, threading model) at compile time.

– Example: template<typename Alloc> class Container ; using MyContainer = Container<std::allocator<int»;.

– Advantages: High flexibility; zero runtime cost.

– Pitfalls: Increased compile time; complex error messages.

• Resource Management with RAII:

– Structure: Tie resource lifetime to object scope; e.g., std::unique_ptr, std::lock_guard.

– Use Case: File handles, mutexes, memory, network sockets.

– Example: class File std::unique_ptr<FILE> handle; public: File(const char* path) : handle(fopen(path,"r")) ;.

– Advantages: Exception-safe; deterministic cleanup.

– Pitfalls: Ensure copy/move semantics are correct (e.g., rule of five).

• Factory Patterns:

– Structure: Abstract factory, factory method, or static factory for object creation.

– Use Case: Decouple object creation from usage; support dependency injection.

– Example: class Factory public: std::unique_ptr<Base> create(); ;.

– Advantages: Encapsulates creation logic; supports polymorphism.

– Pitfalls: Can introduce complexity; ensure thread safety if shared.

• Visitor Pattern:

– Structure: Double dispatch to separate algorithms from object structure.

– Use Case: Traverse complex object hierarchies (e.g., AST in compilers).

– Example: class Visitor virtual void visit(Node1) = 0; ; class Node virtual void accept(Visitor) = 0; ;.

– Advantages: Extensible operations without modifying classes.

– Pitfalls: Invasive; requires modifying base classes to add new types.

• Concepts (C++20):

– Structure: Define type constraints; e.g., template<typename T> concept Numeric = std::is_arithmetic_v<T>;.

– Use Case: Restrict template parameters for better error messages and type safety.

– Example: template<Numeric T> T add(T a, T b) return a + b; ;.

– Advantages: Clearer interfaces; compile-time checks.

– Pitfalls: Requires C++20; learning curve for complex constraints.

6.4 OOP Design Patterns

• Singleton: Ensure single instance; use std::call_once for thread-safe initialisation.

• Observer: Event-driven systems; use std::function for callbacks.

• Strategy: Runtime behaviour selection; implement via interfaces or std::function.

• Decorator: Dynamically add responsibilities; use composition or inheritance.

• Bridge: Decouple abstraction from implementation; useful for cross-platform code.

6.5 OOP Best Practices

• Adhere to SOLID principles: Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion.

• Minimise inheritance depth; prefer composition for flexibility.

• Use const methods and noexcept for performance and safety.

7

• Implement rule of five for resource-owning classes.

• Use std::enable_if or concepts for SFINAE-based constraints.

• Profile virtual function performance; consider CRTP or final classes for hot paths.

7 Advanced Data Structures and Applications

7.1 Overview

Advanced data structures are critical for optimising specific use cases. This section details their implementation and practical applications.

7.2 Data Structures and Use Cases

• B-Trees:

– Structure: Balanced tree with multiple keys per node; order m defines node capacity.

– Application: Database indexing (e.g., MySQL); file systems (e.g., NTFS).

– Operations: O(log n) search, insert, delete; minimises disk I/O.

– Implementation: Use std::vector for node storage; balance during splits/merges.

• Trie (Prefix Tree):

– Structure: Tree where nodes represent characters; paths form strings.

– Application: Autocomplete, spell checkers, IP routing.

– Operations: O(m) search/insert/delete, where m is key length.

– Implementation: Use std::unordered_map for children; compress paths for efficiency.

• Fenwick Tree (Binary Indexed Tree):

– Structure: Array-based tree for cumulative queries.

– Application: Range sum queries, real-time analytics.

– Operations: O(log n) update/query; compact memory.

– Implementation: Use bit manipulation for tree traversal; extend for range updates.

• Segment Tree:

– Structure: Binary tree for range queries/updates.

– Application: Range minimum/maximum, computational geometry.

– Operations: O(log n) query/update; lazy propagation for range updates.

– Implementation: Use recursive structure; store function objects for custom operations.

• Disjoint Set (Union-Find):

– Structure: Forest with path compression, union by rank.

– Application: Graph connectivity, Kruskal’s MST algorithm.

– Operations: Near O(1) with optimisations (inverse Ackermann).

– Implementation: Use std::vector for parent/rank arrays.

• Suffix Array:

– Structure: Sorted array of string suffixes.

– Application: String matching, bioinformatics (DNA analysis).

– Operations: O(n log n) construction; O(log n) searches with LCP.

– Implementation: Use DC3 algorithm; pair with LCP array.

• Skip List:

8

– Structure: Probabilistic layered linked list; mimics balanced trees.

– Application: Concurrent key-value stores, lock-free data structures.

– Operations: O(log n) average-case search/insert/delete.

– Implementation: Use random levels; ensure thread safety with atomics.

7.3 Implementation Considerations

• Memory Efficiency: Use memory pools or std::pmr for large structures.

• Thread Safety: Implement lock-free versions (e.g., concurrent tries) using std::atomic.

• Cache Optimisation: Ensure contiguous memory access; align nodes to cache lines.

• Generic Programming: Use templates for type-agnostic structures; constrain with concepts.

7.4 Practical Applications

• Database Systems: B-trees for indexing; suffix arrays for full-text search.

• Real-Time Systems: Segment trees for dynamic queries; Fenwick trees for analytics.

• Networking: Tries for IP routing; disjoint sets for network clustering.

• Game Development: Quadtrees for spatial partitioning; A* with heaps for pathfinding.

• Bioinformatics: Suffix arrays for sequence alignment; tries for pattern matching.

Need to add makefiles and more on cache and compiler optimisation. 

