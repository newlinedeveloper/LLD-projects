# LLD-projects
Low level design concept &amp;&amp; projects


```

Design Patterns
│
├── Creational Patterns
│   ├── Singleton
│   ├── Factory Method
│   ├── Abstract Factory
│   ├── Builder
│   └── Prototype
│
├── Structural Patterns
│   ├── Adapter
│   ├── Bridge
│   ├── Composite
│   ├── Decorator
│   ├── Facade
│   ├── Flyweight
│   └── Proxy
│
└── Behavioral Patterns
    ├── Observer
    ├── Strategy
    ├── Command
    ├── Iterator
    ├── State
    ├── Chain of Responsibility
    ├── Visitor
    ├── Template Method
    ├── Memento
    ├── Mediator
    ├── Interpreter
    └── Null Object
```


#### Creational Design patterns

1. Singleton Pattern:

```go
package main

import (
	"fmt"
	"sync"
)

type Database struct {
	Name string
}

var (
	instance *Database
	once     sync.Once
)

func GetInstance() *Database {
	once.Do(func() {
		instance = &Database{Name: "MySQL"}
	})
	return instance
}

func main() {
	db1 := GetInstance()
	db2 := GetInstance()

	fmt.Println("DB1 Name:", db1.Name)
	fmt.Println("DB2 Name:", db2.Name)
}
```

Explanation:
- The singleton pattern ensures that a class has only one instance and provides a global point of access to it.
- In the example, `Database` struct represents a database connection, and `GetInstance` function returns the singleton instance of the `Database`.
- The `once` sync.Once ensures that the initialization code inside `GetInstance` is executed only once, even in a concurrent environment.
- When `GetInstance` is called for the first time, it creates a new instance of `Database`, and subsequent calls return the same instance.

2. Factory Method Pattern:

```go
package main

import "fmt"

type Vehicle interface {
	GetColor() string
}

type Car struct{}

func (c Car) GetColor() string {
	return "Red"
}

type Bike struct{}

func (b Bike) GetColor() string {
	return "Blue"
}

type VehicleFactory interface {
	Create() Vehicle
}

type CarFactory struct{}

func (cf CarFactory) Create() Vehicle {
	return Car{}
}

type BikeFactory struct{}

func (bf BikeFactory) Create() Vehicle {
	return Bike{}
}

func main() {
	carFactory := CarFactory{}
	car := carFactory.Create()
	fmt.Println("Car Color:", car.GetColor())

	bikeFactory := BikeFactory{}
	bike := bikeFactory.Create()
	fmt.Println("Bike Color:", bike.GetColor())
}
```

Explanation:
- The factory method pattern defines an interface for creating objects, but allows subclasses to alter the type of objects that will be created.
- In the example, `VehicleFactory` interface declares a method `Create` to create vehicles, and `CarFactory` and `BikeFactory` implement this interface to create cars and bikes, respectively.
- When `Create` is called on a factory instance, it returns a concrete implementation of the `Vehicle` interface, such as `Car` or `Bike`.

3. Abstract Factory Pattern:

```go
package main

import "fmt"

type Vehicle interface {
	GetColor() string
}

type Car struct{}

func (c Car) GetColor() string {
	return "Red"
}

type Bike struct{}

func (b Bike) GetColor() string {
	return "Blue"
}

type VehicleFactory interface {
	CreateCar() Vehicle
	CreateBike() Vehicle
}

type DomesticFactory struct{}

func (df DomesticFactory) CreateCar() Vehicle {
	return Car{}
}

func (df DomesticFactory) CreateBike() Vehicle {
	return Bike{}
}

type InternationalFactory struct{}

func (ifc InternationalFactory) CreateCar() Vehicle {
	return Car{}
}

func (ifc InternationalFactory) CreateBike() Vehicle {
	return Bike{}
}

func main() {
	domesticFactory := DomesticFactory{}
	domesticCar := domesticFactory.CreateCar()
	domesticBike := domesticFactory.CreateBike()
	fmt.Println("Domestic Car Color:", domesticCar.GetColor())
	fmt.Println("Domestic Bike Color:", domesticBike.GetColor())

	internationalFactory := InternationalFactory{}
	internationalCar := internationalFactory.CreateCar()
	internationalBike := internationalFactory.CreateBike()
	fmt.Println("International Car Color:", internationalCar.GetColor())
	fmt.Println("International Bike Color:", internationalBike.GetColor())
}
```

Explanation:
- The abstract factory pattern provides an interface for creating families of related or dependent objects without specifying their concrete classes.
- In the example, `VehicleFactory` interface declares methods `CreateCar` and `CreateBike` to create cars and bikes, respectively.
- `DomesticFactory` and `InternationalFactory` implement `VehicleFactory` to create domestic and international vehicles, respectively.
- Clients can use the factory to create related objects, such as domestic or international cars and bikes.

4. Builder Pattern:

```go
package main

import "fmt"

type Product struct {
	PartA, PartB, PartC string
}

type Builder interface {
	BuildPartA()
	BuildPartB()
	BuildPartC()
	GetProduct() Product
}

type ConcreteBuilder struct {
	product Product
}

func (b *ConcreteBuilder) BuildPartA() {
	b.product.PartA = "PartA"
}

func (b *ConcreteBuilder) BuildPartB() {
	b.product.PartB = "PartB"
}

func (b *ConcreteBuilder) BuildPartC() {
	b.product.PartC = "PartC"
}

func (b *ConcreteBuilder) GetProduct() Product {
	return b.product
}

type Director struct {
	builder Builder
}

func (d *Director) Construct() {
	d.builder.BuildPartA()
	d.builder.BuildPartB()
	d.builder.BuildPartC()
}

func main() {
	builder := &ConcreteBuilder{}
	director := &Director{builder: builder}
	director.Construct()
	product := builder.GetProduct()
	fmt.Printf("Product Parts: %s, %s, %s\n", product.PartA, product.PartB, product.PartC)
}
```

Explanation:
- The builder pattern separates the construction of a complex object from its representation, allowing the same construction process to create different representations.
- In the example, `Product` struct represents the complex object to be built, and `Builder` interface declares methods to build its parts.
- `ConcreteBuilder` implements `Builder` to build the parts of the product.
- `Director` directs the construction process using a builder instance.
- Clients can use the director to construct the product using a specific builder implementation.
