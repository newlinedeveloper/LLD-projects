### Low-Level Design (LLD) for a Parking Lot System

#### Requirements:
- The parking lot can have multiple floors and multiple parking spots per floor.
- Different types of vehicles can park (bike, car, truck).
- Each vehicle type needs a specific type of parking spot.
- The system should handle vehicle entry and exit.
- The system should charge for parking based on the time the vehicle is parked.

#### Core Components:
1. **ParkingLot**: Represents the entire parking structure.
2. **ParkingFloor**: Represents each floor in the parking lot.
3. **ParkingSpot**: Represents a parking spot on a floor.
4. **Vehicle**: Abstract class for different vehicle types.
5. **ParkingTicket**: Issued for each vehicle when it parks.
6. **ParkingFee**: Calculates the parking fee based on the duration.

#### Class Diagram:

```plaintext
ParkingLot
  - name
  - location
  - floors: list of ParkingFloor
  + park_vehicle(vehicle): ParkingTicket
  + unpark_vehicle(ticket): ParkingFee

ParkingFloor
  - number
  - spots: list of ParkingSpot
  + find_available_spot(vehicle): ParkingSpot
  + park_vehicle(vehicle, spot): ParkingTicket

ParkingSpot
  - number
  - type: SpotType
  - occupied: bool
  + is_available(): bool

Vehicle
  - license_plate
  - type: VehicleType

Car, Bike, Truck (inherits from Vehicle)

ParkingTicket
  - id
  - vehicle: Vehicle
  - spot: ParkingSpot
  - issued_at: datetime

ParkingFee
  + calculate_fee(ticket): float
```

#### Design Pattern:
We'll use the **Factory Pattern** to create different types of `ParkingSpot` and `Vehicle`.

---

### Golang Code

```go
package main

import (
	"fmt"
	"time"
)

type VehicleType string

const (
	Car   VehicleType = "Car"
	Bike  VehicleType = "Bike"
	Truck VehicleType = "Truck"
)

type Vehicle struct {
	LicensePlate string
	Type         VehicleType
}

type SpotType string

const (
	CarSpot   SpotType = "CarSpot"
	BikeSpot  SpotType = "BikeSpot"
	TruckSpot SpotType = "TruckSpot"
)

type ParkingSpot struct {
	Number    int
	Type      SpotType
	Occupied  bool
}

func (ps *ParkingSpot) IsAvailable() bool {
	return !ps.Occupied
}

type ParkingFloor struct {
	Number int
	Spots  []*ParkingSpot
}

func (pf *ParkingFloor) FindAvailableSpot(vehicle *Vehicle) *ParkingSpot {
	for _, spot := range pf.Spots {
		if spot.IsAvailable() && spot.Type == SpotType(vehicle.Type+"Spot") {
			return spot
		}
	}
	return nil
}

type ParkingTicket struct {
	ID        string
	Vehicle   *Vehicle
	Spot      *ParkingSpot
	IssuedAt  time.Time
}

type ParkingLot struct {
	Name    string
	Floors  []*ParkingFloor
}

func (pl *ParkingLot) ParkVehicle(vehicle *Vehicle) *ParkingTicket {
	for _, floor := range pl.Floors {
		spot := floor.FindAvailableSpot(vehicle)
		if spot != nil {
			spot.Occupied = true
			ticket := &ParkingTicket{
				ID:       fmt.Sprintf("T-%d", time.Now().UnixNano()),
				Vehicle:  vehicle,
				Spot:     spot,
				IssuedAt: time.Now(),
			}
			return ticket
		}
	}
	return nil
}

func main() {
	// Example Usage
	car := &Vehicle{LicensePlate: "XYZ123", Type: Car}
	spot := &ParkingSpot{Number: 1, Type: CarSpot}
	floor := &ParkingFloor{Number: 1, Spots: []*ParkingSpot{spot}}
	lot := &ParkingLot{Name: "City Center", Floors: []*ParkingFloor{floor}}

	ticket := lot.ParkVehicle(car)
	if ticket != nil {
		fmt.Printf("Vehicle parked with ticket ID: %s\n", ticket.ID)
	} else {
		fmt.Println("No available spot.")
	}
}
```

---

### Python Code

```python
from enum import Enum
import time
import uuid

class VehicleType(Enum):
    CAR = "Car"
    BIKE = "Bike"
    TRUCK = "Truck"

class SpotType(Enum):
    CAR_SPOT = "CarSpot"
    BIKE_SPOT = "BikeSpot"
    TRUCK_SPOT = "TruckSpot"

class Vehicle:
    def __init__(self, license_plate, vehicle_type):
        self.license_plate = license_plate
        self.type = vehicle_type

class ParkingSpot:
    def __init__(self, number, spot_type):
        self.number = number
        self.type = spot_type
        self.occupied = False

    def is_available(self):
        return not self.occupied

class ParkingFloor:
    def __init__(self, number):
        self.number = number
        self.spots = []

    def find_available_spot(self, vehicle):
        for spot in self.spots:
            if spot.is_available() and spot.type == SpotType[vehicle.type.name + "_SPOT"]:
                return spot
        return None

class ParkingTicket:
    def __init__(self, vehicle, spot):
        self.id = str(uuid.uuid4())
        self.vehicle = vehicle
        self.spot = spot
        self.issued_at = time.time()

class ParkingLot:
    def __init__(self, name):
        self.name = name
        self.floors = []

    def park_vehicle(self, vehicle):
        for floor in self.floors:
            spot = floor.find_available_spot(vehicle)
            if spot:
                spot.occupied = True
                ticket = ParkingTicket(vehicle, spot)
                return ticket
        return None

# Example Usage
car = Vehicle("XYZ123", VehicleType.CAR)
spot = ParkingSpot(1, SpotType.CAR_SPOT)
floor = ParkingFloor(1)
floor.spots.append(spot)
lot = ParkingLot("City Center")
lot.floors.append(floor)

ticket = lot.park_vehicle(car)
if ticket:
    print(f"Vehicle parked with ticket ID: {ticket.id}")
else:
    print("No available spot.")
```

---
