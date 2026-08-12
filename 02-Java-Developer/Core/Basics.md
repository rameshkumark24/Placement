### 1. Access Modifiers
Access modifiers determine who can see and use your classes, variables, and methods. They are the primary tools used to enforce Encapsulation.

There are four levels of access in Java, from most restrictive to least restrictive:

| Modifier | Where is it accessible? | Real-World Analogy |
| :--- | :--- | :--- |
| **`private`** | **Only within the same class.** | A personal diary. Only you can read or write in it. |
| **`default`** (No keyword) | **Within the same package.** | A conversation in an office. Anyone in the room (package) can hear it. |
| **`protected`** | **Same package + Subclasses in other packages.** | A family heirloom. Passed down to family members (child classes), even if they live far away. |
| **`public`** | **Everywhere.** | A public billboard. Anyone, anywhere in the application can see it. |

*Note: If you do not write any modifier, Java assigns the **default** (package-private) modifier automatically.*

---

### 2. Constructors
A constructor is a special block of code that runs automatically the moment you create an object using the `new` keyword. Its primary job is to initialize the object's variables so it starts in a valid state.

**The Golden Rules of Constructors:**
1. It must have the **exact same name** as the class.
2. It must **not have a return type** (not even `void`).

**Types of Constructors:**
* **Default Constructor:** Provided by Java automatically if no constructors are written. It initializes variables to their default values (e.g., `0` for ints, `null` for Strings).
* **Parameterized Constructor:** Written by the developer to accept arguments and set specific initial values.

```java
public class Employee {
    String name;
    
    // Parameterized Constructor
    public Employee(String empName) {
        this.name = empName;
    }
}

// Usage: The constructor runs immediately upon object creation
// Employee e1 = new Employee("Alice");
