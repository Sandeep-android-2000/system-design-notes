### Parking Lot System - Discussion & Design Analysis

#### **Overview**
The parking lot system is designed to efficiently manage parking spots, vehicle entries, ticket generation, and payment processing. It supports multiple vehicle types and incorporates scalable design principles. This document integrates insights from the provided transcript for improved clarity and structure.

---

### **Key Components & Responsibilities**

#### **1. Vehicle**
- Attributes: `vehicleNumber`, `vehicleType (TWO_WHEELER/FOUR_WHEELER)`
- Represents a vehicle entering the parking lot.
- Methods: 
  - `getVehicleNumber()`: Returns the vehicle's registration number.
  - `getVehicleType()`: Identifies if it’s a two-wheeler or four-wheeler.

#### **2. Ticket**
- Attributes: `ticketId`, `entryTime`, `parkingSpot`, `price`
- Methods:
  - `generateTicket(Vehicle vehicle, ParkingSpot spot)`: Generates a ticket upon vehicle entry.
  - `calculatePrice()`: Computes the parking fee based on duration and type.
  
#### **3. Parking Spot** (Abstract Class)
- Attributes: `spotId`, `isEmpty`, `allowedVehicleType`
- Methods: 
  - `isAvailable()`: Checks if the spot is free.
  - `assignVehicle(Vehicle vehicle)`: Allocates a vehicle to the spot.
  - `removeVehicle()`: Frees up the spot.
- Specialized into:
  - `TwoWheelerSpot`
  - `FourWheelerSpot`

#### **4. Parking Manager** (Abstract Class)
- Manages parking allocation.
- Attributes: `List<ParkingSpot>`
- Methods:
  - `findParkingSpot()`: Finds an available spot based on the vehicle type.
  - Implemented by:
    - `TwoWheelerManager`
    - `FourWheelerManager`

#### **5. Parking Manager Factory**
- Factory class responsible for creating instances of `ParkingManager`.
- Uses **Factory Pattern** to decide which manager to instantiate based on vehicle type.

#### **6. Entrance Gate**
- Responsibilities:
  - Uses `ParkingManager` to find an available spot.
  - Generates a `Ticket` for the vehicle.

#### **7. Exit Gate**
- Responsibilities:
  - Calculates parking duration.
  - Computes the fee using a **Pricing Strategy**.
  - Processes payment and updates the spot status.

---

### **Design Pattern Analysis**

#### **1. Factory Pattern**
- Implemented in `ParkingManagerFactory` to return appropriate parking manager instances dynamically.
- Encapsulates object creation, promoting modularity.

#### **2. Strategy Pattern**
- Different pricing strategies for vehicle types:
  - **Two-Wheeler Pricing**
  - **Four-Wheeler Pricing**
  - **Mixed Vehicle Pricing**
- Implemented using a `PricingStrategy` interface and multiple concrete strategies.

#### **3. Singleton Pattern**
- `ParkingManager` could be a Singleton, ensuring a single instance manages all spots.

#### **4. Template Method Pattern**
- `ParkingSpot` serves as an abstract class defining common behaviors, with subclasses providing specific implementations.

---

### **Refactored Code Implementation**
```java
import java.util.*;
import java.util.concurrent.TimeUnit;

// ===========================
// ENUM: Vehicle Type
// ===========================
enum VehicleType {
    TWO_WHEELER, FOUR_WHEELER
}

// ===========================
// Vehicle
// ===========================
class Vehicle {
    private String vehicleNumber;
    private VehicleType vehicleType;

    public Vehicle(String vehicleNumber, VehicleType vehicleType) {
        this.vehicleNumber = vehicleNumber;
        this.vehicleType = vehicleType;
    }

    public String getVehicleNumber() {
        return vehicleNumber;
    }

    public VehicleType getVehicleType() {
        return vehicleType;
    }
}

// ===========================
// Parking Spot
// ===========================
class ParkingSpot {
    protected int id;
    protected boolean isFree;
    protected VehicleType allowedVehicleType;

    public ParkingSpot(int id, VehicleType allowedVehicleType) {
        this.id = id;
        this.allowedVehicleType = allowedVehicleType;
        this.isFree = true;
    }

    public boolean isFree() {
        return isFree;
    }

    public void occupySpot() {
        isFree = false;
    }

    public void freeSpot() {
        isFree = true;
    }

    public VehicleType getAllowedVehicleType() {
        return allowedVehicleType;
    }

    public int getId() {
        return id;
    }
}

// ===========================
// Spot Manager
// ===========================
class ParkingSpotManager {
    private Queue<ParkingSpot> twoWheelerSpots;
    private Queue<ParkingSpot> fourWheelerSpots;

    public ParkingSpotManager(int twoWheelers, int fourWheelers) {
        twoWheelerSpots = new LinkedList<>();
        fourWheelerSpots = new LinkedList<>();

        // create spots
        for (int i = 1; i <= twoWheelers; i++) {
            twoWheelerSpots.add(new ParkingSpot(i, VehicleType.TWO_WHEELER));
        }
        for (int i = 1; i <= fourWheelers; i++) {
            fourWheelerSpots.add(new ParkingSpot(i + 100, VehicleType.FOUR_WHEELER));
        }
    }

    public ParkingSpot assignSpot(VehicleType type) {
        if (type == VehicleType.TWO_WHEELER && !twoWheelerSpots.isEmpty()) {
            ParkingSpot spot = twoWheelerSpots.poll();
            spot.occupySpot();
            return spot;
        }

        if (type == VehicleType.FOUR_WHEELER && !fourWheelerSpots.isEmpty()) {
            ParkingSpot spot = fourWheelerSpots.poll();
            spot.occupySpot();
            return spot;
        }

        return null; // no spot available
    }

    public void freeSpot(ParkingSpot spot) {
        spot.freeSpot();

        if (spot.getAllowedVehicleType() == VehicleType.TWO_WHEELER)
            twoWheelerSpots.offer(spot);
        else
            fourWheelerSpots.offer(spot);
    }
}

// ===========================
// Ticket
// ===========================
class Ticket {
    protected String ticketId;
    protected Date entryTime;
    protected ParkingSpot parkingSpot;
    protected Vehicle vehicle;

    public Ticket(String ticketId, Vehicle vehicle, ParkingSpot spot, Date entryTime) {
        this.ticketId = ticketId;
        this.vehicle = vehicle;
        this.parkingSpot = spot;
        this.entryTime = entryTime;
    }
}

// ===========================
// Pricing Strategy
// ===========================
interface PricingStrategy {
    double calculatePrice(Date entryTime, Date exitTime, VehicleType vehicleType);
}

class TwoWheelerPricingStrategy implements PricingStrategy {
    private static final double RATE_PER_HOUR = 10.0;

    @Override
    public double calculatePrice(Date entryTime, Date exitTime, VehicleType vehicleType) {
        long duration = exitTime.getTime() - entryTime.getTime();
        long hours = TimeUnit.MILLISECONDS.toHours(duration) + 1;
        return hours * RATE_PER_HOUR;
    }
}

class FourWheelerPricingStrategy implements PricingStrategy {
    private static final double RATE_PER_HOUR = 20.0;

    @Override
    public double calculatePrice(Date entryTime, Date exitTime, VehicleType vehicleType) {
        long duration = exitTime.getTime() - entryTime.getTime();
        long hours = TimeUnit.MILLISECONDS.toHours(duration) + 1;
        return hours * RATE_PER_HOUR;
    }
}

// ===========================
// Pricing Manager
// ===========================
class PricingManager {
    public double getParkingPrice(Date entryTime, Date exitTime, VehicleType type) {
        PricingStrategy strategy;

        if (type == VehicleType.TWO_WHEELER)
            strategy = new TwoWheelerPricingStrategy();
        else
            strategy = new FourWheelerPricingStrategy();

        return strategy.calculatePrice(entryTime, exitTime, type);
    }
}

// ===========================
// Entry Gate
// ===========================
class EntryGate {
    private ParkingSpotManager spotManager;

    public EntryGate(ParkingSpotManager spotManager) {
        this.spotManager = spotManager;
    }

    public Ticket generateTicket(Vehicle vehicle) {
        ParkingSpot spot = spotManager.assignSpot(vehicle.getVehicleType());
        if (spot == null) {
            System.out.println("No spot available!");
            return null;
        }

        String ticketId = UUID.randomUUID().toString();
        Date entryTime = new Date();

        System.out.println("Spot " + spot.getId() + " assigned.");

        return new Ticket(ticketId, vehicle, spot, entryTime);
    }
}

// ===========================
// Exit Gate
// ===========================
class ExitGate {
    private PricingManager pricingManager;
    private ParkingSpotManager spotManager;

    public ExitGate(PricingManager pricingManager, ParkingSpotManager spotManager) {
        this.pricingManager = pricingManager;
        this.spotManager = spotManager;
    }

    public void processExit(Ticket ticket) {
        Date exitTime = new Date();

        double price = pricingManager.getParkingPrice(
                ticket.entryTime,
                exitTime,
                ticket.vehicle.getVehicleType()
        );

        System.out.println("Vehicle " + ticket.vehicle.getVehicleNumber() +
                " parked at spot " + ticket.parkingSpot.getId());
        System.out.println("Total Parking Fee: ₹" + price);

        spotManager.freeSpot(ticket.parkingSpot);
    }
}

// ===========================
// MAIN
// ===========================
public class ParkingLotSystem {
    public static void main(String[] args) throws InterruptedException {

        // create parking spot manager
        ParkingSpotManager spotManager = new ParkingSpotManager(2, 2);

        EntryGate entryGate = new EntryGate(spotManager);
        PricingManager pricingManager = new PricingManager();
        ExitGate exitGate = new ExitGate(pricingManager, spotManager);

        // Vehicle enters
        Vehicle v1 = new Vehicle("UP32AB1234", VehicleType.FOUR_WHEELER);
        Ticket t1 = entryGate.generateTicket(v1);

        // WAIT FOR BILLING DEMO
        Thread.sleep(2000);

        // vehicle exits
        exitGate.processExit(t1);
    }
}

```

---

### **Conclusion**
This design integrates key OOP principles and leverages **Factory, Strategy, Singleton, and Template Method Patterns** for an optimized, maintainable parking lot system. Let me know if further refinements are needed!
