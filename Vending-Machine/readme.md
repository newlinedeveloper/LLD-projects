### Low-Level Design (LLD) for a Vending Machine

#### Requirements:
- The vending machine should allow users to select products, insert coins, and dispense products.
- It should manage inventory, return change, and handle insufficient funds.
- Products can have different prices.
- The machine should return the inserted coins if the transaction is canceled.

#### Core Components:
1. **VendingMachine**: Main interface for the machine.
2. **Product**: Represents a product in the machine.
3. **Inventory**: Manages stock of products.
4. **Coin**: Represents different coin denominations.
5. **Display**: Shows messages to the user.
6. **Transaction**: Manages the current transaction.

#### Design Pattern:
We'll use the **State Pattern** to handle the different states of the vending machine (e.g., waiting for selection, waiting for coins, dispensing product).

---

### Class Diagram:

```plaintext
VendingMachine
  - inventory: Inventory
  - current_state: State
  - balance: int
  + select_product(code: str)
  + insert_coin(coin: Coin)
  + dispense_product()
  + cancel_transaction()

Inventory
  - products: dict of Product to quantity
  + add_product(product: Product, quantity: int)
  + get_product(code: str): Product
  + reduce_quantity(product: Product)

Product
  - name: str
  - code: str
  - price: int

Coin
  - value: int
  + validate_coin(coin: Coin): bool

State (abstract)
  + select_product(machine: VendingMachine, code: str)
  + insert_coin(machine: VendingMachine, coin: Coin)
  + dispense_product(machine: VendingMachine)
  + cancel_transaction(machine: VendingMachine)

WaitingForSelection (inherits from State)
WaitingForCoins (inherits from State)
DispensingProduct (inherits from State)
```

---

### Golang Code

```go
package main

import "fmt"

// Coin struct
type Coin struct {
	Value int
}

// Product struct
type Product struct {
	Name  string
	Code  string
	Price int
}

// Inventory struct
type Inventory struct {
	products map[string]*Product
	quantity map[string]int
}

func (i *Inventory) AddProduct(product *Product, quantity int) {
	i.products[product.Code] = product
	i.quantity[product.Code] = quantity
}

func (i *Inventory) GetProduct(code string) *Product {
	return i.products[code]
}

func (i *Inventory) ReduceQuantity(product *Product) {
	i.quantity[product.Code]--
}

// State interface
type State interface {
	SelectProduct(machine *VendingMachine, code string)
	InsertCoin(machine *VendingMachine, coin Coin)
	DispenseProduct(machine *VendingMachine)
	CancelTransaction(machine *VendingMachine)
}

// VendingMachine struct
type VendingMachine struct {
	Inventory   *Inventory
	CurrentState State
	Balance      int
}

func (vm *VendingMachine) SetState(state State) {
	vm.CurrentState = state
}

func (vm *VendingMachine) SelectProduct(code string) {
	vm.CurrentState.SelectProduct(vm, code)
}

func (vm *VendingMachine) InsertCoin(coin Coin) {
	vm.CurrentState.InsertCoin(vm, coin)
}

func (vm *VendingMachine) DispenseProduct() {
	vm.CurrentState.DispenseProduct(vm)
}

func (vm *VendingMachine) CancelTransaction() {
	vm.CurrentState.CancelTransaction(vm)
}

// WaitingForSelection state
type WaitingForSelection struct{}

func (wfs *WaitingForSelection) SelectProduct(machine *VendingMachine, code string) {
	product := machine.Inventory.GetProduct(code)
	if product != nil {
		fmt.Printf("Selected product: %s\n", product.Name)
		machine.SetState(&WaitingForCoins{})
	} else {
		fmt.Println("Product not found.")
	}
}

func (wfs *WaitingForSelection) InsertCoin(machine *VendingMachine, coin Coin) {
	fmt.Println("Please select a product first.")
}

func (wfs *WaitingForSelection) DispenseProduct(machine *VendingMachine) {
	fmt.Println("Please select a product first.")
}

func (wfs *WaitingForSelection) CancelTransaction(machine *VendingMachine) {
	fmt.Println("No transaction to cancel.")
}

// WaitingForCoins state
type WaitingForCoins struct{}

func (wfc *WaitingForCoins) SelectProduct(machine *VendingMachine, code string) {
	fmt.Println("Product already selected. Please insert coins.")
}

func (wfc *WaitingForCoins) InsertCoin(machine *VendingMachine, coin Coin) {
	machine.Balance += coin.Value
	fmt.Printf("Inserted coin: %d, Current balance: %d\n", coin.Value, machine.Balance)
	if machine.Balance >= machine.Inventory.GetProduct(machine.Inventory.products).Price {
		machine.SetState(&DispensingProduct{})
	}
}

func (wfc *WaitingForCoins) DispenseProduct(machine *VendingMachine) {
	fmt.Println("Please insert more coins.")
}

func (wfc *WaitingForCoins) CancelTransaction(machine *VendingMachine) {
	fmt.Println("Transaction canceled. Returning coins.")
	machine.Balance = 0
	machine.SetState(&WaitingForSelection{})
}

// DispensingProduct state
type DispensingProduct struct{}

func (dp *DispensingProduct) SelectProduct(machine *VendingMachine, code string) {
	fmt.Println("Dispensing product. Please wait.")
}

func (dp *DispensingProduct) InsertCoin(machine *VendingMachine, coin Coin) {
	fmt.Println("Dispensing product. Please wait.")
}

func (dp *DispensingProduct) DispenseProduct(machine *VendingMachine) {
	product := machine.Inventory.GetProduct(machine.Inventory.products)
	if product != nil {
		machine.Inventory.ReduceQuantity(product)
		fmt.Printf("Dispensed product: %s\n", product.Name)
		machine.Balance -= product.Price
		fmt.Printf("Returning change: %d\n", machine.Balance)
		machine.Balance = 0
		machine.SetState(&WaitingForSelection{})
	}
}

func (dp *DispensingProduct) CancelTransaction(machine *VendingMachine) {
	fmt.Println("Dispensing product. Please wait.")
}

func main() {
	inventory := &Inventory{products: make(map[string]*Product), quantity: make(map[string]int)}
	inventory.AddProduct(&Product{Name: "Soda", Code: "A1", Price: 25}, 10)

	vendingMachine := &VendingMachine{Inventory: inventory}
	vendingMachine.SetState(&WaitingForSelection{})

	vendingMachine.SelectProduct("A1")
	vendingMachine.InsertCoin(Coin{Value: 10})
	vendingMachine.InsertCoin(Coin{Value: 10})
	vendingMachine.InsertCoin(Coin{Value: 10})
	vendingMachine.DispenseProduct()
}
```

---

### Python Code

```python
class Coin:
    def __init__(self, value):
        self.value = value

class Product:
    def __init__(self, name, code, price):
        self.name = name
        self.code = code
        self.price = price

class Inventory:
    def __init__(self):
        self.products = {}
        self.quantity = {}

    def add_product(self, product, quantity):
        self.products[product.code] = product
        self.quantity[product.code] = quantity

    def get_product(self, code):
        return self.products.get(code)

    def reduce_quantity(self, product):
        self.quantity[product.code] -= 1

class State:
    def select_product(self, machine, code):
        pass

    def insert_coin(self, machine, coin):
        pass

    def dispense_product(self, machine):
        pass

    def cancel_transaction(self, machine):
        pass

class WaitingForSelection(State):
    def select_product(self, machine, code):
        product = machine.inventory.get_product(code)
        if product:
            print(f"Selected product: {product.name}")
            machine.set_state(WaitingForCoins())
        else:
            print("Product not found.")

    def insert_coin(self, machine, coin):
        print("Please select a product first.")

    def dispense_product(self, machine):
        print("Please select a product first.")

    def cancel_transaction(self, machine):
        print("No transaction to cancel.")

class WaitingForCoins(State):
    def select_product(self, machine, code):
        print("Product already selected. Please insert coins.")

    def insert_coin(self, machine, coin):
        machine.balance += coin.value
        print(f"Inserted coin: {coin.value}, Current balance: {machine.balance}")
        if machine.balance >= machine.current_product.price:
            machine.set_state(DispensingProduct())

    def dispense_product(self, machine):
        print("Please insert more coins.")

    def cancel_transaction(self, machine):
        print("Transaction canceled. Returning coins.")
        machine.balance = 0
        machine.set_state(WaitingForSelection())

class DispensingProduct(State):
    def select_product(self, machine, code):
        print("Dispensing product. Please wait.")

    def insert_coin(self, machine, coin):
        print("Dispensing product. Please wait.")

    def dispense_product(self, machine):
        product = machine.current_product
        if product:
            machine.inventory.reduce_quantity(product)
            print(f"Dispensed product: {product.name}")
            change = machine.balance - product.price
            print(f"Returning change: {change}")
            machine.balance = 0
            machine.set_state(WaitingForSelection())

    def cancel_transaction(self, machine):
        print("Dispensing product. Please wait.")

class VendingMachine:
    def __init__(self):
        self.inventory = Inventory()
        self.current_state = WaitingForSelection()
        self.balance = 0
        self.current_product = None

    def set_state(self, state):
        self.current_state = state

    def select_product(self, code):
        self.current_product = self.inventory.get_product(code)
        self.current_state.select_product(self, code)

    def insert_coin(self, coin):
        self.current_state.insert_coin(self, coin)

    def dispense_product(self):
        self.current_state.dispense_product(self)

    def cancel_transaction(self):
        self.current_state.cancel_transaction(self)

# Example usage
vending_machine = VendingMachine()
vending_machine.inventory.add_product(Product("Soda", "A1", 25), 10)

vending_machine.select_product("A1")
vending_machine.insert_coin(Coin(10))
vending_machine.insert_coin(Coin(10))
vending_machine.insert_coin(Coin(10))
vending_machine.dispense_product()
```

