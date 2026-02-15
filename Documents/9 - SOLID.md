# S.O.L.I.D Principles

## **S \- Single Responsibility Principle** {#s---single-responsibility-principle}

The Single Responsibility Principle says:

**A class should have only one reason to change.**

That means:

* A class should focus on one job only.  
* If a class has multiple responsibilities (e.g., data storage, printing, persistence), it becomes harder to maintain, test, and extend.

### Why It Matters

* If a class does too many things, changes in one responsibility can break another.  
* Having separate classes for separate responsibilities makes the code easier to maintain and extend.  
* Helps with clean architecture and avoids “god classes.”

### Bird Example: Without SRP

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

### Bird Example: With SRP

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

#### **Real-World Analogy**

Think of a bird sanctuary team:

* A bird keeper looks after the bird’s well-being.  
* A scientist records data about the bird.  
* A tour guide talks about the bird to visitors.  
* You wouldn’t expect one person to do all of these jobs at once — it would be inefficient and error-prone.

#### **Summary**

* Without SRP → one Bird class does everything (data, persistence, printing).  
* With SRP → responsibilities are split: Bird, BirdRepository, BirdPrinter.  
* This makes the system modular, testable, and easier to extend.

---

## **O - Open Closed Principle** {#o---open-closed-principle}

The Open/Closed Principle says:

**A class should be open for extension but closed for modification.**

That means:

* You should be able to add new functionality (extend behavior)  
* Without having to change the existing, stable code (modify classes that are already tested and deployed).

### Bird Example: Without OCP

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

### Bird Example: With OCP

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

#### **Real-World Analogy**

Think of BirdInfoPrinter like a bird-watching guidebook:

* Instead of rewriting the whole book every time a new bird species is discovered, you just add a new bird entry card to the guide.  
* The guidebook’s structure (the printing system) doesn’t change.

#### **Summary**

* Open for extension → you can add new birds like Parrot, Falcon, Owl.  
* Closed for modification → BirdInfoPrinter doesn’t need changes when new bird types appear.  
* This keeps your codebase stable, flexible, and less error-prone as it grows.

---

## **L \- Liskov Substitution Principle** {#l---liskov-substitution-principle}

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

#### **Real-World Analogy**

Think of a bird sanctuary app:

* If you say “all birds can fly,” then adding a penguin breaks your program (like UnsupportedOperationException).  
* By respecting LSP, you model reality better: not all birds fly, so only FlyableBirds should promise fly().

#### **Summary**

* Subtypes (Eagle, Condor) can replace Bird safely → fly() always works.  
* Avoid lying subclasses (like a Penguin that throws error in fly()).  
* LSP keeps polymorphism safe and ensures your code doesn’t break when you add new types.

---

## **I \- Interface Segregation Principle** {#i---interface-segregation-principle}

The Interface Segregation Principle says:

**No client should be forced to depend on methods it does not use.**

That means:

* Instead of one fat interface with many unrelated methods, create smaller, focused interfaces.  
* Classes should only implement what makes sense for them.

### Why It Matters

* Prevents unnecessary code (empty or dummy method implementations).  
* Keeps interfaces clean and focused.  
* Makes the system more flexible — different birds can implement different capabilities without being forced into irrelevant ones.

### Bird Example: Without ISP

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

### Bird Example: With ISP

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

#### **Real-World Analogy**

Think of a bird-care system in a sanctuary:

* You wouldn’t hand every bird the same care checklist (“teach to fly, teach to swim, teach to scavenge”).  
* Instead, you’d have separate specialized routines.  
* A penguin would follow the swimming routine, not the flying one.

#### **Summary**

* Without ISP → one bloated interface forces irrelevant methods (Eagle having to “swim”).  
* With ISP → smaller, focused interfaces (Flyable, Swimmable, Scavenger) let each bird implement only what it actually does.  
* This makes code cleaner, easier to maintain, and closer to reality.

---

## **D \- Dependency Inversion Principle** {#d---dependency-inversion-principle}

The Dependency Inversion Principle says:

* **High-level modules should not depend on low-level modules. Both should depend on abstractions.**  
* **Abstractions should not depend on details. Details should depend on abstractions.**

That means:

* High-level logic (big-picture features) shouldn’t be tightly coupled to specific implementations.  
* Instead, both depend on interfaces/abstract classes, so swapping implementations is easy.

### Why It Matters

* Prevents “hard-wiring” your system to specific classes (e.g., only Eagle sounds).  
* Makes it easy to swap or extend functionality (plug in a Condor, Parrot, or Duck later).  
* Enables unit testing (you can mock interfaces instead of needing real birds).

### Bird Example: Without DIP

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

### Bird Example: With DIP

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

#### **Real-World Analogy**

Think of a bird alarm system in a sanctuary:

* Instead of wiring the system directly to a specific eagle, you design it to accept any bird sound.  
* That way, you can “plug in” different species without changing the wiring.

#### **Summary**

* Without DIP → High-level BirdNotifier directly depends on Eagle.  
* With DIP → Both BirdNotifier (high-level) and EagleSound/CondorSound (low-level) depend on BirdSound (abstraction).  
* This makes the design flexible, extensible, and testable.
