# Java Collections Framework: Core Concepts & Comparisons

The Java Collections Framework provides an architecture to store and manipulate groups of objects. Understanding which collection to use and *why* is a guaranteed topic in any Java technical interview.

---

## 1. The Big Picture: List, Set, and Map

Before diving into specific classes, you must understand the three main interfaces. 

| Feature | `List` | `Set` | `Map` |
| :--- | :--- | :--- | :--- |
| **Definition** | An ordered collection (sequence). | A collection that contains no duplicate elements. | An object that maps keys to values. (Technically not a Collection, but part of the framework). |
| **Duplicates** | ✅ Allows duplicates. | ❌ No duplicates allowed. | ❌ Keys must be unique. ✅ Values can be duplicated. |
| **Ordering** | Maintains insertion order. | Generally unordered (depends on implementation). | Generally unordered (depends on implementation). |
| **Access Method** | Accessed via index (e.g., `get(0)`). | Accessed via object reference (no index). | Accessed via Key (e.g., `get(key)`). |
| **Null Values** | Allows multiple `null` values. | Allows only one `null` value. | Allows one `null` key and multiple `null` values. |



---

## 2. ArrayList vs. LinkedList

Both implement the `List` interface, but they handle memory and operations very differently.

| Feature | `ArrayList` | `LinkedList` |
| :--- | :--- | :--- |
| **Internal Data Structure** | Dynamic Array. | Doubly Linked List. |
| **Memory Allocation** | Contiguous memory locations. | Scattered memory (nodes connected by pointers). |
| **Search / Retrieval** | **Fast (O(1)):** It uses an index, so it jumps directly to the element. | **Slow (O(n)):** It must traverse the list node-by-node from the beginning or end. |
| **Insertion / Deletion** | **Slow (O(n)):** If you insert/delete in the middle, it has to shift all subsequent elements to new positions. | **Fast (O(1)):** It only needs to change the pointer links of the neighboring nodes. |
| **When to use?** | When your application does more **READING / SEARCHING**. | When your application does more **INSERTING / DELETING**. |



---

## 3. HashSet vs. TreeSet

Both implement the `Set` interface (no duplicates), but they organize their data differently.

| Feature | `HashSet` | `TreeSet` |
| :--- | :--- | :--- |
| **Internal Implementation** | Backed by a `HashMap` (Hash table). | Backed by a `TreeMap` (Red-Black Tree). |
| **Ordering** | **Unordered.** Does not maintain insertion order. | **Sorted.** Elements are sorted in ascending (natural) order or by a custom `Comparator`. |
| **Performance** | **O(1)** for add, remove, and contains (Very Fast). | **O(log n)** for add, remove, and contains (Slightly slower due to sorting). |
| **Null Elements** | Allows exactly one `null` element. | **Does NOT allow `null`.** (Throws `NullPointerException` because it cannot compare `null` to sort it). |
| **When to use?** | When you just need a unique collection and don't care about the order. | When you need a unique collection that is always alphabetically or numerically sorted. |

---

## 4. HashMap vs. LinkedHashMap vs. TreeMap

All three implement the `Map` interface (Key-Value pairs), but they dictate the iteration order of the keys.

| Feature | `HashMap` | `LinkedHashMap` | `TreeMap` |
| :--- | :--- | :--- | :--- |
| **Internal Data Structure** | Array of LinkedLists (Buckets). | Hash Table + Doubly Linked List. | Red-Black Tree. |
| **Ordering** | **Unordered.** No guarantee how elements will be ordered when looping. | Maintains **Insertion Order**. | Maintains **Sorted Order** (ascending by keys). |
| **Performance** | **Fastest (O(1))** for get/put. | **Slightly slower** than HashMap because it has to maintain linked pointers. | **Slowest (O(log n))** because it has to sort the tree on every insertion. |
| **Null Keys** | Allows exactly one `null` key. | Allows exactly one `null` key. | **Does NOT allow `null` keys** (cannot compare them for sorting). |
| **When to use?** | Default choice for key-value pairs when order doesn't matter. | When you need a cache or need to remember the order in which items were added. | When you need a dictionary or leaderboard sorted by the keys. |



---

### Interview Pro-Tip 💡
If an interviewer asks: *"I want to store a list of student names, remove duplicates, and print them out in alphabetical order. Which collection should I use?"*

**Your Answer:** *"A `TreeSet`. It implements the `Set` interface to automatically filter out duplicates, and uses a Red-Black tree internally to keep the names in alphabetical (natural) order."*

# Internal Working of HashSet & HashMap in Java

In Java, a `HashSet` is backed by a `HashMap` internally. To understand how a `HashSet` stores unique elements, you must understand the internal workings of `HashMap` and the contract between `equals()` and `hashCode()`.

---

## ❌ The Problem: Without `equals()` and `hashCode()`

By default, Java checks if two objects are the exact same instance in memory. If we create two distinct objects with the exact same data, Java treats them as different. Therefore, a `HashSet` will fail to filter out duplicates.

```java
import java.util.HashSet;

class employee {
    int id;
    String name;
    
    public employee(int id, String name) {
        this.id = id;
        this.name = name;
    }
}

public class HashingTest {
    public static void main(String[] args) {
        HashSet<employee> employ = new HashSet<>();
        
        employee e1 = new employee(1, "John");
        employee e2 = new employee(1, "John"); // Identical data, different memory location
        
        employ.add(e1);
        employ.add(e2);
        
        // Output will be 2! The duplicate was NOT removed.
        System.out.println("Set size without override: " + employ.size()); 
    }
}

✅ The Solution: Overriding the Methods
To fix this, we must teach Java what makes two Employee objects "equal" by overriding methods from the Object class.

The Golden Rule: If two objects are equal according to equals(), they MUST return the exact same integer from hashCode().

Java
import java.util.HashSet;
import java.util.Objects;

class Employee {
    int id;
    String name;
    
    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }
    
    // 1. Override equals(): Check if data is identical
    @Override
    public boolean equals(Object o) {
        if (this == o) return true; // Is it the exact same object in memory?
        if (o == null || getClass() != o.getClass()) return false; // Is it a different type?
        
        Employee employee = (Employee) o;
        return id == employee.id && Objects.equals(name, employee.name);
    }
    
    // 2. Override hashCode(): Generate a math number based on the data
    @Override
    public int hashCode() {
        return Objects.hash(id, name);
    }
}

public class HashingTestCorrected {
    public static void main(String[] args) {
        HashSet<Employee> Employ = new HashSet<>();
        
        Employee e3 = new Employee(2, "Doe");        
        Employee e4 = new Employee(2, "Doe"); // Identical data
        
        Employ.add(e3);
        Employ.add(e4); 
        
        // Output will be 1! The duplicate was successfully rejected.
        System.out.println("Set size with override: " + Employ.size()); 
    }
}
🧠 The 5-Point Explanation (Interview Core)
When you call Employ.add(e3), here is exactly what happens behind the scenes in Java's memory. If an interviewer asks how a HashMap or HashSet works, explain these five steps:

1. Buckets
When a HashSet (or HashMap) is created, Java initializes an array in memory called a "Bucket Array." The default capacity is 16 buckets (indexed 0 to 15).

2. hashCode()
When you insert an object, Java calls the object's hashCode() method to generate an integer. Java then runs a mathematical formula on this integer (bitwise AND operation) to calculate which specific Bucket (index) the object should be placed into.

3. Collision
A collision occurs when two completely different objects generate the same hash code, or when the math formula maps them to the exact same Bucket. Collisions are normal and expected.

4. equals()
When a collision happens (Java tries to put e4 into a bucket that already holds e3), Java uses the equals() method to resolve it.

If equals() is TRUE: Java knows this is a duplicate. In a HashSet, it rejects the new item. In a HashMap, it overwrites the old value with the new value.

If equals() is FALSE: Java knows these are two different objects that just happen to share a bucket. It stores both of them in that same bucket by linking them together using a LinkedList (or a Balanced Tree if the list gets too long).

5. Load Factor & Rehashing
If you keep adding elements, the buckets will get crowded, causing too many collisions and slowing down performance.

Java monitors the Load Factor, which defaults to 0.75 (meaning the array is 75% full).

Once the buckets are 75% full (e.g., 12 out of 16 buckets are occupied), Java triggers Rehashing.

Rehashing creates a brand new bucket array that is double the size (32 buckets) and recalculates the position of every single element to spread them out evenly again, ensuring the add and search operations remain incredibly fast (O(1) time complexity).
