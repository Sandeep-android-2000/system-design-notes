### Parking Lot System - Discussion & Design Analysis

#### **Overview**
The parking lot system is designed to efficiently manage parking spots, vehicle entries, ticket generation, and payment processing. It supports multiple vehicle types and incorporates scalable design principles.

---

### **Key Components & Responsibilities**

#### **1. Vehicle**
- Attributes: `vehicleNumber`, `vehicleType (TWO_WHEELER/FOUR_WHEELER)`
- Represents a vehicle entering the parking lot.

#### **2. Ticket**
- Attributes: `ticketId`, `entryTime`, `parkingSpot`, `price`
- Generated at the entrance and updated at the exit.

#### **3. Parking Spot** (Abstract Class)
- Attributes: `spotId`, `isEmpty`, `allowedVehicleType`
- Methods: `isEmpty()`, `updateParkingSpace(boolean status)`
- Specialized into:
  - `TwoWheelerSpot`
  - `FourWheelerSpot`

#### **4. Parking Manager** (Abstract Class)
- Manages parking allocation.
- Methods:
  - `findParkingSpot()`: Finds an available spot.
  - Implemented by:
    - `TwoWheelerManager`
    - `FourWheelerManager`

#### **5. Parking Manager Factory**
- Provides the correct parking manager based on vehicle type.

#### **6. Entrance Gate**
- Uses `ParkingManager` to assign spots and generate tickets.

#### **7. Exit Gate**
- Processes exits, calculates cost, updates availability.

---

### **Design Pattern Analysis**

#### **1. Factory Pattern**
- Used in `ParkingManagerFactory` to return the correct parking manager.
- Encapsulates object creation, promoting modularity.

#### **2. Strategy Pattern** (Potential Improvement)
- Parking strategies (Nearest to Entrance, Nearest to Elevator) could be implemented using the Strategy Pattern.
- Different strategies can be applied dynamically based on user preference.

#### **3. Singleton Pattern** (Potential Improvement)
- `ParkingManager` could be a Singleton if we want a single instance handling all spots.

#### **4. Template Method Pattern**
- `ParkingSpot` as an abstract class follows this principle by defining common behaviors with specific implementations in subclasses.

---

### **Code Implementation**
```java
import java.util.*;

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
}

// Ticket class
class Ticket {
    private String ticketId;
    private Date entryTime;
    private ParkingSpot parkingSpot;
    private double price;
}

// Abstract Parking Spot class
abstract class ParkingSpot {
    protected String spotId;
    protected boolean isEmpty;
    protected VehicleType allowedVehicleType;
}

class TwoWheelerSpot extends ParkingSpot {}
class FourWheelerSpot extends ParkingSpot {}

// Abstract Parking Manager
abstract class ParkingManager {
    protected List<ParkingSpot> parkingSpots;
    public abstract ParkingSpot findParkingSpot();
}

class TwoWheelerManager extends ParkingManager {}
class FourWheelerManager extends ParkingManager {}

// Factory for Parking Manager
class ParkingManagerFactory {
    public static ParkingManager getParkingManager(VehicleType type, List<ParkingSpot> spots) {
        return type == VehicleType.TWO_WHEELER ? new TwoWheelerManager() : new FourWheelerManager();
    }
}

// Entrance and Exit Gates
class EntranceGate {}
class ExitGate {}

// Main Class
public class ParkingLotSystem {
    public static void main(String[] args) {
        // Sample Implementation
    }
}
```

---

### **Conclusion**
This design ensures **modularity, scalability, and maintainability**. The **Factory Pattern** is implemented, while **Strategy and Singleton Patterns** could further improve the system.
