# Item 6: Avoid Creating Unnecessary Objects
> ## Key Points:
> 1. Minimizing unnecessary object creation is crucial for efficiency.
> 2. Strategies include reusing immutable objects, favoring static factory methods for immutable classes, and caching expensive objects.
> 3. The impact of inadvertent object creation and emphasize the importance of defensive copying to prevent bugs and security vulnerabilities.
> 4. Balancing creation and reuse, while considering defensive copying, is key to optimizing performance and code clarity.


## Detailed Notes:
- **Reuse vs. Creation:**
  - Reusing objects is often faster and more stylish.
  - Immutable objects can always be reused (Item 17).

- **Avoiding Unnecessary String Object Creation:**
  - Example:
    ```java
    // Avoid
    String s = new String("bikini");
    
    // Prefer
    String s = "bikini";
    ```
  - Explanation: Creating unnecessary String instances can be inefficient, especially in loops or frequently invoked methods. Reusing existing instances is more efficient and stylish.

- **Static Factory Methods for Immutable Classes:**
  - Example:
    ```java
    // Prefer
    Boolean value = Boolean.valueOf("true");
    
    // Avoid (deprecated in Java 9)
    Boolean value = new Boolean("true");
    ```
  - Explanation: Using static factory methods is preferred for immutable classes, as they can reuse instances and are more efficient than constructors.

- **Caching Expensive Objects:**
  - Example - Validating Roman Numerals:
    ```java
    // Original implementation
    static boolean isRomanNumeral(String s) {
        return s.matches("^(?=.)M*(C[MD]|D?C{0,3})" + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
    }
    ```
    ```java
    // Improved implementation with cached Pattern instance
    public class RomanNumerals {
        private static final Pattern ROMAN = Pattern.compile(
            "^(?=.)M*(C[MD]|D?C{0,3})" + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
        
        static boolean isRomanNumeral(String s) {
            return ROMAN.matcher(s).matches();
        }
    }
    ```
  - Explanation: Caching expensive objects, like the Pattern instance, significantly improves performance by avoiding repeated creation.

- **Understanding Expensive Objects:**
  - Example:
    ```java
    // Hideously slow! Unintentional object creation
    private static long sum() {
        Long sum = 0L; // Should be 'long' instead of 'Long'
        // ...
    }
    ```
  - Explanation: Incorrect variable types, such as using `Long` instead of `long`, can lead to unnecessary object creation and impact performance.

- **Object Reusability in Adapters:**
  - Example:
    ```java
    // No need for multiple instances of keySet view
    Set keys = map.keySet();
    ```
  - Explanation: Adapters or views can often be reused since they delegate to a backing object. In the example, multiple instances of the keySet view are unnecessary.

- **Autoboxing and Performance Impact:**
  - Example:
    ```java
    // Hideously slow! Unintentional autoboxing
    private static long sum() {
        Long sum = 0L;
        // ...
    }
    ```
  - Explanation: Unintentional autoboxing, mixing primitive and boxed primitive types, can lead to unnecessary object creation and performance issues.

- **Object Pools and Modern JVMs:**
  - Explanation: Maintaining custom object pools is generally discouraged unless objects are extremely heavyweight. Modern JVMs have optimized garbage collectors that outperform object pools for lightweight objects.

- **Counterpoint - Item 50: Defensive Copying:**
  - Explanation: Balancing object creation and reuse with defensive copying (Item 50). While avoiding unnecessary object creation is encouraged, creating objects is acceptable for clarity and simplicity. Defensive copying is crucial in specific scenarios to prevent bugs and security issues.
