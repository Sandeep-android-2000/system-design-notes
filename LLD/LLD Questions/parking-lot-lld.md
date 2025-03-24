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

// Enum for Vehicle Type
enum VehicleType {
    TWO_WHEELER, FOUR_WHEELER
}

// Vehicle class
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

// Ticket class
class Ticket {
    protected String ticketId;
    protected Date entryTime;
    protected ParkingSpot parkingSpot;
    protected double price;
}

// Pricing Strategy Interface
interface PricingStrategy {
    double calculatePrice(Date entryTime, Date exitTime, VehicleType vehicleType);
}

// Two-Wheeler Pricing Strategy
class TwoWheelerPricingStrategy implements PricingStrategy {
    private static final double RATE_PER_HOUR = 10.0;

    @Override
    public double calculatePrice(Date entryTime, Date exitTime, VehicleType vehicleType) {
        long duration = exitTime.getTime() - entryTime.getTime();
        long hours = TimeUnit.MILLISECONDS.toHours(duration) + 1;
        return hours * RATE_PER_HOUR;
    }
}

// Four-Wheeler Pricing Strategy
class FourWheelerPricingStrategy implements PricingStrategy {
    private static final double RATE_PER_HOUR = 20.0;

    @Override
    public double calculatePrice(Date entryTime, Date exitTime, VehicleType vehicleType) {
        long duration = exitTime.getTime() - entryTime.getTime();
        long hours = TimeUnit.MILLISECONDS.toHours(duration) + 1;
        return hours * RATE_PER_HOUR;
    }
}

// Pricing Manager
class PricingManager {
    private PricingStrategy pricingStrategy;

    public PricingManager(PricingStrategy pricingStrategy) {
        this.pricingStrategy = pricingStrategy;
    }

    public double getParkingPrice(Date entryTime, Date exitTime, VehicleType vehicleType) {
        return pricingStrategy.calculatePrice(entryTime, exitTime, vehicleType);
    }
}

// Exit Gate - Implements Pricing
class ExitGate {
    private PricingManager pricingManager;

    public ExitGate(PricingManager pricingManager) {
        this.pricingManager = pricingManager;
    }

    public void processExit(Ticket ticket, Date exitTime) {
        double price = pricingManager.getParkingPrice(ticket.entryTime, exitTime, ticket.parkingSpot.allowedVehicleType);
        System.out.println("Total Parking Fee: $" + price);
    }
}

// Main Class
public class ParkingLotSystem {
    public static void main(String[] args) {
        // Sample Implementation
    }
}
```

---

### **Conclusion**
This design integrates key OOP principles and leverages **Factory, Strategy, Singleton, and Template Method Patterns** for an optimized, maintainable parking lot system. Let me know if further refinements are needed!
