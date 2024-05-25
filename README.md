# ironhack-lab7

### Design Challenges:

**Scenario Description:** Participants are provided with a series of common software design challenges. They will need to choose appropriate design patterns to solve these specific problems effectively.

<br>

* <span class="colour" style="color:rgb(204, 204, 204)">**Global Configuration Management:** Design a system that ensures a single, globally accessible configuration object without access conflicts.</span>
* <span class="colour" style="color:rgb(204, 204, 204)">**Dynamic Object Creation Based on User Input:** Implement a system to dynamically create various types of user interface elements based on user actions.</span>
* <span class="colour" style="color:rgb(204, 204, 204)">**State Change Notification Across System Components:** Ensure components are notified about changes in the state of other parts without creating tight coupling.</span>
* <span class="colour" style="color:rgb(204, 204, 204)">**Efficient Management of Asynchronous Operations:** Manage multiple asynchronous operations like API calls which need to be coordinated without blocking the main application workflow.</span>

#### Project outline simulation:

<span class="colour" style="color:rgb(204, 204, 204)">**a) Global Configuration Management:** </span>

**Solution:** Use the Singleton Pattern. This pattern ensures that a class has only one instance and provides a global point of access to it.

``` java
public class ConfigurationManager {
    private static ConfigurationManager instance;
    private Properties properties;

    private ConfigurationManager() {
        properties = new Properties();
        // Load configuration properties here
    }

    public static synchronized ConfigurationManager getInstance() {
        if (instance == null) {
            instance = new ConfigurationManager();
        }
        return instance;
    }

    public String getProperty(String key) {
        return properties.getProperty(key);
    }

    public void setProperty(String key, String value) {
        properties.setProperty(key, value);
    }
}
```

Additional in frameworks like spring boot, we have an stereotype that could be used to define config classess "@Configuration"

<span class="colour" style="color:rgb(204, 204, 204)">**b) Dynamic Object Creation Based on User Input:**</span>
**Solution:** Use the Factory Pattern. This pattern defines an interface for creating an object but lets subclasses alter the type of objects that will be created.

<br>

``` java
public interface UIElement {
    void render();
}

public class Button implements UIElement {
    @Override
    public void render() {
        System.out.println("Render a button");
    }
}

public class TextField implements UIElement {
    @Override
    public void render() {
        System.out.println("Render a text field");
    }
}

public class UIElementFactory {
    public static UIElement createUIElement(String type) {
        switch (type) {
            case "button":
                return new Button();
            case "textField":
                return new TextField();
            default:
                throw new IllegalArgumentException("Unknown UI element type");
        }
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        UIElement element = UIElementFactory.createUIElement("button");
        element.render();
    }
}
```
<br>
<br>

<span class="colour" style="color:rgb(204, 204, 204)">**c) State Change Notification Across System Components:**</span>
**Solution:** Use the Observer Pattern. This pattern defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

<br>

``` java
import java.util.ArrayList;
import java.util.List;

public class Subject {
    private List<Observer> observers = new ArrayList<>();
    private String state;

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
        notifyAllObservers();
    }

    public void attach(Observer observer) {
        observers.add(observer);
    }

    public void notifyAllObservers() {
        for (Observer observer : observers) {
            observer.update();
        }
    }
}

public abstract class Observer {
    protected Subject subject;
    public abstract void update();
}

public class ConcreteObserver extends Observer {
    public ConcreteObserver(Subject subject) {
        this.subject = subject;
        this.subject.attach(this);
    }

    @Override
    public void update() {
        System.out.println("State changed to: " + subject.getState());
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Subject subject = new Subject();

        new ConcreteObserver(subject);
        new ConcreteObserver(subject);

        subject.setState("New State");
    }
}
```
<br>

<span class="colour" style="color:rgb(204, 204, 204)">**d) Efficient Management of Asynchronous Operations:**</span>
**Solution:** Use the Future/Promise Pattern. This pattern is used for managing the results of asynchronous operations.

<br>

``` java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

public class AsyncOperations {
    public static void main(String[] args) {
        CompletableFuture<String> apiCall1 = CompletableFuture.supplyAsync(() -> {
            // Simulate API call 1
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Result from API call 1";
        });

        CompletableFuture<String> apiCall2 = CompletableFuture.supplyAsync(() -> {
            // Simulate API call 2
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Result from API call 2";
        });

        CompletableFuture<Void> combinedFuture = CompletableFuture.allOf(apiCall1, apiCall2);

        combinedFuture.thenRun(() -> {
            try {
                String result1 = apiCall1.get();
                String result2 = apiCall2.get();
                System.out.println("Results: " + result1 + ", " + result2);
            } catch (InterruptedException | ExecutionException e) {
                e.printStackTrace();
            }
        });

        // Main thread can continue to run while async operations are processed
        System.out.println("Main thread continues...");
    }
}
```
<br>

These design patterns provide robust solutions for the described scenarios, ensuring maintainability and scalability of the system.
