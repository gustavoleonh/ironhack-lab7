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

public class Slider implements UIElement {
    @Override
    public void render() {
        System.out.println("Render a slider");
    }
}

public class UIElementFactory {
    public static UIElement createUIElement(String type) {
        switch (type) {
            case "button":
                return new Button();
            case "slider":
                return new Slider();
            default:
                throw new IllegalArgumentException("Unknown UI element type");
        }
    }
}
```
<br>
<br>
<br>
<span class="colour" style="color:rgb(204, 204, 204)">**c) State Change Notification Across System Components:**</span>
**Solution:** Use the Observer Pattern. This pattern defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

<br>
``` java
import java.util.ArrayList;
import java.util.List;

public class Device {
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

    private void notifyAllObservers() {
        for (Observer observer : observers) {
            observer.update();
        }
    }
}

public abstract class Observer {
    protected Device device;
    public abstract void update();
}

public class MobileApp extends Observer {
    public MobileApp(Device device) {
        this.device = device;
        this.device.attach(this);
    }

    @Override
    public void update() {
        System.out.println("Device state changed to: " + device.getState());
    }
}
```
<br>
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
            // Simulate API call to turn on the lights
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Lights turned on";
        });

        CompletableFuture<String> apiCall2 = CompletableFuture.supplyAsync(() -> {
            // Simulate API call to adjust the thermostat
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Thermostat adjusted";
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
### Project simulation and explanation how all these parts could interact together:
<br>
Suppose we are building a Smart Home Automation System. This system allows users to control and monitor various aspects of their home, such as lighting, heating, security, and appliances, through a mobile application.
<br>
* **Singleton Pattern (ConfigurationManager):** Ensures that configuration settings are managed globally without conflicts. This is crucial for consistent behavior across the system.
* **Factory Pattern (UIElementFactory):** Allows the system to create various UI elements based on user input dynamically, enhancing flexibility and user experience.
* **Observer Pattern (Device and MobileApp):** Enables real-time updates and synchronization across different components of the system without tight coupling, ensuring that state changes in devices are propagated to the mobile app and other components efficiently.
* **Future/Promise Pattern (AsyncOperations):** Manages asynchronous API calls to different smart devices, ensuring that the main application workflow is not blocked and results are handled once all operations are complete.

<span class="colour" style="color: rgb(13, 13, 13);">This integrated use of design patterns ensures that the Smart Home Automation System is robust, flexible, and scalable, capable of handling complex interactions and real-time updates effectively.</span>