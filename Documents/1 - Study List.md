# Study Guide

## Interview notes powered by Gemini AI 

# To Place in Document {#to-place-in-document}

## **List of items from feedback and thoughts that should be elaborated on and given a specific action in this document** {#list-of-items-from-feedback-and-thoughts-that-should-be-elaborated-on-and-given-a-specific-action-in-this-document}

How would you approach testing a new feature

More dev examples of what I did.

What problems did I solve? Looking for a problem solver more than a coding hero.

When presented with a problem, how did I solve it. Answer very methodically, first talking about asking for more info, how many users? 100 or 1,000,000, what region is it deployed. Then talk about spiking out a demo, after talking with stakeholders. If possible… have a real example of when I did this.

Use the STAR method

CV idea’s to show I’m more dev than automation engineer. 1\. Put the languages at the top. 2\. Sell myself as an engineer who gives solutions, sometimes they are dev solutions, sometimes they are automation solutions.

Yarn and npm, differences

# Hard Skills {#hard-skills}

## **Questions** {#questions}

…

## **JAVA** {#java}

### **Garbage Collection** {#garbage-collection}

In simple terms, **Garbage Collection (GC)** is Java’s automatic memory management system.

In older languages like C++, developers have to manually "allocate" memory for an object and then "deallocate" (delete) it when they are done. If they forget, the program leaks memory and eventually crashes. Java handles this for you: it automatically identifies objects that are no longer being used and clears them out to free up space.

---

How it Works: The "Reachability" Test

The Garbage Collector follows a simple rule: **If an object cannot be reached by any live part of the program, it is eligible for deletion.**

1. **Live Objects:** Objects currently being used or referenced by a variable in your code.  
2. **Dead Objects:** Objects that no longer have a "path" to them.  
   * *Example:* You set a variable to `null` or a method finishes and its local variables disappear. The object that variable was pointing to is now "orphaned."

---

The Three Steps

The GC generally follows a **"Mark and Sweep"** pattern:

1. **Mark:** The GC starts from the "Roots" (active threads, static variables, etc.) and follows every reference to find every object that is still "reachable." It marks these as "Alive."  
2. **Sweep:** It goes through the heap (where objects live) and removes everything that wasn't marked.  
3. **Compact (Optional):** It moves the remaining live objects closer together to prevent "fragmentation" (holes in memory), making room for large new objects.

---

Memory Structure: Generational Strategy

Java assumes that **most objects die young** (like temporary strings in a loop). To be efficient, it divides the Heap into generations:

* **Young Generation:** Where new objects are born. GC happens here frequently (**Minor GC**). It's very fast.  
* **Old Generation:** If an object survives several rounds of GC in the Young Generation, it gets "promoted" here. GC happens here less often (**Major GC**).  
* **Permanent Generation (Metaspace):** Where the actual class definitions and static metadata are stored.

---

Key Things to Remember

* **It’s Non-Deterministic:** You can’t predict *exactly* when the GC will run. You can suggest it using `System.gc()`, but the JVM usually ignores you (it knows better).  
* **Stop-the-World:** In some GC versions, the entire application pauses briefly while the GC cleans up. Modern Java versions (like G1 or ZGC) are designed to make these pauses almost invisible.  
* **Memory Leaks still happen:** Even with GC, you can have a "leak" if you accidentally keep a reference to an object you don't need (e.g., adding objects to a `static List` and never removing them).

### **Static variables** {#static-variables}

In Java, the `static` keyword means that a member (variable or method) belongs to the class itself, rather than to a specific instance (object) of that class.

```java
class Player {
    String name;          // Instance variable: each player has their own name
    static int count = 0; // Static variable: shared by ALL players

    Player(String name) {
        this.name = name;
        count++; // Increment the shared counter every time a player is created
    }
}
```

### **Int, Double, Float, BigDecimal** {#int,-double,-float,-bigdecimal}

Cast between them all….


## **Languages, Frameworks, and Libraries** {#languages,-frameworks,-and-libraries}

Java

C\#

Javascript/Typescript

SQL

React.js

Angular

Selenium

Cypress

Playwright

## **Design Pattern** {#design-pattern}

### **Factory Pattern** {#factory-pattern}

The Factory Pattern is a powerful creational design pattern that should be used primarily because it promotes loose coupling and adheres to the **Open/Closed Principle (OCP)**. By delegating object instantiation to a specialized factory class, the client code interacts only with a common **interface** (like Bird), never with the concrete classes (Eagle or Condor). This separation means that if you need to introduce a new bird type, **you only modify the factory**—the client code that uses the birds remains untouched. This dramatically increases the maintainability and flexibility of the code, making it easier to manage complex object creation logic and scale the application without breaking existing functionality

.

```java
public class BirdFactory {
    // Enum to make the type checking safer and clearer
    public enum BirdType {
        EAGLE,
        CONDOR
    }

    /**
     * Factory method to create an instance of a Bird.
     * @param type The type of bird to create.
     * @return A concrete Bird object (Eagle or Condor).
     */
    public Bird createBird(BirdType type) {
        if (type == null) {
            return null;
        }

        switch (type) {
            case EAGLE:
                return new Eagle();
            case CONDOR:
                return new Condor();
            default:
                throw new IllegalArgumentException("Unknown bird type: " + type);
        }
    }
}
```

```java
public class FactoryDemo {
    public static void main(String[] args) {
        BirdFactory factory = new BirdFactory();

        // Create an Eagle object
        Bird eagle = factory.createBird(BirdFactory.BirdType.EAGLE);
        System.out.println("--- Creating an Eagle ---");
        eagle.fly();
        eagle.makeSound();

        System.out.println("\n----------------------------\n");

        // Create a Condor object
        Bird condor = factory.createBird(BirdFactory.BirdType.CONDOR);
        System.out.println("--- Creating a Condor ---");
        condor.fly();
        condor.makeSound();
    }
}
```

### **Page Object Model** {#page-object-model}

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class LoginPage {
    private final WebDriver driver;
    private final By usernameInput = By.id("user"); // Minimal ID
    private final By passwordInput = By.name("pass"); // Minimal name
    private final By loginButton = By.tagName("button"); // Minimal tag

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    public void login(String user, String pass) {
        driver.findElement(usernameInput).sendKeys(user);
        driver.findElement(passwordInput).sendKeys(pass);
        driver.findElement(loginButton).click();
    }
}
```

## **S.O.L.I.D** {#s.o.l.i.d}

### **S \- Single Responsibility Principle** {#s---single-responsibility-principle}

The Single Responsibility Principle says:

**A class should have only one reason to change.**

That means:

* A class should focus on one job only.  
* If a class has multiple responsibilities (e.g., data storage, printing, persistence), it becomes harder to maintain, test, and extend.

Why It Matters

* If a class does too many things, changes in one responsibility can break another.  
* Having separate classes for separate responsibilities makes the code easier to maintain and extend.  
* Helps with clean architecture and avoids “god classes.”

Bird Example: Without SRP

Suppose we have a Bird class that handles everything:

```java
// ❌ Bad: multiple responsibilities
class Bird {
    String name;

    void saveToDatabase() {
        // code to save bird in DB
    }

    void printDetails() {
        System.out.println("Bird: " + name);
    }
}
```

❌ Problems:

* This class is responsible for:  
  * Data model (bird’s name)  
  * Persistence (saving to DB)  
  * Presentation (printing details)  
* If the database changes → you must modify this class.  
* If printing changes (e.g., print JSON instead of plain text) → you must modify this class.  
* Too many reasons to change → violates SRP.

Bird Example: With SRP

Let’s separate responsibilities into distinct classes:

```java
// ✅ Class focused only on bird data
class Bird {
    String name;

    Bird(String name) {
        this.name = name;
    }
}

// ✅ Handles saving birds (persistence responsibility)
class BirdRepository {
    void save(Bird bird) {
        System.out.println("Saving " + bird.name + " to database...");
    }
}

// ✅ Handles printing birds (presentation responsibility)
class BirdPrinter {
    void print(Bird bird) {
        System.out.println("Bird: " + bird.name);
    }
}

// Usage
public class SanctuaryApp {
    public static void main(String[] args) {
        Bird eagle = new Bird("Eagle");

        BirdRepository repo = new BirdRepository();
        BirdPrinter printer = new BirdPrinter();

        repo.save(eagle);
        printer.print(eagle);
    }
}
```

✅ Advantages:

* Bird only cares about representing a bird.  
* BirdRepository cares about saving birds.  
* BirdPrinter cares about displaying birds.  
* Each class has exactly one reason to change.

#### **Real-World Analogy** {#real-world-analogy}

Think of a bird sanctuary team:

* A bird keeper looks after the bird’s well-being.  
* A scientist records data about the bird.  
* A tour guide talks about the bird to visitors.  
* You wouldn’t expect one person to do all of these jobs at once — it would be inefficient and error-prone.

#### **Summary** {#summary}

* Without SRP → one Bird class does everything (data, persistence, printing).  
* With SRP → responsibilities are split: Bird, BirdRepository, BirdPrinter.  
* This makes the system modular, testable, and easier to extend.

### **O - Open Closed Principle** {#o---open-closed-principle}

The Open/Closed Principle says:

**A class should be open for extension but closed for modification.**

That means:

* You should be able to add new functionality (extend behavior)  
* Without having to change the existing, stable code (modify classes that are already tested and deployed).

Bird Example: Without OCP

```java
class BirdInfoPrinter {
    void printInfo(String birdType) {
        if (birdType.equals("Eagle")) {
            System.out.println("This is an Eagle");
        } else if (birdType.equals("Condor")) {
            System.out.println("This is a Condor");
        } else {
            System.out.println("Unknown bird");
        }
    }
}
```

❌ Problems:

* If you add a new bird (say Parrot), you must go back and edit BirdInfoPrinter.  
* Every new bird requires modifying old code — violating OCP.  
* Over time, the class becomes cluttered with if/else or switch statements.

Bird Example: With OCP

Instead, you design an abstract class (or interface) Bird that defines the behavior you want to extend:

```java
abstract class Bird {
    abstract String getType();
}

class Eagle extends Bird {
    String getType() { return "Eagle"; }
}

class Condor extends Bird {
    String getType() { return "Condor"; }
}

class BirdInfoPrinter {
    void printInfo(List<Bird> birds) {
        for (Bird b : birds) {
            System.out.println("This is a " + b.getType());
        }
    }
}
```

✅ Advantages:

* BirdInfoPrinter no longer needs to know about specific bird types.  
* To add a new bird (say Parrot), you extend the system with a new subclass:

```java
class Parrot extends Bird {
    String getType() { return "Parrot"; }
}
```

#### **Real-World Analogy** {#real-world-analogy-1}

Think of BirdInfoPrinter like a bird-watching guidebook:

* Instead of rewriting the whole book every time a new bird species is discovered, you just add a new bird entry card to the guide.  
* The guidebook’s structure (the printing system) doesn’t change.

#### **Summary** {#summary-1}

* Open for extension → you can add new birds like Parrot, Falcon, Owl.  
* Closed for modification → BirdInfoPrinter doesn’t need changes when new bird types appear.  
* This keeps your codebase stable, flexible, and less error-prone as it grows.

### **L \- Liskov Substitution Principle** {#l---liskov-substitution-principle}

Subtypes must be substitutable for their base types.

```java
class Bird {
    String fly() { return "Flying high"; }
}

class Eagle extends Bird {
    @Override
    String fly() { return "Eagle soars gracefully"; }
}

class Condor extends Bird {
    @Override
    String fly() { return "Condor glides on thermals"; }
}

// Works fine: any Bird reference can hold an Eagle or Condor
public class Sanctuary {
    public static void main(String[] args) {
        Bird b1 = new Eagle();
        Bird b2 = new Condor();
        System.out.println(b1.fly());
        System.out.println(b2.fly());
    }
}
```

```text
Eagle soars gracefully
Condor glides on thermals
```

#### **Real-World Analogy** {#real-world-analogy-2}

Think of a bird sanctuary app:

* If you say “all birds can fly,” then adding a penguin breaks your program (like UnsupportedOperationException).  
* By respecting LSP, you model reality better: not all birds fly, so only FlyableBirds should promise fly().

#### **Summary** {#summary-2}

* Subtypes (Eagle, Condor) can replace Bird safely → fly() always works.  
* Avoid lying subclasses (like a Penguin that throws error in fly()).  
* LSP keeps polymorphism safe and ensures your code doesn’t break when you add new types.

### **I \- Interface Segregation Principle** {#i---interface-segregation-principle}

The Interface Segregation Principle says:

**No client should be forced to depend on methods it does not use.**

That means:

* Instead of one fat interface with many unrelated methods, create smaller, focused interfaces.  
* Classes should only implement what makes sense for them.

Why It Matters

* Prevents unnecessary code (empty or dummy method implementations).  
* Keeps interfaces clean and focused.  
* Makes the system more flexible — different birds can implement different capabilities without being forced into irrelevant ones.

Bird Example: Without ISP

Imagine we create one big BirdActions interface:

```java
// ❌ Fat interface
interface BirdActions {
    void fly();
    void swim();
    void scavenge();
}

class Eagle implements BirdActions {
    public void fly() { System.out.println("Eagle soars!"); }
    public void swim() { /* ??? Eagles don't swim */ }
    public void scavenge() { /* ??? not typical */ }
}

class Condor implements BirdActions {
    public void fly() { System.out.println("Condor glides!"); }
    public void swim() { /* ??? Condors don't swim */ }
    public void scavenge() { System.out.println("Condor scavenges!"); }
}
```

❌ Problems:

* Eagle is forced to implement swim() even though it doesn’t make sense.  
* Condor may or may not scavenge, but the method is still mandatory.  
* Violates ISP — classes depend on methods they don’t need.

Bird Example: With ISP

Instead, split the big interface into smaller, focused ones:

```java
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

interface Scavenger {
    void scavenge();
}

class Eagle implements Flyable {
    public void fly() { System.out.println("Eagle soars!"); }
}

class Condor implements Flyable, Scavenger {
    public void fly() { System.out.println("Condor glides!"); }
    public void scavenge() { System.out.println("Condor scavenges!"); }
}
```

✅ Advantages:

* Eagle only implements what it can actually do: Flyable.  
* Condor implements both Flyable and Scavenger.  
* If we later add a Penguin, it could just implement Swimmable.  
* Each bird only depends on methods that are relevant.

#### **Real-World Analogy** {#real-world-analogy-3}

Think of a bird-care system in a sanctuary:

* You wouldn’t hand every bird the same care checklist (“teach to fly, teach to swim, teach to scavenge”).  
* Instead, you’d have separate specialized routines.  
* A penguin would follow the swimming routine, not the flying one.

#### **Summary** {#summary-3}

* Without ISP → one bloated interface forces irrelevant methods (Eagle having to “swim”).  
* With ISP → smaller, focused interfaces (Flyable, Swimmable, Scavenger) let each bird implement only what it actually does.  
* This makes code cleaner, easier to maintain, and closer to reality.

### **D \- Dependency Inversion Principle** {#d---dependency-inversion-principle}

The Dependency Inversion Principle says:

* **High-level modules should not depend on low-level modules. Both should depend on abstractions.**  
* **Abstractions should not depend on details. Details should depend on abstractions.**

That means:

* High-level logic (big-picture features) shouldn’t be tightly coupled to specific implementations.  
* Instead, both depend on interfaces/abstract classes, so swapping implementations is easy.

Why It Matters

* Prevents “hard-wiring” your system to specific classes (e.g., only Eagle sounds).  
* Makes it easy to swap or extend functionality (plug in a Condor, Parrot, or Duck later).  
* Enables unit testing (you can mock interfaces instead of needing real birds).

Bird Example: Without DIP

```java
// ❌ High-level class depends on concrete class
class BirdNotifier {
    private Eagle eagle;  // tightly coupled

    public BirdNotifier() {
        this.eagle = new Eagle();
    }

    public void alert() {
        System.out.println("Eagle says: Screech!");
    }
}

class Eagle {
    public void makeSound() {
        System.out.println("Screech!");
    }
}
```

❌ Problems:

* BirdNotifier can only ever use Eagle.  
* If we want a Condor, we must modify BirdNotifier.  
* Breaks Open/Closed and Dependency Inversion.

Bird Example: With DIP

Introduce an abstraction (BirdSound) that both Eagle and Condor can implement:

```java
// Abstraction
interface BirdSound {
    void makeSound();
}

// Low-level implementations
class EagleSound implements BirdSound {
    public void makeSound() {
        System.out.println("Screech!");
    }
}

class CondorSound implements BirdSound {
    public void makeSound() {
        System.out.println("Croak!");
    }
}

// High-level class depends on abstraction, not concrete details
class BirdNotifier {
    private BirdSound sound;

    public BirdNotifier(BirdSound sound) {
        this.sound = sound;
    }

    public void alert() {
        sound.makeSound();
    }
}

// Usage
public class SanctuaryApp {
    public static void main(String[] args) {
        BirdNotifier eagleNotifier = new BirdNotifier(new EagleSound());
        eagleNotifier.alert();  // Screech!

        BirdNotifier condorNotifier = new BirdNotifier(new CondorSound());
        condorNotifier.alert(); // Croak!
    }
}
```

✅ Advantages:

BirdNotifier is no longer tied to Eagle or Condor.

* You can pass in any BirdSound implementation — even a MockBirdSound for testing.  
* Adding a new bird doesn’t require touching BirdNotifier at all.

#### **Real-World Analogy** {#real-world-analogy-4}

Think of a bird alarm system in a sanctuary:

* Instead of wiring the system directly to a specific eagle, you design it to accept any bird sound.  
* That way, you can “plug in” different species without changing the wiring.

#### **Summary** {#summary-4}

* Without DIP → High-level BirdNotifier directly depends on Eagle.  
* With DIP → Both BirdNotifier (high-level) and EagleSound/CondorSound (low-level) depend on BirdSound (abstraction).  
* This makes the design flexible, extensible, and testable.

## **Object-Oriented design** {#object-oriented-design}


The main concepts of Object-Oriented Programming (OOP) are:

### **Encapsulation** {#encapsulation}

Bundling data (attributes) and the methods that operate on that data into a single unit, a class. It hides the internal state of an object from the outside world and only allows access through a public interface.

**Definition**: Wrapping attributes (fields) and behaviors (methods) into one class, and controlling access with visibility modifiers (private, protected, public).

**Example**:  
The Bird class has a name field that is private, and we access it through a getter (getName()), not directly.

```java
private String name;
public String getName() { return name; }
```

This keeps the internal state safe and controlled.

### **Inheritance** {#inheritance}

A mechanism that allows a new class (subclass) to inherit properties and behaviors (methods) from an existing class (superclass). This promotes code reuse and establishes a hierarchical relationship between classes.

**Definition**: A way to form new classes from existing ones, reusing attributes and methods.

**Example**:  
Eagle and Condor inherit from Bird. They automatically get name and can use getName() without redefining them.

```java
class Eagle extends Bird { ... }
class Condor extends Bird { ... }
```

This prevents repeating common bird logic in every subclass.

### **Polymorphism** {#polymorphism}

The ability of an object to take on many forms. It allows a single interface to be used for different data types. For example, a single method name can be used to perform different tasks depending on the object it's called on.

**Definition**: The ability for the same method or interface to take different forms depending on the object.

**Example:**  
Both Eagle and Condor override fly() and makeSound().  
At runtime, the method called depends on the object type, even if we reference them as Bird:

```java
Bird eagle = new Eagle("Golden Eagle");
Bird condor = new Condor("Andean Condor");

eagle.fly();   // Eagle's version
condor.fly();  // Condor's version
```

Same method names, different behaviors.

### **Abstraction** {#abstraction}

The process of hiding complex implementation details and showing only the essential features of an object. It focuses on what an object does rather than how it does it. This simplifies the complexity of a system for the user.

**Definition**: Showing only essential details while hiding implementation.

In your example:

Bird is abstract and defines what a bird does (e.g., makeSound()) without saying how.  
Each subclass provides the implementation:

```java
public abstract void makeSound();
```

Users of Bird don’t care how each bird makes a sound, only that it can.

### **Interfaces** {#interfaces}

An interface in Java is a reference type that defines a **contract** for classes. It specifies what methods a class must implement without providing the actual implementation (except for default and static methods).

When to use interfaces

* When you want to define a contract that multiple unrelated classes can follow  
* When you need multiple inheritance capabilities  
* When you want to define behavior that can be implemented by any class  
* When you want to create pluggable components  
* When you're designing APIs or frameworks

### **Abstract** {#abstract}

An abstract class in Java is a class that cannot be instantiated directly and is designed to be a base class for other classes. It can contain both abstract methods (methods without implementation) and concrete methods (methods with implementation). It serves as a blueprint for related classes.

When to use Abstract classes

* When you have related classes that share common functionality  
* When you want to provide some default behavior while forcing specific implementations  
* When you need to maintain state (instance variables) in the base class  
* When you have a clear "is-a" relationship between classes  
* When you want to control the inheritance hierarchy  
* When you need constructors in your base class  
* When you want to provide common utility methods to subclasses

### **Interface and Abstract example** {#interface-and-abstract-example}

```java
// The Flyable interface
interface Flyable {
    void fly(); // contract: all flyable creatures must implement fly()
}

// The abstract Bird class
abstract class Bird implements Flyable {
    private String name;

    public Bird(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    // Abstract method: each bird has its own sound
    public abstract void makeSound();
}

// Condor class
class Condor extends Bird {
    public Condor(String name) {
        super(name);
    }

    @Override
    public void fly() {
        System.out.println(getName() + " soars slowly at high altitudes.");
    }

    @Override
    public void makeSound() {
        System.out.println(getName() + " makes a hissing sound.");
    }
}

// Eagle class
class Eagle extends Bird {
    public Eagle(String name) {
        super(name);
    }

    @Override
    public void fly() {
        System.out.println(getName() + " flies swiftly and powerfully.");
    }

    @Override
    public void makeSound() {
        System.out.println(getName() + " screeches loudly.");
    }
}

// Example usage
public class Main {
    public static void main(String[] args) {
        Bird condor = new Condor("Andean Condor");
        Bird eagle = new Eagle("Golden Eagle");

        condor.fly();
        condor.makeSound();

        eagle.fly();
        eagle.makeSound();
    }
}
```

```text
Andean Condor soars slowly at high altitudes.
Andean Condor makes a hissing sound.
Golden Eagle flies swiftly and powerfully.
Golden Eagle screeches loudly.
```

## **Leetcode**  {#leetcode}

## **Java Collections Framework** {#java-collections-framework}

Main Ideas

* List: Ordered, allows duplicates  
* Set: No duplicates  
* Queue / Deque: FIFO, LIFO, or priority-based retrieval  
* Map: Key-value pairs (not a Collection, but related)

### **List (ordered, index-based, allows duplicates)** {#list-(ordered,-index-based,-allows-duplicates)}

#### **ArrayList** {#arraylist}

Resizable array. Fast access. Slower middle inserts/removals.

#### **LinkedList** {#linkedlist}

Nodes linked. Fast inserts/removals. Slow access by index.

#### **Vector** {#vector}

Thread-safe ArrayList (legacy).

#### **Stack** {#stack}

Legacy stack. Use ArrayDeque instead.

### **Set (unique elements, no duplicates)** {#set-(unique-elements,-no-duplicates)}

#### **HashSet** {#hashset}

Fast operations. No order guaranteed.

#### **LinkedHashSet** {#linkedhashset}

Keeps insertion order.

#### **TreeSet** {#treeset}

Automatically sorted elements. Based on Red-Black Tree.

### **Queue / Deque (FIFO or double-ended, or priority)** {#queue-/-deque-(fifo-or-double-ended,-or-priority)}

#### **ArrayDeque** {#arraydeque}

Recommended queue or stack replacement. Very fast.

#### **PriorityQueue** {#priorityqueue}

Min-heap by default. Removes smallest first.

#### **LinkedList** {#linkedlist-1}

Can act as queue/deque. Slower than ArrayDeque.

*Deque \= double-ended queue (add and remove from both ends)*

### **Map (key-value lookup)** {#map-(key-value-lookup)}

#### **HashMap** {#hashmap}

Fast lookup. No order guaranteed.

#### **LinkedHashMap** {#linkedhashmap}

Maintains insertion order.

#### **TreeMap** {#treemap}

Keys sorted automatically. Red-Black Tree.

#### **Hashtable** {#hashtable}

Old synchronized map. Avoid in new code.

#### **ConcurrentHashMap** {#concurrenthashmap}

Thread-safe and efficient for concurrency.

### **Quick Selection Guide** {#quick-selection-guide}

Just read down until one matches:

* I want **fast lookups by index** → **ArrayList**  
* I want **fast inserts/removals anywhere** → **LinkedList**  
* I want **no duplicates** → **HashSet**  
* I want **no duplicates \+ sorted** → **TreeSet**  
* I want a **queue** → **ArrayDeque**  
* I want a **stack** → **ArrayDeque** (not `Stack`)  
* I want to always remove **smallest/largest item** → **PriorityQueue**  
* I want **key-value storage** → **HashMap**  
* I want key-value but **sorted keys** → **TreeMap**

