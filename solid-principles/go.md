The SOLID principles are a set of five design principles for writing maintainable and scalable software. These principles were introduced by Robert C. Martin and are widely used in object-oriented programming. While Go is not a purely object-oriented language, these principles can still be applied to write clean and maintainable Go code. Here's how you can apply each of the SOLID principles in Go:

1. **Single Responsibility Principle (SRP):**
   - Each Go function or method should have a single responsibility. It should do one thing and do it well.
   - Avoid functions or methods that perform multiple unrelated tasks.
   - Example: Break down a large function into smaller functions, each with a specific responsibility.

2. **Open/Closed Principle (OCP):**
   - Go types (structs and interfaces) should be open for extension but closed for modification.
   - You can achieve this by using interfaces and struct composition.
   - Example: Define interfaces and implement new functionality by creating new types that satisfy those interfaces instead of modifying existing code.

3. **Liskov Substitution Principle (LSP):**
   - If a type implements an interface, it should be able to substitute any other type that implements the same interface without causing issues.
   - Ensure that subtypes do not violate the expected behavior of the supertype.
   - Example: When using interfaces, make sure that all implementations adhere to the same contract.

4. **Interface Segregation Principle (ISP):**
   - Clients should not be forced to depend on interfaces they do not use.
   - Define smaller, focused interfaces instead of large, monolithic ones.
   - Example: Instead of having a large interface with many methods, define smaller interfaces specific to each client's needs.

5. **Dependency Inversion Principle (DIP):**
   - High-level modules should not depend on low-level modules. Both should depend on abstractions.
   - Abstractions should not depend on details; details should depend on abstractions.
   - Example: Use dependency injection to provide dependencies to a component rather than hardcoding them.

Here's a simple example in Go that demonstrates these principles:

```go
package main

import (
	"fmt"
)

// Single Responsibility Principle
type UserService struct {
	userRepository UserRepository
}

func (us *UserService) GetUserByID(id int) User {
	return us.userRepository.FindByID(id)
}

// Open/Closed Principle
type UserRepository interface {
	FindByID(id int) User
}

type MySQLUserRepository struct{}

func (r *MySQLUserRepository) FindByID(id int) User {
	// Query the database
	return User{Name: "John", ID: id}
}

// Liskov Substitution Principle
type User struct {
	ID   int
	Name string
}

func PrintUserDetails(u User) {
	fmt.Printf("User ID: %d, Name: %s\n", u.ID, u.Name)
}

// Interface Segregation Principle
type CanPrintUserDetails interface {
	PrintDetails()
}

func (u User) PrintDetails() {
	fmt.Printf("User ID: %d, Name: %s\n", u.ID, u.Name)
}

func main() {
	// Dependency Inversion Principle
	userRepository := &MySQLUserRepository{}
	userService := &UserService{userRepository}

	user := userService.GetUserByID(123)
	PrintUserDetails(user)

	// Applying Interface Segregation Principle
	var printable CanPrintUserDetails = user
	printable.PrintDetails()
}
```
Certainly! Here are separate examples for each of the SOLID principles in Go:

1. **Single Responsibility Principle (SRP):**
   ```go
   package main

   import "fmt"

   // User represents a user entity.
   type User struct {
       ID   int
       Name string
   }

   // UserService handles user-related operations.
   type UserService struct{}

   // CreateUser creates a new user.
   func (us *UserService) CreateUser(name string) *User {
       // Logic to create a user in the database.
       user := &User{Name: name}
       return user
   }

   // UserPrinter prints user details.
   type UserPrinter struct{}

   // PrintUser prints user details.
   func (up *UserPrinter) PrintUser(user *User) {
       fmt.Printf("User ID: %d, Name: %s\n", user.ID, user.Name)
   }

   func main() {
       userService := &UserService{}
       user := userService.CreateUser("John")

       userPrinter := &UserPrinter{}
       userPrinter.PrintUser(user)
   }
   ```

2. **Open/Closed Principle (OCP):**
   ```go
   package main

   import "fmt"

   // Not following OCP (open for modification)
   type Circle struct {
       Radius float64
   }

   func (c *Circle) Area() float64 {
       return 3.14 * c.Radius * c.Radius
   }

   // Extending to support a new shape (modification)
   type Square struct {
       SideLength float64
   }

   func (s *Square) Area() float64 {
       return s.SideLength * s.SideLength
   }

   // Following OCP (open for extension)
   type Shape interface {
       Area() float64
   }

   func CalculateArea(shape Shape) float64 {
       return shape.Area()
   }

   func main() {
       circle := &Circle{Radius: 5.0}
       square := &Square{SideLength: 4.0}

       fmt.Println("Circle Area:", CalculateArea(circle))
       fmt.Println("Square Area:", CalculateArea(square))
   }
   ```

3. **Liskov Substitution Principle (LSP):**
   ```go
   package main

   import "fmt"

   // LSP violation: Square is not substitutable for Rectangle
   type Rectangle struct {
       Width  float64
       Height float64
   }

   func (r *Rectangle) Area() float64 {
       return r.Width * r.Height
   }

   type Square struct {
       SideLength float64
   }

   func (s *Square) Area() float64 {
       return s.SideLength * s.SideLength
   }

   func main() {
       rectangle := &Rectangle{Width: 5.0, Height: 4.0}
       square := &Square{SideLength: 4.0}

       fmt.Println("Rectangle Area:", rectangle.Area())
       fmt.Println("Square Area:", square.Area())
   }
   ```

4. **Interface Segregation Principle (ISP):**
   ```go
   package main

   import "fmt"

   // Not following ISP (large interface)
   type Worker interface {
       Work()
       Eat()
   }

   // Following ISP (smaller, focused interfaces)
   type Workable interface {
       Work()
   }

   type Eatable interface {
       Eat()
   }

   type Engineer struct{}

   func (e *Engineer) Work() {
       fmt.Println("Engineer is working.")
   }

   func (e *Engineer) Eat() {
       fmt.Println("Engineer is eating.")
   }

   func main() {
       engineer := &Engineer{}
       engineer.Work()
       engineer.Eat()
   }
   ```

5. **Dependency Inversion Principle (DIP):**
   ```go
   package main

   import "fmt"

   // High-level module (UserService) depends on an abstraction (UserRepository)
   type UserRepository interface {
       GetUserByID(id int) string
   }

   type MySQLUserRepository struct{}

   func (r *MySQLUserRepository) GetUserByID(id int) string {
       // Query the database and return user name
       return fmt.Sprintf("User with ID %d", id)
   }

   type UserService struct {
       UserRepository UserRepository
   }

   func (us *UserService) GetUser(id int) string {
       return us.UserRepository.GetUserByID(id)
   }

   func main() {
       userRepository := &MySQLUserRepository{}
       userService := &UserService{UserRepository: userRepository}

       userName := userService.GetUser(123)
       fmt.Println(userName)
   }
   ```
   
