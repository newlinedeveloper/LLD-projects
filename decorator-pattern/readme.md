The Decorator Design Pattern is a structural design pattern that allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class. The pattern creates a decorator class that wraps the original class, adding new behavior before or after the method call of the original class.

In Java, this can be achieved by using interfaces and classes to define the original class and the decorator classes. Here's an example of how to implement the Decorator Design Pattern in Java:

```
interface Beverage {
    double cost();
}

class Espresso implements Beverage {
    public double cost() {
        return 1.99;
    }
}

abstract class CondimentDecorator implements Beverage {
    protected Beverage beverage;

    public CondimentDecorator(Beverage beverage) {
        this.beverage = beverage;
    }

    public abstract double cost();
}

class Mocha extends CondimentDecorator {
    public Mocha(Beverage beverage) {
        super(beverage);
    }

    public double cost() {
        return 0.20 + beverage.cost();
    }
}


```

In this example, the Beverage interface defines the cost method. The Espresso class implements the Beverage interface and provides the base cost of the espresso. The CondimentDecorator class is an abstract class that implements the Beverage interface and holds a reference to the Beverage object. The Mocha class is a concrete decorator class that adds its own behavior (the cost of a Mocha) to the base behavior (the cost of the espresso) by calling the cost method of the Beverage object it decorates.

The decorator pattern allows the client code to create different combinations of decorators and the original class by instantiating the decorator classes and passing the original class or the previously decorated class as an argument in the constructor. For example, the following code creates an Espresso with Mocha and another Espresso with Mocha and Whip:

```
Beverage espresso = new Espresso();
System.out.println("Espresso: $" + espresso.cost());

Beverage espressoMocha = new Mocha(espresso);
System.out.println("Espresso Mocha: $" + espressoMocha.cost());

Beverage espressoMochaWhip = new Whip(espressoMocha);
System.out.println("Espresso Mocha Whip: $" + espressoMochaWhip.cost());


```

The Decorator Design Pattern is a powerful pattern that allows for flexibility and maintainability in the code by adding new behavior to an individual object without changing the code of the original class. It also allows for dynamic updates and easy to add new behaviors in the future. This pattern is widely used in various areas such as GUI, I/O stream and other systems that require dynamic updates and adding new behavior to a class.



