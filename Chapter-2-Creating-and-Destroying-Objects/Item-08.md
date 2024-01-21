# Item 8: Avoid Finalizers and Cleaners

> ## Key Points:
> Advice against utilizing finalizers and cleaners in Java due to their unpredictability, security risks, and performance drawbacks. 
> Finalizers, deprecated in Java 9, are replaced by cleaners, which remain unpredictable and slow. 
> The recommendation is to avoid these mechanisms and instead implement AutoCloseable for resource management, emphasizing the unpredictability and performance implications of finalizers and cleaners.

## Detailed Notes
*   **Finalizers**
    
    *   Unpredictable, dangerous, and generally unnecessary.
    *   Deprecated in Java 9; still used in Java libraries.
*   **Cleaners (Java 9 Replacement)**
    
    *   Less dangerous than finalizers but still unpredictable and slow.
*   **Comparison with C++**
    
    *   Finalizers/cleaners ≠ C++ destructors.
    *   Java uses garbage collector; C++ uses destructors for resource reclamation.
*   **Tardy Execution**
    
    *   No guarantee of prompt execution of finalizers or cleaners.
    *   Avoid time-critical operations in finalizers/cleaners.
*   **Performance Impact**
    
    *   Severe penalty; finalizers are about 50 times slower than try-with-resources.
*   **Garbage Collection Variability**
    
    *   Execution depends on garbage collection algorithm, leading to varying behaviors.
*   **Case Study: Tardy Finalization**
    
    *   Long-running GUI application experienced OutOfMemoryError due to delayed finalization.
    *   Finalizer thread at lower priority caused objects not to finalize promptly.
*   **No Guarantees on Execution**
    
    *   No guarantee that finalizers or cleaners will run promptly or at all.
    *   Never depend on them to update persistent state.
*   **Avoid System.gc and System.runFinalization**
    
    *   Do not guarantee execution; deprecated methods like `runFinalizersOnExit`.
*   **Uncaught Exceptions**
    
    *   Finalizers ignore uncaught exceptions during finalization.
    *   Cleaners, controlled by library, do not have this issue.
*   **Performance Benchmark**
    
    *   Finalizers and cleaners have a severe performance penalty.
    *   Example: Creating/destroying objects with finalizers is about 50 times slower.
*   **Security Concerns**
    
    *   Finalizers pose a security risk with finalizer attacks.
    *   Final classes immune; nonfinal classes need a do-nothing final `finalize` method.
*   **Alternative: Implement AutoCloseable**
    
    *   Instead of finalizers or cleaners, implement `AutoCloseable` for resource encapsulation.
    *   Clients invoke `close` method using try-with-resources for proper termination.
*   **Legitimate Uses of Cleaners**
    
    *   Act as a safety net if clients neglect to call `close`.
    *   Objects with native peers, but performance and resource considerations apply.
*   **Example: Using Cleaners**
    
    *   `Room` class demonstrates cleaner as a safety net for resource cleanup.
```java
public class Room implements AutoCloseable {
    private static final Cleaner cleaner = Cleaner.create();
    private static class State implements Runnable {
        int numJunkPiles;
        State(int numJunkPiles) {
            this.numJunkPiles = numJunkPiles;
        }
        @Override
        public void run() {
            System.out.println("Cleaning room");
            numJunkPiles = 0;
        }
    }
    private final State state;
    private final Cleaner.Cleanable cleanable;
    public Room(int numJunkPiles) {
        state = new State(numJunkPiles);
        cleanable = cleaner.register(this, state);
    }
    @Override
    public void close() {
        cleanable.clean();
    }
}
```
*   **Cleaner Usage Notes**
    
    *   `State` must not refer to its `Room` instance to avoid circular references.
    *   Cleaners used as safety nets; try-with-resources eliminates the need for automatic cleaning.
*   **Client Behavior**
    
    *   Well-behaved client using try-with-resources demonstrates proper cleaning.
```java
public class Adult {
    public static void main(String[] args) {
        try (Room myRoom = new Room(7)) {
            System.out.println("Goodbye");
        }
    }
}
```
*   **Unpredictable Behavior**
    *   Ill-behaved client may not trigger cleaning due to the unpredictability of cleaners.

```java
public class Teenager {
    public static void main(String[] args) {
        new Room(99);
        System.out.println("Peace out");
    }
}
```

*   **Conclusion**
    *   Avoid cleaners and, in pre-Java 9 releases, finalizers, unless for safety nets or noncritical native resource termination.
    *   Consider the unpredictability and performance consequences.
