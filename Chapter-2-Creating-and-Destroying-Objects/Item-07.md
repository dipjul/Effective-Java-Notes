# Item 7: Eliminate Obsolete Object References
> ## Key Points:
> Switching from manual memory management to garbage-collected languages like Java might create a false sense that memory management is no longer a concern.
>
> Despite seemingly flawless code, there could be hidden memory leaks. The solution involves nulling out references to eliminate obsolete ones, particularly in self-managed classes.
>
> It's essential to handle potential memory leaks in common scenarios, such as with caches and callbacks, to prevent long-term unnoticed issues.

## Detailed Notes:
- **Introduction**
  - Transition from manual memory management to garbage-collected languages.
  - Objects automatically reclaimed in languages like Java.
  - Misconception: No need to think about memory management.

- **Simple Stack Implementation Example**
  ```java
  public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
      elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
      ensureCapacity();
      elements[size++] = e;
    }

    public Object pop() {
      if (size == 0)
        throw new EmptyStackException();
      return elements[--size];
    }

    private void ensureCapacity() {
      if (elements.length == size)
        elements = Arrays.copyOf(elements, 2 * size + 1);
    }
  }
  ```
  - No obvious issues, but a potential "memory leak" exists.
  - Testing may not reveal the problem immediately.

- **Understanding Memory Leaks**
  - **Definition**
    - "Memory leak" in garbage-collected languages is unintentional object retention.
    - Can result in reduced performance or increased memory footprint.
  - **Identification**
    - Objects popped off the stack may not be garbage collected.
    - Stack maintains obsolete references, causing memory leaks.

- **Fixing the Issue**
  ```java
  public Object pop() {
    if (size == 0)
      throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null; // Eliminate obsolete reference
    return result;
  ```
  - Null out references when they become obsolete.
  - Immediate failure with `NullPointerException` if mistakenly dereferenced.

- **Best Practices for Nulling References**
  - Overcompensation by nulling every reference is unnecessary.
  - Nulling out should be the exception, not the norm.
  - Let variables fall out of scope naturally (Item 57).

- **Understanding Memory Management in the Stack Class**
  - Stack class manages its memory.
  - Elements array: Object reference cells, not the objects.
  - Garbage collector treats all object references equally valid.
  - Programmer's role: Manually null out elements in the inactive portion.

- **Common Sources of Memory Leaks**
  - **Alert for Memory Leaks in Self-Managing Classes**
    - Whenever an element is freed, null out object references in that element.
  - **Caches**
    - Solutions: WeakHashMap for well-defined lifetimes, periodic cleansing for less-defined lifetimes.
    - LinkedHashMap's `removeEldestEntry` for cache entry management.
    - Use java.lang.ref for sophisticated caches.

  - **Listeners and Callbacks**
    - Accumulation of callbacks if not deregistered explicitly.
    - Ensure garbage collection by storing only weak references, e.g., WeakHashMap keys.

- **Detecting and Preventing Memory Leaks**
  - Memory leaks may persist for years without obvious failures.
  - Discovered through careful code inspection or heap profiler.
  - Desirable: Anticipate and prevent memory leak problems.
