# Item 1: Consider static factory methods instead of constructors
Static factory methods are a technique in object-oriented programming where a class provides a public static method that returns an instance of the class. 
This is an alternative to using public constructors.
```java
public static Boolean valueOf(boolean b) {
  return b ? Boolean.TRUE : Boolean.FALSE;
}
```
> ## **Key Points**:
>
> ### **Advantages**:
>
> 1. <i>Unlike constructors, they have names.</i>
> 2. <i>Unlike constructors, they are not required to create a new object each time they’re invoked.</i>
> 3. <i>Unlike constructors, they can return an object of any subtype of their return type.</i>
> 4. <i>The class of the returned object can vary from call to call as a function of the input parameters.</i>
> 5. <i>The class of the returned object need not exist when the class containing the method is written.</i>
>
> ### **Disadvantages**:
>
> 1. <i>The main limitation of providing only static factory methods is that classes without public or protected constructors cannot be subclassed.</i>
> 2. <i>A second shortcoming of static factory methods is that they are hard for programmers to find.</i>


## Detailed Notes:
### Advantages:
#### 1. Unlike constructors, they have names.
  - Static factory methods have an advantage over constructors as they have names, making them easier to use and the resulting client code easier to read.
  - An example is the `BigInteger.probablePrime` static factory method in Java, which is more descriptive than a constructor `BigInteger(int, int, Random)` with the same functionality.
  - A class can only have one constructor with a given signature. Using two constructors with parameter lists that only differ in order can lead to confusion and mistakes.
  - Static factory methods, having names, do not share this restriction. If a class needs multiple constructors with the same signature, it's better to replace them with static factory methods with carefully chosen names to highlight their differences.

#### 2. Unlike constructors, they are not required to create a new object each time they’re invoked.
- Static factory methods, unlike constructors, don't need to create a new object every time they're called. This can save time and resources, especially if the objects are complex or costly to create.
- This approach allows classes to use already created instances or cache instances for future use, avoiding unnecessary duplication. An example is the `Boolean.valueOf(boolean)` method, which never creates a new object.
- This technique is similar to the <strong>Flyweight pattern</strong>, which is a design pattern used to minimize memory use by sharing as much data as possible with similar objects.
- Static factory methods can return the same object for repeated calls, allowing classes to control the instances that exist at any time. These are called <strong>instance-controlled classes</strong>.
- Instance control can ensure that a class is a <strong>singleton</strong> (only one instance can exist) or <strong>non-instantiable</strong> (cannot be instantiated).
- It also allows an <strong>immutable value class</strong> (a class that doesn't change once it's created) to ensure that no two equal instances exist. This means if two instances are equal, they are actually the same instance (`a.equals(b)` if and only if `a == b`).
- Enum types, a special type of class representing a group of constants, also provide this guarantee.

##### Important terms:
1. Flyweight Pattern
<details>
<summary><strong><i>2. Immutable Class</i></strong></summary>

```java
import java.util.HashMap;
import java.util.Map;

// 1. Declare the class as final
final class Student {
    // 2. Make all fields private and final
    private final String name;
    private final int regNo;
    private final Map<String, String> metadata;

    // 4. Perform a deep copy in the constructor
    public Student(String name, int regNo, Map<String, String> metadata) {
        this.name = name;
        this.regNo = regNo;
        Map<String, String> tempMap = new HashMap<>();
        for (Map.Entry<String, String> entry : metadata.entrySet()) {
            tempMap.put(entry.getKey(), entry.getValue());
        }
        this.metadata = tempMap;
    }

    // 3. Don’t provide setter methods

    public String getName() {
        return name;
    }

    public int getRegNo() {
        return regNo;
    }

    // 5. Perform a deep copy in the getter methods
    public Map<String, String> getMetadata() {
        Map<String, String> tempMap = new HashMap<>();
        for (Map.Entry<String, String> entry : this.metadata.entrySet()) {
            tempMap.put(entry.getKey(), entry.getValue());
        }
        return tempMap;
    }
}
```
</details>

<details>
<summary><strong><i>3. Singleton Class</i></strong></summary>

```java
public class Singleton {
    // Step 2: Private static instance
    private static Singleton instance = null;

    // Step 1: Private constructor
    private Singleton() {
        // Optional: add logic here
    }

    // Step 3: Public static method
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
</details>


<details>
<summary><strong><i>4. Non-Instantiable Class</i></strong></summary>

```java
public final class NonInstantiable {
    // Private constructor
    private NonInstantiable() {
        throw new UnsupportedOperationException("This class cannot be instantiated");
    }

    // Static method
    public static void displayMessage() {
        System.out.println("Hello from NonInstantiable class!");
    }
}

// From some other class
NonInstantiable.displayMessage();

   ```
</details>


#### 3. Unlike constructors, they can return an object of any subtype of their return type.
- <strong>Return flexibility:</strong> Can return objects of any subtype of their return type, providing flexibility in object creation.
- <strong>API compactness:</strong> Enable hiding implementation classes, leading to more concise and focused APIs.
- <strong>Interface-based frameworks:</strong> Align well with interface-based design, promoting loose coupling and abstraction.
- <strong>Conventions before Java 8:</strong>
  - Static factory methods for an interface Type were placed in a noninstantiable companion class named Types.
  - Example: Java Collections Framework's static factory methods in java.util.Collections class.
- <strong>Changes in Java 8 and beyond:</strong>
  - Interfaces can now have static methods, reducing the need for companion classes.
  - However, interface static members must still be public in Java 8.
  - Java 9 allows private static methods, but static fields and member classes remain public.
- <strong>Best practices:</strong>
  - Place static factory methods directly in the interface itself when possible (Java 8 and later).
  - Consider using a package-private class for implementation code if needed.
  - Employ static factory methods to create objects without exposing implementation classes, leading to cleaner APIs and better encapsulation.

- <details>
  <summary><strong><i>Package Private class for implementation code</i></strong></summary>
  <strong>Interface</strong>
  
  ```java
  public interface Shape {
      double getArea();
  
      // Static factory method in the interface (Java 8+)
      static Shape createRectangle(double width, double height) {
          return new RectangleImpl(width, height); // Package-private implementation
      }
  }
  ```
  
  <strong>Implementation</strong>
  ```java
  class RectangleImpl implements Shape {
      private final double width, height;
  
      RectangleImpl(double width, double height) {
          this.width = width;
          this.height = height;
      }
  
      @Override
      public double getArea() {
          return width * height;
      }
  }
  ```
  <strong>Client code</strong>
  ```java
  // Client code only interacts with the interface
  Shape rectangle = Shape.createRectangle(5, 3);
  double area = rectangle.getArea(); // Output: 15.0
  ```
  
  - The Shape interface exposes the createRectangle static factory method, but its implementation details are hidden within the package-private RectangleImpl class.
  - Clients only interact with the public interface, promoting loose coupling and encapsulation.
  - The implementation class can be modified or replaced without affecting client code as long as the interface contract remains consistent.
  - This approach is particularly useful when:
    - You want to hide implementation details for better control over API exposure.
    - You have complex implementation logic that would clutter the interface.
    - You need to manage multiple implementation classes for a single interface.
  </details>


#### 4. The class of the returned object can vary from call to call as a function of the input parameters.
- <strong>Dynamic Return Type Selection:</strong>
  - Static factory methods can return objects of different subtypes based on input parameters, offering flexibility and potential performance benefits.
  - This contrasts with constructors, which always create instances of a fixed class.
  - Example: `EnumSet`:
    - `EnumSet` class in Java has static factories instead of public constructors.
    - It returns either `RegularEnumSet` or `JumboEnumSet` instances depending on the enum type's size for optimal performance.

- <strong>Implementation Flexibility:</strong>
  - Clients interact with the interface, not specific implementation classes.
  - Developers can change implementation details without breaking client code.
  - New implementations can be added seamlessly in future releases.

- <strong>Advantages:</strong>
  - Performance optimization: Choose appropriate implementations based on input characteristics.
  - Evolvability: Adapt to changing requirements and improve performance over time.
  - Loose coupling: Clients depend on the interface, not concrete classes, enhancing maintainability.
  - Encapsulation: Hide implementation details and promote better API design.


#### 5. The class of the returned object need not exist when the class containing the method is written.

- **Flexibility in Service Provider Frameworks:**
  - Static factory methods can return objects of classes that didn't exist when the method was written, enabling flexibility in service provider frameworks.

- **Service Provider Framework Components:**
  - **Service interface:** Defines the contract for the service.
  - **Provider registration API:** Allows providers to register implementations.
  - **Service access API:** Used by clients to obtain service instances (typically a flexible static factory).
  - **Optional service provider interface:** Describes a factory object for creating service instances.

- **JDBC Example:**
  - **Connection:** Service interface.
  - **DriverManager.registerDriver:** Provider registration API.
  - **DriverManager.getConnection:** Service access API.
  - **Driver:** Service provider interface.

- **Variants and Patterns:**
  - **Bridge pattern:** Service access API can return a richer interface than providers.
  - **Dependency injection frameworks:** Powerful service providers.
  - **java.util.ServiceLoader:** General-purpose service provider framework in Java 6+.

- **Advantages:**
  - **Decoupling:** Separates clients from implementations, promoting flexibility.
  - **Extensibility:** New implementations can be added without modifying client code.
  - **Configuration-driven service selection:** Clients can choose implementations based on criteria.
  - **Dependency injection:** Facilitates loose coupling and testability.
  - **Encapsulation:** Hides implementation details and encourages modular design.


### Disadvantages:
#### 1. The main limitation of providing only static factory methods is thatclasses without public or protected constructors cannot be subclassed.
- **Non-Subclassable:**
  - Classes that only provide static factory methods and do not have public or protected constructors cannot be subclassed.
  - This makes it impossible to subclass any of the convenience implementation classes in the Collections Framework.
  - However, this can be beneficial as it encourages programmers to use composition instead of inheritance, and is necessary for creating immutable types.

#### 2. A second shortcoming of static factory methods is that they are hard for programmers to find. 
- **Discoverability:**
  - Static factory methods can be hard for programmers to find as they do not stand out in API documentation like constructors do.
  - This can make it challenging to figure out how to instantiate a class that provides static factory methods instead of constructors.
  - While the Javadoc tool may eventually highlight static factory methods, currently, this issue can be mitigated by emphasizing static factories in class or interface documentation and adhering to common naming conventions.
 

### Some common names for static factory methods

- **Naming Conventions:**
  - **from:** Type conversion, single parameter (e.g., `Date.from(instant)`).
  - **of:** Aggregation, multiple parameters (e.g., `EnumSet.of(JACK, QUEEN, KING)`).
  - **valueOf:** Verbose alternative to `from` and `of` (e.g., `BigInteger.valueOf(Integer.MAX_VALUE)`).
  - **instance** or **getInstance:** Describes the instance but doesn't guarantee a new object (e.g., `StackWalker.getInstance(options)`).
  - **create** or **newInstance:** Guarantees a new instance on each call (e.g., `Array.newInstance(classObject, arrayLen)`).
  - **getType:** Used when the factory method is in a different class (e.g., `Files.getFileStore(path)`).
  - **newType:** Like `newInstance`, but in a different class (e.g., `Files.newBufferedReader(path)`).
  - **type:** Concise alternative to `getType` and `newType` (e.g., `Collections.list(legacyLitany)`).

- **Additional Insights:**
  - These conventions offer guidance, but specific names can vary based on context and design preferences.
  - Choose names that clearly convey the method's purpose and the type of object it creates.
  - Consider consistency with other APIs in your codebase for readability and maintainability.
