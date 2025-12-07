# Parking Lot LLD (Beginner-Friendly)  
### With 2-Wheeler, 3-Wheeler, 4-Wheeler, Heavy Utility Vehicle (HUV)  
### Includes Simple Price Calculation Strategy

This is a **beginner‑friendly** Low-Level Design of a Parking Lot System with simplified vehicle categories and pricing strategies. It includes:

- Requirements  
- Entities  
- Relationships  
- UML (PlantUML)  
- Full Java Code  
- Designed for beginners  

---

# 1. Requirements

## Functional
1. System must support **vehicle types**:
   - **2-Wheeler** (bike, scooter)
   - **3-Wheeler** (auto)
   - **4-Wheeler** (car, small van)
   - **HUV (Heavy Utility Vehicle)** (bus, truck)

2. Parking spot types:
   - TW_SPOT (2-wheeler)
   - THW_SPOT (3-wheeler)
   - FW_SPOT (4-wheeler)
   - HUV_SPOT (heavy vehicles)

3. Entry Gate:
   - Assigns the correct parking spot.
   - Generates a ticket.

4. Exit Gate:
   - Computes price.
   - Frees the spot.

5. Ticket contains:
   - Vehicle details
   - Spot assigned
   - Entry time
   - Exit time
   - Price

## Non‑Functional
- Easy for beginners to understand.
- Clean extensible OOP design.
- Console-based sample run.

---

# 2. Entities & Responsibilities

### Vehicle
Represents a vehicle. Types:
- TwoWheeler
- ThreeWheeler
- FourWheeler
- HUV

### ParkingSpot
Each spot has:
- id
- spotType
- occupied or not

### Ticket
Contains:
- ticketId
- vehicle
- parkingSpot
- entryTime
- exitTime
- price

### EntryGate
Handles:
- park vehicle
- generate ticket

### ExitGate
Handles:
- unpark vehicle
- calculate price

### PriceCalculationStrategy
Strategy pattern:
- TwoWheelerPricing
- ThreeWheelerPricing
- FourWheelerPricing
- HUVPricing

### ParkingManager
Admin functions:
- View floor status

---

# 3. UML Diagram (PlantUML)

<img width="1488" height="822" alt="ParkingLot" src="https://github.com/user-attachments/assets/492afef0-f21d-4782-ae2f-afc4c6445a76" />


---

# 4. Beginner-Friendly Explanation of Pricing Strategy

We use **Strategy Pattern** because:

- Each vehicle type has different pricing.
- If tomorrow we introduce EV pricing — easy to add.
- No `if-else-if-else` mess inside ExitGate.

### Simple Pricing:
| Vehicle Type | Hourly Price |
|--------------|--------------|
| 2-Wheeler    | ₹10/hour     |
| 3-Wheeler    | ₹15/hour     |
| 4-Wheeler    | ₹25/hour     |
| HUV          | ₹50/hour     |

Minimum charge = **1 hour**.

---

# 5. 💻 Full Java Code (Beginner-Friendly)

```java
import java.time.*;
import java.util.*;

// ===========================================================
// MAIN DEMO CLASS
// ===========================================================
public class ParkingLotSystem {

    public static void main(String[] args) throws InterruptedException {

        ParkingLot lot = new ParkingLot();

        // Add floors
        lot.addFloor(new ParkingFloor(1, 5, 5, 5, 2));

        // Vehicles
        Vehicle v1 = new TwoWheeler("TW-101", "Red");
        Vehicle v2 = new ThreeWheeler("THW-301", "Yellow");
        Vehicle v3 = new FourWheeler("CAR-999", "Blue");
        Vehicle v4 = new HUV("TRK-808", "Black");

        Ticket t1 = lot.getEntryGate().parkVehicle(v1);
        Ticket t2 = lot.getEntryGate().parkVehicle(v2);
        Ticket t3 = lot.getEntryGate().parkVehicle(v3);

        Thread.sleep(1200); // simulate time

        System.out.println("Price v1 = " + lot.getExitGate().unparkVehicle(t1.getTicketId()));
        System.out.println("Price v2 = " + lot.getExitGate().unparkVehicle(t2.getTicketId()));

        lot.getManager().printStatus();
    }
}


// ===========================================================
// VEHICLES
// ===========================================================
enum VehicleType { TWO_WHEELER, THREE_WHEELER, FOUR_WHEELER, HUV }

abstract class Vehicle {
    protected String regNo;
    protected String color;

    public Vehicle(String regNo, String color) {
        this.regNo = regNo;
        this.color = color;
    }

    public abstract VehicleType getType();
}

class TwoWheeler extends Vehicle {
    public TwoWheeler(String r, String c) { super(r,c); }
    public VehicleType getType() { return VehicleType.TWO_WHEELER; }
}

class ThreeWheeler extends Vehicle {
    public ThreeWheeler(String r, String c) { super(r,c); }
    public VehicleType getType() { return VehicleType.THREE_WHEELER; }
}

class FourWheeler extends Vehicle {
    public FourWheeler(String r, String c) { super(r,c); }
    public VehicleType getType() { return VehicleType.FOUR_WHEELER; }
}

class HUV extends Vehicle {
    public HUV(String r, String c) { super(r,c); }
    public VehicleType getType() { return VehicleType.HUV; }
}


// ===========================================================
// PARKING SPOTS
// ===========================================================
enum SpotType { TW_SPOT, THW_SPOT, FW_SPOT, HUV_SPOT }

class ParkingSpot {
    private String id;
    private SpotType type;
    private boolean occupied = false;

    public ParkingSpot(String id, SpotType t) {
        this.id = id;
        this.type = t;
    }

    public boolean assign() {
        if (occupied) return false;
        occupied = true;
        return true;
    }

    public void release() { occupied = false; }

    public boolean isOccupied() { return occupied; }
    public SpotType getType() { return type; }
    public String getId() { return id; }
}


// ===========================================================
// FLOOR
// ===========================================================
class ParkingFloor {
    private int floorNo;
    private List<ParkingSpot> spots = new ArrayList<>();

    public ParkingFloor(int floorNo, int tw, int thw, int fw, int huv) {
        this.floorNo = floorNo;

        createSpots("TW", SpotType.TW_SPOT, tw);
        createSpots("THW", SpotType.THW_SPOT, thw);
        createSpots("FW", SpotType.FW_SPOT, fw);
        createSpots("HUV", SpotType.HUV_SPOT, huv);
    }

    private void createSpots(String prefix, SpotType type, int count) {
        for(int i=1;i<=count;i++){
            spots.add(new ParkingSpot(prefix + "-" + floorNo + "-" + i, type));
        }
    }

    public ParkingSpot getAvailableSpot(VehicleType type) {
        SpotType required = mapVehicleToSpot(type);

        for(ParkingSpot s : spots) {
            if(!s.isOccupied() && s.getType() == required)
                return s;
        }
        return null;
    }

    private SpotType mapVehicleToSpot(VehicleType vt) {
        switch (vt) {
            case TWO_WHEELER: return SpotType.TW_SPOT;
            case THREE_WHEELER: return SpotType.THW_SPOT;
            case FOUR_WHEELER: return SpotType.FW_SPOT;
            case HUV: return SpotType.HUV_SPOT;
        }
        return SpotType.FW_SPOT;
    }

    public int getFloorNo() { return floorNo; }

    public List<ParkingSpot> getSpots() { return spots; }
}


// ===========================================================
// TICKET
// ===========================================================
class Ticket {
    private String ticketId;
    private Vehicle vehicle;
    private ParkingSpot spot;
    private LocalDateTime entryTime;
    private LocalDateTime exitTime;
    private double price;

    public Ticket(String id, Vehicle v, ParkingSpot s) {
        this.ticketId = id;
        this.vehicle = v;
        this.spot = s;
        this.entryTime = LocalDateTime.now();
    }

    public String getTicketId() { return ticketId; }
    public Vehicle getVehicle() { return vehicle; }
    public ParkingSpot getSpot() { return spot; }
    public LocalDateTime getEntryTime() { return entryTime; }
    public void setExitTime(LocalDateTime t) { exitTime = t; }
    public void setPrice(double p) { price = p; }
    public double getPrice() { return price; }
    public LocalDateTime getExitTime() { return exitTime; }
}


// ===========================================================
// PRICE STRATEGY PATTERN
// ===========================================================
interface PriceStrategy {
    double calculatePrice(long hours);
}

class TwoWheelerPricing implements PriceStrategy {
    public double calculatePrice(long h) { return 10 * Math.max(1, h); }
}

class ThreeWheelerPricing implements PriceStrategy {
    public double calculatePrice(long h) { return 15 * Math.max(1, h); }
}

class FourWheelerPricing implements PriceStrategy {
    public double calculatePrice(long h) { return 25 * Math.max(1, h); }
}

class HUVPricing implements PriceStrategy {
    public double calculatePrice(long h) { return 50 * Math.max(1, h); }
}


// ===========================================================
// ENTRY GATE
// ===========================================================
class EntryGate {
    private ParkingLot lot;

    public EntryGate(ParkingLot lot) { this.lot = lot; }

    public Ticket parkVehicle(Vehicle v) {
        ParkingSpot spot = lot.findSpot(v.getType());
        if (spot == null) {
            System.out.println("No spot available for " + v.getType());
            return null;
        }

        spot.assign();
        String tid = UUID.randomUUID().toString();

        Ticket t = new Ticket(tid, v, spot);
        lot.getActiveTickets().put(tid, t);

        System.out.println("Assigned " + v.getRegNo() + " to spot " + spot.getId());
        return t;
    }
}


// ===========================================================
// EXIT GATE
// ===========================================================
class ExitGate {
    private ParkingLot lot;

    public ExitGate(ParkingLot lot) { this.lot = lot; }

    public double unparkVehicle(String ticketId) {

        Ticket t = lot.getActiveTickets().get(ticketId);
        if (t == null) {
            System.out.println("Invalid ticket");
            return 0;
        }

        t.setExitTime(LocalDateTime.now());

        long minutes = Duration.between(t.getEntryTime(), t.getExitTime()).toMinutes();
        long hours = Math.max(1, (minutes + 59) / 60);

        PriceStrategy strategy = lot.getPricing(t.getVehicle().getType());

        double price = strategy.calculatePrice(hours);
        t.setPrice(price);

        t.getSpot().release();
        lot.getActiveTickets().remove(ticketId);

        return price;
    }
}


// ===========================================================
// PARKING LOT MAIN CLASS (FACADE)
// ===========================================================
class ParkingLot {
    private List<ParkingFloor> floors = new ArrayList<>();
    private Map<String, Ticket> activeTickets = new HashMap<>();

    private EntryGate entryGate = new EntryGate(this);
    private ExitGate exitGate = new ExitGate(this);
    private ParkingManager manager = new ParkingManager(this);

    private Map<VehicleType, PriceStrategy> strategyMap = new HashMap<>();

    public ParkingLot() {
        strategyMap.put(VehicleType.TWO_WHEELER, new TwoWheelerPricing());
        strategyMap.put(VehicleType.THREE_WHEELER, new ThreeWheelerPricing());
        strategyMap.put(VehicleType.FOUR_WHEELER, new FourWheelerPricing());
        strategyMap.put(VehicleType.HUV, new HUVPricing());
    }

    public void addFloor(ParkingFloor f) { floors.add(f); }

    public ParkingSpot findSpot(VehicleType vt) {
        for(ParkingFloor f: floors){
            ParkingSpot s = f.getAvailableSpot(vt);
            if(s != null) return s;
        }
        return null;
    }

    public PriceStrategy getPricing(VehicleType vt) {
        return strategyMap.get(vt);
    }

    public EntryGate getEntryGate() { return entryGate; }
    public ExitGate getExitGate() { return exitGate; }
    public ParkingManager getManager() { return manager; }
    public Map<String, Ticket> getActiveTickets() { return activeTickets; }
    public List<ParkingFloor> getFloors(){ return floors; }
}


// ===========================================================
// MANAGER
// ===========================================================
class ParkingManager {
    private ParkingLot lot;

    public ParkingManager(ParkingLot lot) { this.lot = lot; }

    public void printStatus() {
        System.out.println("------ Parking Status ------");
        for(ParkingFloor f : lot.getFloors()) {
            System.out.println("Floor " + f.getFloorNo());
            long occ = f.getSpots().stream().filter(ParkingSpot::isOccupied).count();
            System.out.println("Occupied: " + occ + " / " + f.getSpots().size());
        }
    }
}
```
