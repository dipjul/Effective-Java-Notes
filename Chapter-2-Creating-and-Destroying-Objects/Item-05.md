# Item 5: Prefer Dependency Injection to Hardwiring Resources
> ## Key Points:
> Do not use a singleton or static utility class to implement a class that depends on one or more underlying resources whose behavior affects that of the class, and do not have the class create these resources directly.
> Instead, pass the resources, or factories to create them, into the constructor (or static factory or
> builder) , i.e, dependency injection, will greatly enhance the flexibility, reusability, and testability of a class.
>

## Detailed Notes:
**Problem:**
- Classes with hardwired resources using static utility classes or singletons can lack flexibility and testability.
- Assumes a single, fixed resource, which may not be suitable for multiple instances or dynamic requirements.

**Inappropriate Approaches:**
```java
//  Inappropriate use of static utility - inflexible & untestable!
public class SpellChecker {
    private static final Lexicon dictionary = ...;
    private SpellChecker() {} // Noninstantiable
    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ... }
}

// Inappropriate use of singleton - inflexible & untestable!
public class SpellChecker {
    private final Lexicon dictionary = ...;
    private SpellChecker(...) {}
    public static final SpellChecker INSTANCE = new SpellChecker(...);
    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}
```

**Issues:**
- Assumes only one dictionary is sufficient.
- Inflexible and untestable.
- Static utility classes and singletons are inappropriate forclasses whose behavior is parameterized by an underlying resource. 

**Solution: Dependency Injection**
```java
// Dependency injection provides flexibility and testability
public class SpellChecker {
    private final Lexicon dictionary;

    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }

    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}
```

**Advantages:**
- Supports multiple instances, each with a different resource.
- Dependency injection pattern allows injecting the resource into the constructor.
- Improved flexibility and testability.

**Additional Patterns:**
- Use a resource factory in the constructor for more flexibility.
```java
public class SpellChecker {
    private final Lexicon dictionary;

    public SpellChecker(Supplier<? extends Lexicon> dictionaryFactory) {
        this.dictionary = Objects.requireNonNull(dictionaryFactory.get());
    }

    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}
```
- The use of `Supplier<? extends Lexicon>` means that clients can provide a factory that produces any subtype of `Lexicon` when creating an instance of `SpellChecker`.

**Note:**
- Dependency injection can be used with constructors, static factories, and builders.
- While it improves flexibility and testability, it might clutter large projects.
- Consider using a dependency injection framework for efficient dependency management.
