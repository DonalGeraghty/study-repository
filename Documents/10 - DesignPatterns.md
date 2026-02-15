# Design Patterns

## **Factory Pattern** {#factory-pattern}

The Factory Pattern is a powerful creational design pattern that should be used primarily because it promotes loose coupling and adheres to the **Open/Closed Principle (OCP)**. By delegating object instantiation to a specialized factory class, the client code interacts only with a common **interface** (like Bird), never with the concrete classes (Eagle or Condor). This separation means that if you need to introduce a new bird type, **you only modify the factory**—the client code that uses the birds remains untouched. This dramatically increases the maintainability and flexibility of the code, making it easier to manage complex object creation logic and scale the application without breaking existing functionality.

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

## **Page Object Model** {#page-object-model}

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
