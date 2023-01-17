The SOLID principles are a set of five principles for object-oriented software design, which were introduced by Robert C. Martin. They are intended to make software designs more understandable, flexible, and maintainable. The SOLID principles are:

1. Single Responsibility Principle: A class should have only one reason to change.
2. Open/Closed Principle: A class should be open for extension but closed for modification.
3. Liskov Substitution Principle: Derived classes should be substitutable for their base classes.
4. Interface Segregation Principle: A class should not be forced to implement interfaces it does not use.
5. Dependency Inversion Principle: High-level modules should not depend on low-level modules; both should depend on abstractions.


1. Single Responsibility Principle: A class should have only one reason to change.

```
class Customer {
    private String name;

    public Customer(String name) {
        this.name = name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

class Order {
    private Customer customer;

    public Order(Customer customer) {
        this.customer = customer;
    }

    public void setCustomer(Customer customer) {
        this.customer = customer;
    }

    public Customer getCustomer() {
        return customer;
    }
}


```
In this example, the Customer class has only one responsibility, which is to hold and manipulate customer data. The Order class has only one responsibility, which is to hold and manipulate order data.

2. Open/Closed Principle: A class should be open for extension but closed for modification.

```
abstract class Shape {
    abstract double area();
}

class Rectangle extends Shape {
    private double width;
    private double height;

    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    @Override
    double area() {
        return width * height;
    }
}

class Circle extends Shape {
    private double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * radius * radius;
    }
}


```

In this example, the Shape class defines an area method that is implemented by the Rectangle and Circle classes. These classes can be extended to add more functionality without modifying the existing code.

3. Liskov Substitution Principle: Derived types should be substitutable for their base types.

```
class Animal {
    private String name;

    public Animal(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public String speak() {
        return "Animals can't speak";
    }
}

class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }

    @Override
    public String speak() {
        return "Woof!";
    }
}


```


In this example, the Animal class has a speak method that returns a default message. The Dog class extends the Animals class and overrides its speak method to return a specific message. This allows the Dog class to be used as a substitute for the Animals class without causing any issues.


4. Interface Segregation Principle: A class should not be forced to implement interfaces it does not use.

```
interface Engine {
    void start();
    void stop();
}


class Car {
    private Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }

    public void start() {
        engine.start();
    }

    public void stop() {
        engine.stop();
    }
}

class ElectricEngine implements Engine {
    public void start() {
        // Electric engine start code
    }

    public void stop() {
        // Electric engine stop code
    }
}

class GasEngine implements Engine {
    public void start() {
        // Gas engine start code
    }

    public void stop() {
        // Gas engine stop code
    }
}

class HybridEngine implements Engine {
    private ElectricEngine electricEngine;
    private GasEngine gasEngine;

    public HybridEngine(ElectricEngine electricEngine, GasEngine gasEngine) {
        this.electricEngine = electricEngine;
        this.gasEngine = gasEngine;
    }

    public void start() {
        electricEngine.start();
        gasEngine.start();
    }

    public void stop() {
        electricEngine.stop();
        gasEngine.stop();
    }
}



```


In this example, the Engine interface defines two methods, start and stop. The Car class uses this interface to start and stop the engine. The ElectricEngine and GasEngine classes implement this interface, but the HybridEngine class embeds both classes and it doesn't need to implement all the methods defined in Engine interface. This allows the HybridEngine to only implement the methods it needs and does not force it to implement unnecessary methods.


5. Dependency Inversion Principle: High-level modules should not depend on low-level modules; both should depend on abstractions.

```
interface Repository<T> {
    List<T> getAll();
    T getById(int id);
    void save(T t);
    void delete(int id);
}

class Model {
    private int id;
    private String name;

    // constructor, getters and setters
}

class Service {
    private Repository<Model> repository;

    public Service(Repository<Model> repository) {
        this.repository = repository;
    }

    public List<Model> getAll() {
        return repository.getAll();
    }

    public Model getById(int id) {
        return repository.getById(id);
    }

    public void save(Model model) {
        repository.save(model);
    }

    public void delete(int id) {
        repository.delete(id);
    }
}

```

In this example, the Repository interface defines methods for getting, saving, and deleting models. The Model class represents the data models. The Service class depends on the Repository interface and not on any specific implementation of the repository. This allows the Service class to be easily tested and reused with different repository implementations without changing the code.



