# Item 4: Enforce noninstantiability with a private constructor
> ## Key Points:
> It discusses enforcing non-instantiability in utility classes (classes with only static methods and fields) by including a private constructor. This prevents unintentional instantiation and subclassing, as the private constructor is inaccessible outside the class.
>
> An AssertionError is thrown if the constructor is accidentally invoked from within the class, ensuring the class is never instantiated.
>
## Detailed Notes:

1. **Utility Classes**: These are classes that consist of static methods and static fields. They are used to group related methods on primitive values or arrays, similar to `java.lang.Math` or `java.util.Arrays`. They can also group static methods, including factories, for objects that implement some interface, similar to `java.util.Collections`.

2. **Non-instantiability**: Utility classes are not designed to be instantiated. However, in the absence of explicit constructors, the compiler provides a public, parameterless default constructor. This can lead to unintentional instantiation of classes.

3. **Preventing Instantiation**: Making a class abstract does not prevent it from being instantiated as it can be subclassed and the subclass can be instantiated. A simple idiom to ensure non-instantiability is to include a private constructor in the class. This makes the constructor inaccessible outside the class.

4. **Example of Non-instantiable Utility Class**:
```java
public class UtilityClass {
    private UtilityClass() {
        throw new AssertionError();
    }
    // Remainder omitted
}
```
In this example, the `AssertionError` isn't strictly required but provides insurance in case the constructor is accidentally invoked from within the class. It guarantees the class will never be instantiated under any circumstances. This idiom also prevents the class from being subclassed as all constructors must invoke a superclass constructor, explicitly or implicitly, and a subclass would have no accessible superclass constructor to invoke. It is wise to include a comment explaining this.
