# Item 3:  Enforce the singleton property with a private constructor or an enum type 
A singleton is simply a class that is instantiated exactly once.

> ## **Key Points**:
>
> 1. Singleton is a design pattern that ensures a class has only one instance. It can be implemented using a public final field, a static factory method, or an enum type. 
> 2. The public final field and static factory methods are traditional approaches, with the former being simpler and clearer, and the latter offering more flexibility. Serialization of a singleton requires declaring all instance fields transient and providing a readResolve method.
> 3. The enum type approach is preferred for implementing singletons as it is concise, thread-safe, provides serialization machinery, and guarantees against multiple instantiations.

## Detailed Notes:
### Public Final Field Approach:
**1. Singleton Declaration:**

  ```java
  public class Elvis {
      public static final Elvis INSTANCE = new Elvis();
      private Elvis() { ... }
      public void leaveTheBuilding() { ... }
  }
  ```
**2. Advantages:**
- Clear API indication of being a singleton (public static field is final).
- Simplicity in implementation.

**3. Caveat:**
- Reflective access to private constructor can potentially create a second instance.
- *Suggested defense:* Modify the constructor to throw an exception if a second instance is created.

### Static Factory Approach:
**1. Singleton Declaration:**

```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getInstance() { return INSTANCE; }
    public void leaveTheBuilding() { ... }
}
```

**2. Advantages:**
- Flexibility to change the class's singularity without altering API.
- Allows a generic singleton factory and method reference as a supplier.

### Serialization:
1. Ensure Singleton Guarantee:
   - To make a singleton class serializable, declare all instance fields transient.
2. Provide `readResolve` Method:
  ```java
  private Object readResolve() {
      // Return the one true Elvis and let the garbage collector
      // take care of the Elvis impersonator.
      return INSTANCE;
  }
  ```
  - This prevents the creation of new instances during deserialization.

### General Note:
- The public field approach is preferable unless the flexibility of the static factory approach is needed.

### Enum Singleton Approach:
**1. Singleton Declaration:**

```java
// Enum singleton - the preferred approach
public enum Elvis {
    INSTANCE;

    public void leaveTheBuilding() {
        // Method logic
    }
}
```

**2. Advantages:**
- More concise compared to other approaches.
- Provides serialization machinery for free.
- Guarantees against multiple instantiation, even with serialization or reflection attacks.
- Thread-safe

**3. Additional Notes:**
- This approach is considered the preferred way to implement a singleton.
- A single-element enum is often the best choice for singleton implementation.
- Provides an ironclad guarantee against multiple instantiations.

**4. Limitations:**
- Cannot be used if the singleton must extend a superclass other than Enum.
- However, an enum can implement interfaces.
