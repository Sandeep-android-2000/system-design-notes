# Car Rental System — Low Level Design (LLD)

---

## 1. Introduction

These notes provide a **very detailed, interview-ready Low Level Design (LLD)** for a **Car Rental System**, created strictly using:
- The complete transcript you provided
- The detailed UML diagrams (Excalidraw screenshots)

The intent of this document is not just to show *what* classes exist, but to clearly explain:
- **Why each class exists**
- **What responsibility it owns**
- **How objects collaborate**
- **How concurrency is safely handled**

This style of documentation is ideal for:
- LLD interview preparation
- Revision notes before interviews
- Explaining design decisions confidently as a senior engineer

---

## 2. Problem Statement

Design a **Car Rental System** where:
- Users can rent vehicles from nearby stores
- Vehicles can be booked for a date range
- Two users must NOT be able to book the same vehicle for overlapping dates
- Billing and payment should be flexible

The system should be extensible to support:
- Two-wheelers, four-wheelers
- Hourly and daily billing
- Multiple payment modes

---

## 3. User Flow (Step-by-Step)

```
User
 → Select Location
   → Select Store
     → Search Vehicles (VehicleType + Date Range)
       → Select Vehicle
         → Create Reservation
           → Start Trip
             → Submit Vehicle
               → Generate Bill
                 → Make Payment
```

This flow is **central** to the entire design and drives entity identification.

---

## 4. How Entities Were Identified

While analyzing the flow, we list down **nouns** and **responsibility holders**:

- User
- Vehicle
- Store
- Reservation
- Bill
- Payment

Next, we identify **behavior-heavy objects** (managers):

- VehicleInventoryManager
- ReservationManager
- BillManager
- PaymentManager

And finally **supporting components**:

- ReservationRepository (storage)
- Strategy interfaces (varying logic)

---

## 5. Core Domain Entities (POJO Classes)

### 5.1 User

```java
class User {
    int userId;
    String userName;
    String drivingLicenseNo;
}
```

User is a **simple data holder**. No business logic is placed here.

---

### 5.2 Vehicle

```java
class Vehicle {
    int vehicleId;
    String vehicleNo;
    VehicleType vehicleType;        // TWO_WHEELER, FOUR_WHEELER
    int dailyRentalCost;
    VehicleStatus status;           // AVAILABLE, BOOKED, MAINTENANCE
}
```

Important Notes:
- Vehicle is **not intelligent**
- It does NOT decide availability
- It does NOT handle booking

All such logic is delegated to the inventory manager.

---

## 6. Store — Central Orchestrator

```java
class Store {
    int storeId;
    Location location;

    VehicleInventoryManager inventoryManager;
    ReservationManager reservationManager;
    BillManager billManager;
    PaymentManager paymentManager;
}
```

Why Store exists:
- Represents a physical rental outlet
- Acts as an **orchestrator**, not a logic-heavy class
- Delegates responsibilities to managers

---

## 7. VehicleInventoryManager (Exact as UML – Core Concurrency Component)

This class is **the most critical part of the system** and should be explained very clearly in interviews.

It is **NOT a singleton**. Each `Store` owns its own `VehicleInventoryManager`.

---

### 7.1 Internal Data Structures (from UML)

```java
class VehicleInventoryManager {

    // vehicleId -> Vehicle
    ConcurrentHashMap<Integer, Vehicle> allVehicles;

    // vehicleId -> list of reservationIds
    ConcurrentHashMap<Integer, List<Integer>> vehicleIdVsReservationIds;

    // used to fetch reservation details using reservationId
    ReservationRepository repo;

    // lock per vehicle (vehicleId -> lock)
    ConcurrentHashMap<Integer, ReentrantLock> vehicleLocks;
}
```

---

### 7.2 Why These Maps Exist

1. **allVehicles**  
   Holds all vehicles present in the store. Pure inventory data.

2. **vehicleIdVsReservationIds**  
   Maintains booking history for each vehicle.  
   A vehicle can have multiple reservations for different date ranges.

3. **ReservationRepository dependency**  
   Inventory only stores reservation IDs.  
   Actual reservation details (date range) are fetched from repository.

4. **vehicleLocks**  
   Ensures **vehicle-level atomicity** during booking and release.

---

### 7.3 Public Methods (as per UML)

```java
+ addVehicle(Vehicle v)
+ Vehicle getVehicle(int vehicleId)
+ boolean isAvailable(int vehicleId, Date from, Date to)
+ boolean bookVehicle(int vehicleId, Date from, Date to)
+ void releaseVehicle(int vehicleId, int reservationId)
+ void putLockOnVehicle(int vehicleId)
```

---

### 7.4 Availability Check Logic (Very Important)

For a given vehicle:
- Fetch all reservationIds
- For each reservationId:
  - Fetch Reservation from repository
  - Check date overlap

**Non-overlapping conditions:**
- New booking ends before existing booking starts
- New booking starts after existing booking ends

Any other case → overlap → NOT available

---

### 7.5 Booking Flow (Vehicle-Level Locking)

```text
1. User selects vehicle
2. InventoryManager.putLockOnVehicle(vehicleId)
3. Acquire lock
4. Re-check availability
5. If available:
     - add reservationId to map
     - update vehicle status to BOOKED
6. Release lock
```

This double-check ensures **no double booking** even under high concurrency.

---

### 7.6 Why Lock Per Vehicle (Not Global)

- Locking entire inventory would block other users
- Vehicle-level lock allows parallel booking of different vehicles
- Scales better under load

---



## 8. Reservation

```java
class Reservation {
    int reservationId;
    int vehicleId;
    int userId;
    Date bookedFrom;
    Date bookedTo;
    ReservationType type;     // DAILY, HOURLY
    ReservationStatus status; // SCHEDULED, IN_USE, COMPLETED, CANCELLED
}
```

Design Decision:
- Store only IDs (vehicleId, userId)
- Avoid heavy object graphs

---

## 9. ReservationRepository (Breaking Circular Dependency)

```java
class ReservationRepository {
    ConcurrentMap<Integer, Reservation> reservations;
}
```

Why this class exists:

- ReservationManager needs reservation data
- VehicleInventoryManager also needs reservation data
- Direct dependency would cause **circular reference**

Solution:
- Move reservation storage into a repository
- Both managers depend on the repository

---

## 10. ReservationManager

```java
class ReservationManager {

    VehicleInventoryManager inventory;
    ReservationRepository repository;
}
```

Responsibilities:
- Create reservation
- Cancel reservation
- Start trip
- Complete trip

Key Rule:
- Reservation is created **only after inventory confirms booking**

---

## 11. Concurrency Handling (ReentrantLock — Beginner Friendly Explanation)

This section explains **step-by-step**, in very simple terms, how **ReentrantLock** is actually used in the `VehicleInventoryManager`.

If you felt earlier that locking looked like pseudocode — you were right. This section now shows **real, concrete Java logic** that exactly matches your UML.

---

## 11.1 Why ConcurrentMap Alone Is NOT Enough

Even if we use `ConcurrentMap`, the following problem still happens:

```text
Thread T1 checks availability → sees vehicle is free
Thread T2 checks availability → sees vehicle is free
T1 books vehicle
T2 books SAME vehicle
```

✅ Data structure is thread-safe
❌ Business logic is NOT thread-safe

So we need **explicit locking**.

---

## 11.2 Locking Design (As Per Your UML)

In `VehicleInventoryManager` we maintain:

```java
ConcurrentMap<Integer, ReentrantLock> vehicleLocks;
```

### Meaning
- **Key** → vehicleId
- **Value** → lock object for that specific vehicle

👉 Each vehicle has **its own lock**.

---

## 11.3 Creating / Fetching Lock for a Vehicle

This method comes **directly from your UML**.

```java
private ReentrantLock lockVehicle(int vehicleId) {
    vehicleLocks.putIfAbsent(vehicleId, new ReentrantLock());
    return vehicleLocks.get(vehicleId);
}
```

### Explanation (Line by Line)

1. `putIfAbsent` ensures:
   - If lock already exists → reuse it
   - If not → create a new one

2. This operation is **thread-safe** because `vehicleLocks` is a `ConcurrentMap`.

3. Same vehicleId will always get the **same lock object**.

---

## 11.4 Booking a Vehicle (ACTUAL Lock Usage)

Below is the **real implementation**, not pseudocode.

```java
public boolean bookVehicle(int vehicleId, int reservationId, Date from, Date to) {

    ReentrantLock lock = lockVehicle(vehicleId);
    lock.lock();   // 🔒 acquire lock

    try {
        // Step 1: re-check availability AFTER acquiring lock
        if (!isAvailable(vehicleId, from, to)) {
            return false;
        }

        // Step 2: perform booking safely
        vehicleIdVsReservationIds
            .computeIfAbsent(vehicleId, k -> new ArrayList<>())
            .add(reservationId);

        allVehicles.get(vehicleId).status = VehicleStatus.BOOKED;
        return true;

    } finally {
        lock.unlock();   // 🔓 release lock (ALWAYS in finally)
    }
}
```

---

## 11.5 Why Lock Is Released in finally

If an exception happens and lock is not released:
- Vehicle stays locked forever
- System breaks

Using `finally` guarantees:
- Lock is always released

---

## 11.6 Releasing a Vehicle (Also Uses Lock)

```java
public void releaseVehicle(int vehicleId, int reservationId) {

    ReentrantLock lock = lockVehicle(vehicleId);
    lock.lock();

    try {
        vehicleIdVsReservationIds
            .get(vehicleId)
            .remove(Integer.valueOf(reservationId));

        allVehicles.get(vehicleId).status = VehicleStatus.AVAILABLE;

    } finally {
        lock.unlock();
    }
}
```

---

## 11.7 Full Thread Scenario (Very Important)

### Without Lock

```text
T1 → checks availability → free
T2 → checks availability → free
T1 → books
T2 → books ❌
```

### With Lock

```text
T1 → acquires lock
T1 → checks availability → free
T1 → books
T1 → releases lock

T2 → acquires lock
T2 → checks availability → NOT free
T2 → fails
```

✅ No double booking

---

## 11.8 Why Lock Per Vehicle (Not Per Inventory)

- Multiple users can book different vehicles simultaneously
- Only same vehicle is serialized
- Much better scalability

---

## 11.9 Interview Explanation (One Line)

> "We use a `ConcurrentMap<vehicleId, ReentrantLock>` and acquire the lock before checking availability and booking, ensuring atomicity at vehicle level."

---


## 12. Billing Design

### 12.1 Bill

```java
class Bill {
    int billId;
    int reservationId;
    double billAmount;
    boolean isPaid;
}
```

Bill stores **minimal required data**.

---

### 12.2 Billing Strategy (Strategy Pattern)

```java
interface BillStrategy {
    Bill generateBill(Reservation reservation);
}
```

#### Implementations

```java
class DailyBillStrategy implements BillStrategy {}
class HourlyBillStrategy implements BillStrategy {}
```

Why Strategy Pattern:
- Billing logic varies
- Easy to add new billing types

---

## 13. Payment Design

### 13.1 Payment

```java
class Payment {
    int paymentId;
    int billId;
    double amountPaid;
    PaymentMode mode; // CASH, UPI, CREDIT
}
```

---

### 13.2 Payment Strategy

```java
interface PaymentStrategy {
    void makePayment(Bill bill, double amount);
}
```

Implementations:
- UPIPaymentStrategy
- CardPaymentStrategy

---

## 14. Managers for Billing & Payment

```java
class BillManager {
    ConcurrentMap<Integer, Bill> bills;
    BillStrategy strategy;
}

class PaymentManager {
    ConcurrentMap<Integer, Payment> payments;
    PaymentStrategy payStrategy;
}
```

---

## 15. VehicleRentalSystem (Root Object)

```java
class VehicleRentalSystem {
    List<Store> stores;
    List<User> users;
}
```

Responsibilities:
- Maintain list of stores
- Maintain list of users
- Entry point to system

---

## 16. UML Diagram

[![Car Rental UML](https://github.com/user-attachments/assets/5bb688c8-a36e-40fd-8a7c-46f8605364b3)]
(https://github.com/user-attachments/assets/5bb688c8-a36e-40fd-8a7c-46f8605364b3)


---

## 17. Final Summary (How to Explain in Interview)

- Start from user flow
- Identify core entities
- Introduce managers for behavior
- Use repositories to avoid circular dependencies
- Handle concurrency with vehicle-level locks
- Use strategy pattern where logic varies

This design is **clean, extensible, concurrency-safe, and interview-ready**.

---

---

## 18. Complete Java Code (LLD Reference Implementation)

> **Note**: This is an in-memory, interview-focused implementation. Thread-safety and structure match the UML.

---

### 18.1 Enums

```java
enum VehicleType {
    TWO_WHEELER,
    FOUR_WHEELER
}

enum VehicleStatus {
    AVAILABLE,
    BOOKED,
    MAINTENANCE
}

enum ReservationType {
    DAILY,
    HOURLY
}

enum ReservationStatus {
    SCHEDULED,
    IN_USE,
    COMPLETED,
    CANCELLED
}

enum PaymentMode {
    CASH,
    UPI,
    CREDIT
}
```

---

### 18.2 Core POJOs

```java
class User {
    int userId;
    String userName;
    String drivingLicenseNo;
}

class Vehicle {
    int vehicleId;
    String vehicleNo;
    VehicleType vehicleType;
    int dailyRentalCost;
    VehicleStatus status;
}

class Reservation {
    int reservationId;
    int vehicleId;
    int userId;
    Date bookedFrom;
    Date bookedTo;
    ReservationType type;
    ReservationStatus status;
}

class Bill {
    int billId;
    int reservationId;
    double billAmount;
    boolean isPaid;
}

class Payment {
    int paymentId;
    int billId;
    double amountPaid;
    PaymentMode paymentMode;
}
```

---

### 18.3 Reservation Repository

```java
class ReservationRepository {
    private ConcurrentMap<Integer, Reservation> reservations = new ConcurrentHashMap<>();

    void save(Reservation r) {
        reservations.put(r.reservationId, r);
    }

    Reservation findById(int id) {
        return reservations.get(id);
    }

    void remove(int id) {
        reservations.remove(id);
    }
}
```

---

### 18.4 VehicleInventoryManager (Concurrency Core)

```java
class VehicleInventoryManager {

    ConcurrentMap<Integer, Vehicle> vehicles = new ConcurrentHashMap<>();
    ConcurrentMap<Integer, List<Integer>> vehicleVsReservationIds = new ConcurrentHashMap<>();
    ConcurrentMap<Integer, ReentrantLock> vehicleLocks = new ConcurrentHashMap<>();

    ReservationRepository reservationRepo;

    VehicleInventoryManager(ReservationRepository repo) {
        this.reservationRepo = repo;
    }

    void addVehicle(Vehicle v) {
        vehicles.put(v.vehicleId, v);
        vehicleVsReservationIds.put(v.vehicleId, new ArrayList<>());
        vehicleLocks.put(v.vehicleId, new ReentrantLock());
    }

    boolean isAvailable(int vehicleId, Date from, Date to) {
        Vehicle v = vehicles.get(vehicleId);
        if (v.status == VehicleStatus.MAINTENANCE) return false;

        for (Integer resId : vehicleVsReservationIds.get(vehicleId)) {
            Reservation r = reservationRepo.findById(resId);
            if (!(to.before(r.bookedFrom) || from.after(r.bookedTo))) {
                return false;
            }
        }
        return true;
    }

    boolean reserveVehicle(int vehicleId, int reservationId, Date from, Date to) {
        ReentrantLock lock = vehicleLocks.get(vehicleId);
        lock.lock();
        try {
            if (!isAvailable(vehicleId, from, to)) return false;
            vehicleVsReservationIds.get(vehicleId).add(reservationId);
            vehicles.get(vehicleId).status = VehicleStatus.BOOKED;
            return true;
        } finally {
            lock.unlock();
        }
    }

    void releaseVehicle(int vehicleId, int reservationId) {
        ReentrantLock lock = vehicleLocks.get(vehicleId);
        lock.lock();
        try {
            vehicleVsReservationIds.get(vehicleId).remove(Integer.valueOf(reservationId));
            vehicles.get(vehicleId).status = VehicleStatus.AVAILABLE;
        } finally {
            lock.unlock();
        }
    }
}
```

---

### 18.5 ReservationManager

```java
class ReservationManager {

    VehicleInventoryManager inventory;
    ReservationRepository repository;

    AtomicInteger reservationCounter = new AtomicInteger(1);

    ReservationManager(VehicleInventoryManager inventory, ReservationRepository repo) {
        this.inventory = inventory;
        this.repository = repo;
    }

    Reservation createReservation(int vehicleId, int userId, Date from, Date to, ReservationType type) {
        int resId = reservationCounter.getAndIncrement();
        if (!inventory.reserveVehicle(vehicleId, resId, from, to)) {
            throw new RuntimeException("Vehicle not available");
        }
        Reservation r = new Reservation();
        r.reservationId = resId;
        r.vehicleId = vehicleId;
        r.userId = userId;
        r.bookedFrom = from;
        r.bookedTo = to;
        r.type = type;
        r.status = ReservationStatus.SCHEDULED;
        repository.save(r);
        return r;
    }

    void startTrip(int resId) {
        repository.findById(resId).status = ReservationStatus.IN_USE;
    }

    void completeTrip(int resId) {
        Reservation r = repository.findById(resId);
        r.status = ReservationStatus.COMPLETED;
        inventory.releaseVehicle(r.vehicleId, resId);
    }
}
```

---

### 18.6 Billing Strategy & Manager

```java
interface BillStrategy {
    Bill generateBill(Reservation r);
}

class DailyBillStrategy implements BillStrategy {
    VehicleInventoryManager inventory;

    DailyBillStrategy(VehicleInventoryManager inv) {
        this.inventory = inv;
    }

    public Bill generateBill(Reservation r) {
        Vehicle v = inventory.vehicles.get(r.vehicleId);
        long days = (r.bookedTo.getTime() - r.bookedFrom.getTime()) / (1000 * 60 * 60 * 24) + 1;
        Bill b = new Bill();
        b.billAmount = days * v.dailyRentalCost;
        b.reservationId = r.reservationId;
        b.isPaid = false;
        return b;
    }
}

class BillManager {
    ConcurrentMap<Integer, Bill> bills = new ConcurrentHashMap<>();
    AtomicInteger billCounter = new AtomicInteger(1);
    BillStrategy strategy;

    Bill generateBill(Reservation r) {
        Bill b = strategy.generateBill(r);
        b.billId = billCounter.getAndIncrement();
        bills.put(b.billId, b);
        return b;
    }
}
```

---

### 18.7 Payment Strategy & Manager

```java
interface PaymentStrategy {
    void makePayment(Bill bill, double amount);
}

class UPIPaymentStrategy implements PaymentStrategy {
    public void makePayment(Bill bill, double amount) {
        bill.isPaid = true;
    }
}

class PaymentManager {
    ConcurrentMap<Integer, Payment> payments = new ConcurrentHashMap<>();
    AtomicInteger paymentCounter = new AtomicInteger(1);
    PaymentStrategy strategy;

    void makePayment(Bill bill, double amount, PaymentMode mode) {
        strategy.makePayment(bill, amount);
        Payment p = new Payment();
        p.paymentId = paymentCounter.getAndIncrement();
        p.billId = bill.billId;
        p.amountPaid = amount;
        p.paymentMode = mode;
        payments.put(p.paymentId, p);
    }
}
```

---

### 18.8 Store & Driver Class

```java
class Store {
    int storeId;

    VehicleInventoryManager inventory;
    ReservationManager reservationManager;
    BillManager billManager;
    PaymentManager paymentManager;

    Store() {
        ReservationRepository repo = new ReservationRepository();
        inventory = new VehicleInventoryManager(repo);
        reservationManager = new ReservationManager(inventory, repo);
        billManager = new BillManager();
        paymentManager = new PaymentManager();
    }
}

public class Driver {
    public static void main(String[] args) {
        Store store = new Store();

        Vehicle car = new Vehicle();
        car.vehicleId = 1;
        car.vehicleNo = "DL01AB1234";
        car.vehicleType = VehicleType.FOUR_WHEELER;
        car.dailyRentalCost = 1000;
        car.status = VehicleStatus.AVAILABLE;

        store.inventory.addVehicle(car);

        Reservation r = store.reservationManager.createReservation(
                1, 101, new Date(), new Date(), ReservationType.DAILY);

        store.reservationManager.startTrip(r.reservationId);
        store.reservationManager.completeTrip(r.reservationId);

        store.billManager.strategy = new DailyBillStrategy(store.inventory);
        Bill bill = store.billManager.generateBill(r);

        store.paymentManager.strategy = new UPIPaymentStrategy();
        store.paymentManager.makePayment(bill, bill.billAmount, PaymentMode.UPI);

        System.out.println("Booking completed successfully");
    }
}
```

---

**End of Detailed Notes**

