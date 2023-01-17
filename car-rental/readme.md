


```
import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;

public class CarRentalSystem {
    public static void main(String[] args) {
      CarRental carRental = new CarRental();
      carRental.addCar(new Car("Toyota", "Camry", 2020, 50));
      carRental.addCar(new Car("Honda", "Civic", 2019, 40));
      carRental.addCar(new Car("Ford", "Mustang", 2018, 60));
      carRental.addCar(new Car("Chevrolet", "Cruze", 2018, 45));
      carRental.addCar(new Car("Nissan", "Altima", 2017, 35));

      System.out.println("Available Cars: ");
      List<Car> availableCars = carRental.getAvailableCars();
      for (Car car : availableCars) {
          System.out.println(car.getMake() + " " + car.getModel() + " " + car.getYear());
      }

      // Test case 1: Renting a car
      Car selectedCar = availableCars.get(0);
      LocalDate startDate = LocalDate.of(2022, 12, 1);
      LocalDate endDate = LocalDate.of(2022, 12, 5);
      Rental rental = carRental.rentCar(selectedCar, startDate, endDate);
      if (rental != null) {
          System.out.println("Rented " + rental.getCar().getMake() + " " + rental.getCar().getModel() + " for $" + rental.getPrice());
      } else {
          System.out.println("The selected car is not available for rental.");
      }

      // Test case 2: Returning a rented car
      rental.returnCar();
      System.out.println("Returned " + rental.getCar().getMake() + " " + rental.getCar().getModel());

      // Test case 3: Renting a car that is not available
      LocalDate startDate2 = LocalDate.of(2022, 12, 6);
      LocalDate endDate2 = LocalDate.of(2022, 12, 10);
      Rental rental2 = carRental.rentCar(selectedCar, startDate2, endDate2);
      if (rental2 != null) {
          System.out.println("Rented " + rental2.getCar().getMake() + " " + rental2.getCar().getModel() + " for $" + rental2.getPrice());
      } else {
          System.out.println("The selected car is not available for rental.");
      }
  }

}

class Car {
    private String make;
    private String model;
    private int year;
    private boolean available;
    private double pricePerDay;

    public Car(String make, String model, int year, double pricePerDay) {
        this.make = make;
        this.model = model;
        this.year = year;
        this.available = true;
        this.pricePerDay = pricePerDay;
    }

    public String getMake() {
        return make;
    }

    public String getModel() {
        return model;
    }

    public int getYear() {
        return year;
    }

    public boolean isAvailable() {
        return available;
    }

    public void setAvailable(boolean available) {
        this.available = available;
    }

    public double getPricePerDay() {
        return pricePerDay;
    }

    public void setPricePerDay(double pricePerDay) {
        this.pricePerDay = pricePerDay;
    }
}

class Rental {
    private Car car;
    private LocalDate startDate;
    private LocalDate endDate;
    private double price;

    public Rental(Car car, LocalDate startDate, LocalDate endDate) {
        this.car = car;
        this.startDate = startDate;
        this.endDate = endDate;
        car.setAvailable(false);
        long numDays = ChronoUnit.DAYS.between(startDate, endDate);
        this.price = numDays * car.getPricePerDay();
    }

    public Car getCar() {
        return car;
    }

    public LocalDate getStartDate() {
        return startDate;
    }

    public LocalDate getEndDate() {
      return endDate;
    }
    
        public double getPrice() {
        return price;
    }

    public void returnCar() {
        car.setAvailable(true);
    }
}

class CarRental {
    private List<Car> cars;

    public CarRental() {
        cars = new ArrayList<Car>();
    }

    public void addCar(Car car) {
        cars.add(car);
    }

    public List<Car> getAvailableCars() {
        List<Car> availableCars = new ArrayList<Car>();
        for (Car car : cars) {
            if (car.isAvailable()) {
                availableCars.add(car);
            }
        }
        return availableCars;
    }

    public Rental rentCar(Car car, LocalDate startDate, LocalDate endDate) {
        if (car.isAvailable()) {
            Rental rental = new Rental(car, startDate, endDate);
            return rental;
        }
        return null;
    }
}



```
The code above creates classes for a Car, Rental, and CarRental. The Car class holds the information of a car, such as make, model, year and pricePerDay. Rental class contains information of a rental such as the car being rented, start date, end date and price. The CarRental class contains a list of all cars available and methods to rent a car, return a car, get the available cars and add a new car to the system

