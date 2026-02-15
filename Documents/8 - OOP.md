# Object-Oriented Design

The main concepts of Object-Oriented Programming (OOP) are:

## **Encapsulation** {#encapsulation}

Bundling data (attributes) and the methods that operate on that data into a single unit, a class. It hides the internal state of an object from the outside world and only allows access through a public interface.

**Definition**: Wrapping attributes (fields) and behaviors (methods) into one class, and controlling access with visibility modifiers (private, protected, public).

**Example**:  
The Bird class has a name field that is private, and we access it through a getter (getName()), not directly.

```java
private String name;
public String getName() { return name; }
```

This keeps the internal state safe and controlled.

## **Inheritance** {#inheritance}

A mechanism that allows a new class (subclass) to inherit properties and behaviors (methods) from an existing class (superclass). This promotes code reuse and establishes a hierarchical relationship between classes.

**Definition**: A way to form new classes from existing ones, reusing attributes and methods.

**Example**:  
Eagle and Condor inherit from Bird. They automatically get name and can use getName() without redefining them.

```java
class Eagle extends Bird { ... }
class Condor extends Bird { ... }
```

This prevents repeating common bird logic in every subclass.

## **Polymorphism** {#polymorphism}

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

## **Abstraction** {#abstraction}

The process of hiding complex implementation details and showing only the essential features of an object. It focuses on what an object does rather than how it does it. This simplifies the complexity of a system for the user.

**Definition**: Showing only essential details while hiding implementation.

In your example:

Bird is abstract and defines what a bird does (e.g., makeSound()) without saying how.  
Each subclass provides the implementation:

```java
public abstract void makeSound();
```

Users of Bird don’t care how each bird makes a sound, only that it can.

## **Interfaces** {#interfaces}

An interface in Java is a reference type that defines a **contract** for classes. It specifies what methods a class must implement without providing the actual implementation (except for default and static methods).

### When to use interfaces

* When you want to define a contract that multiple unrelated classes can follow  
* When you need multiple inheritance capabilities  
* When you want to define behavior that can be implemented by any class  
* When you want to create pluggable components  
* When you're designing APIs or frameworks

## **Abstract** {#abstract}

An abstract class in Java is a class that cannot be instantiated directly and is designed to be a base class for other classes. It can contain both abstract methods (methods without implementation) and concrete methods (methods with implementation). It serves as a blueprint for related classes.

### When to use Abstract classes

* When you have related classes that share common functionality  
* When you want to provide some default behavior while forcing specific implementations  
* When you need to maintain state (instance variables) in the base class  
* When you have a clear "is-a" relationship between classes  
* When you want to control the inheritance hierarchy  
* When you need constructors in your base class  
* When you want to provide common utility methods to subclasses

## **Interface and Abstract example** {#interface-and-abstract-example}

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
