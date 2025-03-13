# Low-Level Design (LLD) of Parking Lot

## Step 1: Requirement Gathering

### Basic Flow:
1. A **vehicle** arrives at the **entrance gate**.
2. The **entrance gate** assigns a **parking spot** using a **ParkingSpotManager**.
3. The **vehicle** parks in the assigned **parking spot**.
4. At exit, the **total charge** is calculated based on **time parked**.
5. The **parking spot** is freed after payment.

### Key Enhancements:
1. **Scalability**: Supports **multiple** entrance and exit gates.
2. **Vehicle Types**: Supports **2-Wheeler, 3-Wheeler, and 4-Wheeler**.
3. **Charging Mechanism**: Mix of **Hourly-based / Minute-based** pricing.
4. **Nearest Spot Allocation** using **ParkingStrategy** (Strategy Pattern).
5. **Factory Pattern** to create appropriate **ParkingSpotManager**.

---

## Step 2: Identify Key Objects

### Main Classes & Their Relationships

| Object                 | Description |
|------------------------|-------------|
| **Vehicle**           | Represents a vehicle with `vehicleNumber` and `vehicleType` (enum: 2W, 3W, 4W). |
| **ParkingSpot (Abstract Class)** | Parent class for all parking spot types. |
| **TwoWheelerSpot, ThreeWheelerSpot, FourWheelerSpot** | Specific parking spot types inheriting `ParkingSpot`. |
| **ParkingSpotManager (Abstract Class)** | Manages parking spots, contains `findParkingSpace()`, `addParkingSpace()`, etc. |
| **TwoWheelerParkingManager, FourWheelerParkingManager** | Specialized managers for different vehicle types. |
| **ParkingStrategy (Interface)** | Defines the strategy to find a parking space. |
| **NearToEntrance, NearToElevator, DefaultParkingStrategy** | Implement different **strategies** for parking spot allocation. |
| **ParkingSpotFactory** | Factory Pattern for creating **ParkingSpotManager**. |
| **Ticket** | Holds `entryTime`, `vehicle`, and `parkingSpot`. |
| **EntranceGate** | Uses **ParkingSpotFactory** to allocate parking spots. |
| **ExitGate** | Calculates parking cost and frees up spots. |

---

## Step 3: Class Definitions (Java)

```java
// Example implementation of Vehicle class
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

// Abstract ParkingSpot class
abstract class ParkingSpot {
    protected int id;
    protected boolean isEmpty;
    protected Vehicle vehicle;

    public ParkingSpot(int id) {
        this.id = id;
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
}

// Concrete Parking Spot types
class TwoWheelerSpot extends ParkingSpot {
    public TwoWheelerSpot(int id) {
        super(id);
    }
}

class FourWheelerSpot extends ParkingSpot {
    public FourWheelerSpot(int id) {
        super(id);
    }
}

// Strategy Pattern for Parking
interface ParkingStrategy {
    ParkingSpot findSpace(List<ParkingSpot> spots);
}

class NearToEntrance implements ParkingStrategy {
    public ParkingSpot findSpace(List<ParkingSpot> spots) {
        for (ParkingSpot spot : spots) {
            if (spot.isAvailable()) {
                return spot;
            }
        }
        return null;
    }
}

// Parking Spot Factory (Factory Pattern)
class ParkingSpotFactory {
    public static ParkingSpot getParkingSpot(VehicleType type, int id) {
        if (type == VehicleType.TWO_WHEELER) {
            return new TwoWheelerSpot(id);
        } else if (type == VehicleType.FOUR_WHEELER) {
            return new FourWheelerSpot(id);
        }
        return null;
    }
}

// Ticket class with Has-A relationship with Vehicle and ParkingSpot
class Ticket {
    private String ticketId;
    private LocalDateTime entryTime;
    private Vehicle vehicle;
    private ParkingSpot parkingSpot;

    public Ticket(String ticketId, LocalDateTime entryTime, Vehicle vehicle, ParkingSpot parkingSpot) {
        this.ticketId = ticketId;
        this.entryTime = entryTime;
        this.vehicle = vehicle;
        this.parkingSpot = parkingSpot;
    }
}

// Entrance Gate
class EntranceGate {
    private ParkingSpotFactory factory;

    public EntranceGate(ParkingSpotFactory factory) {
        this.factory = factory;
    }

    public Ticket generateTicket(Vehicle vehicle, int spotId) {
        ParkingSpot spot = factory.getParkingSpot(vehicle.getType(), spotId);

        if (spot == null) {
            System.out.println("No parking space available.");
            return null;
        }

        spot.assignVehicle(vehicle);
        return new Ticket("TCKT-" + System.currentTimeMillis(), LocalDateTime.now(), vehicle, spot);
    }
}

// Exit Gate
class ExitGate {
    public double calculateCost(Ticket ticket) {
        long minutes = Duration.between(ticket.getEntryTime(), LocalDateTime.now()).toMinutes();
        return minutes * 2.0; // Example rate per minute
    }

    public void processExit(Ticket ticket) {
        double cost = calculateCost(ticket);
        System.out.println("Total cost: $" + cost);
        ticket.getParkingSpot().removeVehicle();
    }
}
```

## Summary

### ✅ Scalability
   - Supports **multiple** entrance & exit gates.
   - **Factory Pattern** for **ParkingSpotManager**.

### ✅ Supports Different Vehicle Types
   - **2-Wheeler, 3-Wheeler, 4-Wheeler**.

### ✅ Strategy Pattern for Spot Allocation
   - **NearToEntrance, NearToElevator, Default Strategy**.

### ✅ Factory Pattern for Parking Spot Management
   - Dynamic creation of **ParkingSpotManager**.

