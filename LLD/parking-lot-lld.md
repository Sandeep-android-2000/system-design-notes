# Low-Level Design (LLD) of Parking Lot

## Step 1: Requirement Gathering

### Basic Flow:
1. A **vehicle** arrives at the **entrance gate**.
2. The **entrance gate** assigns a **parking spot** and issues a **ticket**.
3. The **vehicle** parks in the assigned **parking spot**.
4. At exit, the **total charge** is calculated based on **time parked**.
5. The **parking spot** is freed after payment.

### Key Requirements:
1. **Scalability**: Should support **multiple** entrance and exit gates.
2. **Vehicle Types**: Supports **2-Wheeler, 3-Wheeler, and 4-Wheeler**.
3. **Charging Mechanism**: Mix of **Hourly-based / Minute-based** pricing.
4. **Nearest Spot Allocation**: The system should assign the **closest available** parking spot.

## Step 2: Identify Key Objects

| Object          | Properties / Functions |
|----------------|----------------------|
| **Vehicle**    | `vehicleNumber`, `vehicleType` (enum: 2, 3, 4 Wheeler) |
| **Ticket**     | `entryTime`, `parkingSpot`, `chargeType` |
| **EntranceGate** | `findParkingSpace()`, `updateParkingSpace()`, `generateTicket()` |
| **ParkingSpot** | `id`, `isEmpty`, `vehicle`, `price`, `type` |
| **ExitGate**   | `calculateCost()`, `processPayment()`, `updateParkingSpot()` |

## Step 3: Class Definitions (Java)

### Vehicle Class

```java
enum VehicleType {
    TWO_WHEELER, THREE_WHEELER, FOUR_WHEELER
}

class Vehicle {
    private String vehicleNumber;
    private VehicleType type;

    public Vehicle(String vehicleNumber, VehicleType type) {
        this.vehicleNumber = vehicleNumber;
        this.type = type;
    }

    public String getVehicleNumber() {
        return vehicleNumber;
    }

    public VehicleType getType() {
        return type;
    }
}
```

### Parking Spot

```java
class ParkingSpot {
    private int id;
    private boolean isEmpty;
    private Vehicle vehicle;
    private double price;
    private VehicleType type;

    public ParkingSpot(int id, double price, VehicleType type) {
        this.id = id;
        this.price = price;
        this.type = type;
        this.isEmpty = true;
    }

    public boolean isAvailable() {
        return isEmpty;
    }

    public void assignVehicle(Vehicle vehicle) {
        this.vehicle = vehicle;
        this.isEmpty = false;
    }

    public void removeVehicle() {
        this.vehicle = null;
        this.isEmpty = true;
    }

    public double getPrice() {
        return price;
    }

    public VehicleType getType() {
        return type;
    }
}
```

### Ticket Class

```java
import java.time.LocalDateTime;

class Ticket {
    private String ticketId;
    private LocalDateTime entryTime;
    private ParkingSpot parkingSpot;

    public Ticket(String ticketId, LocalDateTime entryTime, ParkingSpot parkingSpot) {
        this.ticketId = ticketId;
        this.entryTime = entryTime;
        this.parkingSpot = parkingSpot;
    }

    public LocalDateTime getEntryTime() {
        return entryTime;
    }

    public ParkingSpot getParkingSpot() {
        return parkingSpot;
    }
}
```

### Entrance Gate

```java
import java.time.LocalDateTime;
import java.util.List;

class EntranceGate {
    private List<ParkingSpot> parkingSpots;

    public EntranceGate(List<ParkingSpot> parkingSpots) {
        this.parkingSpots = parkingSpots;
    }

    public ParkingSpot findNearestParkingSpot(VehicleType type) {
        for (ParkingSpot spot : parkingSpots) {
            if (spot.getType() == type && spot.isAvailable()) {
                return spot;
            }
        }
        return null;
    }

    public Ticket generateTicket(Vehicle vehicle) {
        ParkingSpot assignedSpot = findNearestParkingSpot(vehicle.getType());
        if (assignedSpot == null) {
            System.out.println("No available parking spot.");
            return null;
        }
        assignedSpot.assignVehicle(vehicle);
        return new Ticket("TCKT-" + System.currentTimeMillis(), LocalDateTime.now(), assignedSpot);
    }
}
```

### Exit Gate

```java
import java.time.Duration;
import java.time.LocalDateTime;

class ExitGate {
    public double calculateCost(Ticket ticket) {
        LocalDateTime entryTime = ticket.getEntryTime();
        LocalDateTime exitTime = LocalDateTime.now();

        long minutesParked = Duration.between(entryTime, exitTime).toMinutes();
        double rate = ticket.getParkingSpot().getPrice();
        return minutesParked * (rate / 60);
    }

    public void processExit(Ticket ticket) {
        double cost = calculateCost(ticket);
        System.out.println("Total parking cost: $" + cost);
        ticket.getParkingSpot().removeVehicle();
    }
}
```

### Parking Lot

```java
import java.util.ArrayList;
import java.util.List;

class ParkingLot {
    private List<ParkingSpot> parkingSpots;
    private List<EntranceGate> entranceGates;
    private List<ExitGate> exitGates;

    public ParkingLot(int numSpots) {
        parkingSpots = new ArrayList<>();
        for (int i = 0; i < numSpots; i++) {
            VehicleType type = (i % 3 == 0) ? VehicleType.TWO_WHEELER : (i % 3 == 1) ? VehicleType.THREE_WHEELER : VehicleType.FOUR_WHEELER;
            parkingSpots.add(new ParkingSpot(i, 10.0 + i, type));
        }

        entranceGates = new ArrayList<>();
        exitGates = new ArrayList<>();
    }

    public void addEntranceGate() {
        entranceGates.add(new EntranceGate(parkingSpots));
    }

    public void addExitGate() {
        exitGates.add(new ExitGate());
    }

    public EntranceGate getEntranceGate(int index) {
        return entranceGates.get(index);
    }

    public ExitGate getExitGate(int index) {
        return exitGates.get(index);
    }
}
```

## Step 4: System Execution

```java
public class ParkingLotSystem {
    public static void main(String[] args) {
        ParkingLot parkingLot = new ParkingLot(10);
        
        parkingLot.addEntranceGate();
        parkingLot.addExitGate();

        EntranceGate entrance = parkingLot.getEntranceGate(0);
        Vehicle vehicle = new Vehicle("AB1234", VehicleType.TWO_WHEELER);
        Ticket ticket = entrance.generateTicket(vehicle);

        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        ExitGate exit = parkingLot.getExitGate(0);
        exit.processExit(ticket);
    }
}
```

---

## Summary

### ✅ Scalability
   - Supports **multiple** entrance & exit gates.
   - Dynamic **spot allocation**.

### ✅ Supports Different Vehicle Types
   - **2-Wheeler, 3-Wheeler, 4-Wheeler**.

### ✅ Flexible Pricing System
   - **Minute-based / Hourly-based** charges.

### ✅ Nearest Spot Allocation
   - Ensures **efficient parking assignment**.

