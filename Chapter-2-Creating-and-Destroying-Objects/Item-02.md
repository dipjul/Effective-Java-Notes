# Item 2: Consider a builder when faced with many constructor parameters
Static factories and constructors share a limitation: they do not scale well to large numbers of optional parameters.
> ## **Key Points**:
>
> 1. <i>The Builder pattern is a good choice when designing classes
whose constructors or static factories would have more than a handful of
parameters, especially if many of the parameters are optional or of identical type.</i>
> 2. <i>Client code is much easier to read and write with builders than with telescoping
constructors, and builders are much safer than JavaBeans.</i>
>

## Detailed Notes:
### Telescoping Constructor Pattern: 
This pattern involves creating multiple constructors with different numbers of parameters. It works but has several disadvantages:

- It’s hard to write client code when there are many parameters.
- It’s harder to read the client code.
- Long sequences of identically typed parameters can cause subtle bugs.

### JavaBeans Pattern: 
This pattern involves calling a parameterless constructor to create the object and then calling setter methods to set each required parameter and each optional parameter of interest. It has its own disadvantages:

- A JavaBean may be in an inconsistent state partway through its construction.
- The class does not have the option of enforcing consistency merely by checking the validity of the constructor parameters.
- It precludes the possibility of making a class immutable and requires added effort on the part of the programmer to ensure thread safety.

### Builder Pattern: 
This pattern combines the safety of the telescoping constructor pattern with the readability of the JavaBeans pattern. The client calls a constructor (or static factory) with all of the required parameters and gets a builder object. Then the client calls setter-like methods on the builder object to set each optional parameter of interest. Finally, the client calls a parameterless build method to generate the object, which is typically immutable. 

This pattern has several advantages:

- The resulting client code is easy to write and read.
- It simulates named optional parameters as found in Python and Scala.
- It allows for early detection of invalid parameters.
- It ensures invariants against attack by doing checks on object fields after copying parameters from the builder. If a check fails, it throws an `IllegalArgumentException`.
- Use a parallel hierarchy of builders, each nested in the corresponding class. Abstract classes have abstract builders; concrete classes have concrete builders. Use covariant return typing and simulated self-type idiom to enable method chaining and avoid casting.
- Builders can have multiple varargs parameters, can be reused to build multiple objects, can fill in some fields automatically, and can make client code more readable and safe than constructors or JavaBeans.

It's disadvantage:
- Builders require extra objects to be created, which may affect performance.
- Builders are also more verbose than constructors, so they should be used only when there are enough parameters(typically four or more) to justify them.

#### Example
```java
// Abstract Pizza class
public abstract class Pizza {
    // Enum representing different types of toppings
    public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }

    // Set to hold the toppings for a pizza
    final Set<Topping> toppings;

    // Abstract Builder class - generic with a recursive type parameter
    abstract static class Builder<T extends Builder<T>> {
        // EnumSet to hold the toppings added to a pizza
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);

        // Method to add a topping to the pizza
        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        // Abstract method to build a pizza - to be implemented by subclasses
        abstract Pizza build();

        // Abstract method to get a reference to this object ("this")
        // Subclasses must override this method to return "this"
        protected abstract T self();
    }

    // Constructor for Pizza class
    Pizza(Builder<?> builder) {
        // Clone the toppings set from the builder to the Pizza object
        toppings = builder.toppings.clone(); // See Item 50
    }
}

```

```java
// NyPizza is a subclass of Pizza
public class NyPizza extends Pizza {
    // Enum representing the size of the pizza
    public enum Size { SMALL, MEDIUM, LARGE }
    // Field for the size of the pizza
    private final Size size;

    // Builder class for NyPizza
    public static class Builder extends Pizza.Builder<Builder> {
        // Field for the size of the pizza
        private final Size size;

        // Constructor for the Builder class
        // Takes a Size as a parameter
        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        // Method to build an instance of NyPizza
        @Override public NyPizza build() {
            return new NyPizza(this);
        }

        // Method to return a reference to this object ("this")
        @Override protected Builder self() { return this; }
    }

    // Constructor for NyPizza class
    // Takes a Builder as a parameter
    private NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }
}

```

```java
// Calzone is a subclass of Pizza
public class Calzone extends Pizza {
    // Field to indicate if the sauce is inside
    private final boolean sauceInside;

    // Builder class for Calzone
    public static class Builder extends Pizza.Builder<Builder> {
        // Field to indicate if the sauce is inside, default is false
        private boolean sauceInside = false;

        // Method to indicate that the sauce should be inside
        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        // Method to build an instance of Calzone
        @Override public Calzone build() {
            return new Calzone(this);
        }

        // Method to return a reference to this object ("this")
        @Override protected Builder self() { return this; }
    }

    // Constructor for Calzone class
    // Takes a Builder as a parameter
    private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }
}

```

```java
// Creating a new NyPizza object with SMALL size and two toppings: SAUSAGE and ONION
NyPizza pizza = new NyPizza.Builder(Size.SMALL) // Start building a new NyPizza with SMALL size
    .addTopping(Topping.SAUSAGE) // Add SAUSAGE topping
    .addTopping(Topping.ONION) // Add ONION topping
    .build(); // Build the NyPizza object

// Creating a new Calzone object with HAM topping and sauce inside
Calzone calzone = new Calzone.Builder() // Start building a new Calzone
    .addTopping(Topping.HAM) // Add HAM topping
    .sauceInside() // Specify that the sauce should be inside
    .build(); // Build the Calzone object

```
