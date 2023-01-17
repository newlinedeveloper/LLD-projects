

```
// Elevator class
public class Elevator {
    private int currentFloor;
    private int destinationFloor;
    private boolean moving;
    private List<Integer> upQueue;
    private List<Integer> downQueue;
    private boolean maintenance;

    public Elevator() {
        currentFloor = 1;
        destinationFloor = 1;
        moving = false;
        upQueue = new ArrayList<Integer>();
        downQueue = new ArrayList<Integer>();
        maintenance = false;
    }

    public void moveUp() {
        if (!moving && !upQueue.isEmpty() && !maintenance) {
            destinationFloor = upQueue.remove(0);
            moving = true;
        }
    }

    public void moveDown() {
        if (!moving && !downQueue.isEmpty() && !maintenance) {
            destinationFloor = downQueue.remove(0);
            moving = true;
        }
    }

    public void update() {
        if (moving) {
            if (currentFloor < destinationFloor) {
                currentFloor++;
            } else if (currentFloor > destinationFloor) {
                currentFloor--
    } else {
                moving = false;
            }
        }
    }

    public void addUpRequest(int floor) {
        upQueue.add(floor);
    }

    public void addDownRequest(int floor) {
        downQueue.add(floor);
    }

    public void setMaintenance(boolean state) {
        maintenance = state;
    }

    public int getCurrentFloor() {
        return currentFloor;
    }

    public boolean isMoving() {
        return moving;
    }

    public boolean isInMaintenance() {
        return maintenance;
    }
}

// ElevatorController class
public class ElevatorController {
    private List<Elevator> elevators;

    public ElevatorController(int numElevators) {
        elevators = new ArrayList<Elevator>();
        for (int i = 0; i < numElevators; i++) {
            elevators.add(new Elevator());
        }
    }

    public void requestElevator(int floor, int destination) {
        Elevator elevator = getIdleElevator();
        if (elevator == null) {
            elevator = getClosestElevator(floor);
        }
        if (elevator != null) {
            if (destination > floor) {
                elevator.addUpRequest(destination);
            } else {
                elevator.addDownRequest(destination);
            }
        }
    }

    public void setMaintenance(int elevatorNum, boolean state) {
        elevators.get(elevatorNum).setMaintenance(state);
    }

    public Elevator getClosestElevator(int floor) {
        Elevator closestElevator = null;
        int closestDistance = Integer.MAX_VALUE;
        for (Elevator elevator : elevators) {
            if (!elevator.isInMaintenance()) {
                int distance = Math.abs(elevator.getCurrentFloor() - floor);
                if (distance < closestDistance) {
                    closestElevator = elevator;
                    closestDistance = distance;
                }
            }
        }
        return closestElevator;
    }

    public Elevator getIdleElevator() {
        for (Elevator elevator : elevators) {
            if (!elevator.isMoving() && !elevator.isInMaintenance()) {
                return elevator;
            }
        }
        return null;
    }
}



```
