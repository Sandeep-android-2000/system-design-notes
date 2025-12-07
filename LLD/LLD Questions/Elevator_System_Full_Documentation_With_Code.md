# Elevator System — Complete Beginner-Friendly Documentation

This document provides a full, easy-to-understand explanation of a **multi-elevator system**, including:

- Requirements (original + corrected important ones)
- Entities involved
- Relationships
- UML Diagram (PlantUML)
- Detailed Explanation
- Java Code (Multi-file Architecture Explanation)

---

# 1. Requirements

## 1.1 High-Level Functional Requirements
1. The system manages **multiple elevators** in a building.  
2. Each elevator should move between floors, open/close doors, and serve requests.  
3. Requests come from:
   - **External floor buttons** (UP/DOWN)
   - **Internal elevator buttons** (selected floor)

---

# 1.2 *Important Requirements (Your Clarified Core Rules)*

### **1. Two Scheduling Strategies**
The system must support two pluggable strategies:

#### **A. Nearest Suitable Strategy**
- Choose the elevator closest to request floor.
- Consider direction:
  - If elevator is going UP → serve only floors above.
  - If elevator is idle → suitable for any direction.

#### **B. Least Busy Strategy**
- Choose the elevator with the **fewest pending requests**.
- Good load balancing.

---

### **2. External Dispatcher**
A central **Dispatcher**:
- Receives external requests: `(floor, direction)`
- Applies the chosen scheduling strategy
- Selects the best elevator
- Assigns request to that elevator

---

### **3. Elevator Request Buckets**
Each elevator keeps its **own internal request storage**, split by direction:

- `upRequests` → TreeSet ascending  
- `downRequests` → TreeSet descending

Benefits:
- Perfectly ordered servicing  
- No zig-zag behavior  
- Efficient movement  

Once a request is assigned → **cannot be moved to another elevator**.

---

### **4. Internal Requests Must Stay with Same Elevator**
When inside elevator:
- User presses floor button  
→ Must be handled by **that same elevator**  
→ Never reassigned by scheduler  

This mirrors real-world elevator rules.

---

### **5. Idle/Wake Behavior**
- When an elevator has **no requests**, it becomes **IDLE**.  
- It wakes up automatically when:
  - Dispatcher assigns new external floor request  
  - Passenger inside adds new internal destination  

When waking:
- Elevator picks direction based on nearest pending request.

---

### **6. Direction-Ordered Requests**
Requests must be served in **strict order based on direction**:

- UP direction → serve floors in ascending order  
- DOWN direction → serve floors in descending order  

Uses:
- `TreeSet<Integer>` for sorted handling

---

# 2. Entities Involved

## **1. Dispatcher**
- Receives external floor requests
- Uses strategy to choose elevator
- Sends request to elevator

## **2. Scheduling Strategies**
Two classes implementing interface:

- `NearestSuitableStrategy`
- `LeastBusyStrategy`

## **3. Elevator**
Attributes:
- Current floor
- Direction (UP/DOWN/IDLE)
- Door state (OPEN/CLOSED)
- `upRequests` bucket (ascending)
- `downRequests` bucket (descending)

Methods:
- `addExternalRequest`
- `addInternalRequest`
- `step()` (moves elevator one floor)
- `scoreForRequest` (used in scheduling)

---

## **4. ElevatorController**
- Oversees all elevators  
- Bridges Dispatcher → Elevators  
- Handles internal requests  
- Runs simulation steps  

---

## **5. Request**
Represents:
- Floor number
- Direction
- Internal/external type
- Time created

---

# 3. Relationship Between Entities

| Entity | Relationship |
|--------|-------------|
| Dispatcher → Strategies | Uses selected strategy to pick elevator |
| Dispatcher → ElevatorController | Communicates decisions |
| ElevatorController → Elevators | Manages list of elevators |
| Elevator → Request | Each elevator stores its own requests |
| Elevators → Controller | Sends status updates |

---

# 4. UML Diagram (PlantUML)

```
@startuml
interface SchedulingStrategy {
  + assignElevator(elevators: List<Elevator>, request: Request): Elevator
}

class NearestSuitableStrategy implements SchedulingStrategy
class LeastBusyStrategy implements SchedulingStrategy

class Dispatcher {
  - strategy: SchedulingStrategy
  + dispatch(request: Request, elevators: List<Elevator>): Elevator
}

class Elevator {
  - id: int
  - currentFloor: int
  - direction: Direction
  - upRequests: TreeSet<int>
  - downRequests: TreeSet<int>
  + addExternalRequest(req: Request)
  + addInternalRequest(floor: int)
  + step(): void
  + scoreForRequest(req: Request): int
}

class ElevatorController {
  - elevators: List<Elevator>
  - dispatcher: Dispatcher
  + externalRequest(floor:int, dir:Direction)
  + internalRequest(elevatorId:int, floor:int)
  + stepSimulation(): void
}

class Request {
  - floor: int
  - direction: Direction
  - internal: boolean
}

enum Direction { UP, DOWN, IDLE }
enum DoorState { OPEN, CLOSED }

Dispatcher --> SchedulingStrategy
Dispatcher --> Elevator
ElevatorController --> Elevator
Elevator --> Request
@enduml
```

---

# 5. Java Code (Structure Overview)

The actual Java files are structured as:

```
elevator.system/
 ├── Direction.java
 ├── DoorState.java
 ├── Request.java
 ├── SchedulingStrategy.java
 ├── NearestSuitableStrategy.java
 ├── LeastBusyStrategy.java
 ├── Dispatcher.java
 ├── Elevator.java
 ├── ElevatorController.java
 └── ElevatorApp.java (main)
```

Each file implements a clean, modular version of the elevator system.

---

# 6. Download the Full Java Multi-file Project

A fully prepared Java project ZIP file (multi-file, clean architecture) is available here:

👉 **[Download elevator_system_multi.zip](sandbox:/mnt/data/elevator_system_multi.zip)**

---

# 7. Next Improvements (Optional)

You may expand the system by adding:

- Multi-threading (each elevator runs independently)
- Fire/emergency mode
- UI simulation (Swing/JavaFX/Web)
- Group controller logic (real elevator algorithms)

---

If you'd like, I can generate:

✅ A **PDF version**  
✅ A **DOCX version**  
✅ A **more advanced elevator scheduling system**  
✅ A step-by-step **LLD interview explanation**  

Just tell me!

---
# 8. Full Java Code (All Files)

## `Direction.java`

```java
package elevator.system;

public enum Direction {
    UP, DOWN, IDLE
}

```

## `Dispatcher.java`

```java
package elevator.system;

import java.util.List;

public class Dispatcher {
    private SchedulingStrategy strategy;

    public Dispatcher(SchedulingStrategy strategy) {
        this.strategy = strategy;
    }

    public void setStrategy(SchedulingStrategy strategy) {
        this.strategy = strategy;
    }

    /**
     * Choose an elevator for the external request and returns it.
     * Returns null if no suitable elevator is found.
     */
    public Elevator dispatch(Request request, List<Elevator> elevators) {
        if (strategy == null) return null;
        return strategy.assignElevator(elevators, request);
    }
}

```

## `DoorState.java`

```java
package elevator.system;

public enum DoorState {
    OPEN, CLOSED
}

```

## `Elevator.java`

```java
package elevator.system;

import java.util.Collections;
import java.util.TreeSet;

public class Elevator {
    private final int id;
    private int currentFloor;
    private Direction direction = Direction.IDLE;
    private DoorState doorState = DoorState.CLOSED;

    // ordered request buckets
    private final TreeSet<Integer> upRequests = new TreeSet<>();
    private final TreeSet<Integer> downRequests = new TreeSet<>(Collections.reverseOrder());

    private final int minFloor = 1;
    private final int maxFloor;

    public Elevator(int id, int initialFloor, int maxFloor) {
        this.id = id;
        this.currentFloor = initialFloor;
        this.maxFloor = maxFloor;
    }

    public int getId() { return id; }
    public int getCurrentFloor() { return currentFloor; }
    public Direction getDirection() { return direction; }
    public DoorState getDoorState() { return doorState; }

    public synchronized void addExternalRequest(Request req) {
        int floor = req.getFloor();
        if (floor == currentFloor) {
            // immediate stop simulated
            openDoor();
            return;
        }
        if (floor > currentFloor) {
            upRequests.add(floor);
            if (direction == Direction.IDLE) direction = Direction.UP;
        } else {
            downRequests.add(floor);
            if (direction == Direction.IDLE) direction = Direction.DOWN;
        }
    }

    public synchronized void addInternalRequest(int floor) {
        if (floor == currentFloor) {
            openDoor();
            return;
        }
        if (floor > currentFloor) {
            upRequests.add(floor);
            if (direction == Direction.IDLE) direction = Direction.UP;
        } else {
            downRequests.add(floor);
            if (direction == Direction.IDLE) direction = Direction.DOWN;
        }
    }

    public synchronized void step() {
        if (doorState == DoorState.OPEN) {
            // close door on next step
            closeDoor();
            return;
        }

        if (direction == Direction.IDLE) {
            if (!upRequests.isEmpty()) direction = Direction.UP;
            else if (!downRequests.isEmpty()) direction = Direction.DOWN;
            else return;
        }

        if (direction == Direction.UP) {
            if (currentFloor < maxFloor) currentFloor++;
            System.out.println("Elevator " + id + " moved UP to " + currentFloor);
            if (upRequests.contains(currentFloor)) {
                upRequests.remove(currentFloor);
                openDoor();
            }
            if (upRequests.isEmpty() && !downRequests.isEmpty() && doorState == DoorState.CLOSED) {
                direction = Direction.DOWN;
            }
        } else if (direction == Direction.DOWN) {
            if (currentFloor > minFloor) currentFloor--;
            System.out.println("Elevator " + id + " moved DOWN to " + currentFloor);
            if (downRequests.contains(currentFloor)) {
                downRequests.remove(currentFloor);
                openDoor();
            }
            if (downRequests.isEmpty() && !upRequests.isEmpty() && doorState == DoorState.CLOSED) {
                direction = Direction.UP;
            }
        }

        if (upRequests.isEmpty() && downRequests.isEmpty() && doorState == DoorState.CLOSED) {
            direction = Direction.IDLE;
        }
    }

    private void openDoor() {
        doorState = DoorState.OPEN;
        System.out.println("Elevator " + id + " DOOR OPEN at " + currentFloor);
    }

    private void closeDoor() {
        doorState = DoorState.CLOSED;
        System.out.println("Elevator " + id + " DOOR CLOSED at " + currentFloor);
    }

    /**
     * Returns score (lower better) for nearest-suitable selection.
     * Penalizes elevators that would need to reverse direction.
     */
    public synchronized int scoreForRequest(Request req) {
        int distance = Math.abs(currentFloor - req.getFloor());
        if (direction == Direction.IDLE) return distance;
        if (direction == Direction.UP && req.getFloor() >= currentFloor && req.getDirection() == Direction.UP) {
            return distance;
        }
        if (direction == Direction.DOWN && req.getFloor() <= currentFloor && req.getDirection() == Direction.DOWN) {
            return distance;
        }
        return distance + 1000; // large penalty
    }

    public synchronized int pendingRequestCount() {
        return upRequests.size() + downRequests.size();
    }

    public synchronized String status() {
        return String.format("E%d[f=%d,d=%s,door=%s,up=%s,down=%s]", id, currentFloor, direction, doorState, upRequests, downRequests);
    }
}

```

## `ElevatorApp.java`

```java
package elevator.system;

public class ElevatorApp {

    public static void main(String[] args) throws InterruptedException {
        int floors = 10;
        // start with nearest suitable strategy
        SchedulingStrategy nearest = new NearestSuitableStrategy();
        ElevatorController controller = new ElevatorController(3, floors, nearest);

        // simulate some external calls
        controller.externalRequest(3, Direction.UP);
        controller.externalRequest(7, Direction.DOWN);

        Thread.sleep(800);
        // simulate internal requests (must be served by same elevator)
        controller.internalRequest(1, 9); // someone inside elevator 1 presses 9
        controller.internalRequest(2, 1);

        // switch strategy mid-run to least busy (demonstrate capability)
        System.out.println("\nSwitching to LeastBusyStrategy\n");
        controller.getDispatcher().setStrategy(new LeastBusyStrategy());

        controller.externalRequest(2, Direction.UP);
        controller.externalRequest(8, Direction.DOWN);

        // run simulation steps
        for (int i = 0; i < 40; i++) {
            System.out.println("\n--- Simulation step " + (i+1) + " ---");
            controller.stepSimulation();
            Thread.sleep(600);
        }

        System.out.println("Simulation ended.");
    }
}

```

## `ElevatorController.java`

```java
package elevator.system;

import java.util.ArrayList;
import java.util.List;

public class ElevatorController {
    private final List<Elevator> elevators = new ArrayList<>();
    private final Dispatcher dispatcher;

    public ElevatorController(int elevatorCount, int floors, SchedulingStrategy strategy) {
        for (int i = 1; i <= elevatorCount; i++) {
            elevators.add(new Elevator(i, 1, floors));
        }
        this.dispatcher = new Dispatcher(strategy);
    }

    public List<Elevator> getElevators() { return elevators; }
    public Dispatcher getDispatcher() { return dispatcher; }

    // external request from a floor
    public void externalRequest(int floor, Direction dir) {
        Request req = new Request(floor, dir, false);
        System.out.println("Controller received external " + req);
        Elevator chosen = dispatcher.dispatch(req, elevators);
        if (chosen == null) {
            System.out.println("No elevator available for " + req + ", request dropped or queued externally.");
            return;
        }
        System.out.println("Dispatcher assigned Elevator " + chosen.getId() + " for " + req);
        chosen.addExternalRequest(req);
    }

    // internal request from inside an elevator: must be served by same elevator
    public void internalRequest(int elevatorId, int floor) {
        Elevator e = findElevator(elevatorId);
        if (e == null) {
            System.out.println("Invalid elevator id " + elevatorId);
            return;
        }
        System.out.println("Controller received internal request for E" + elevatorId + " -> " + floor);
        e.addInternalRequest(floor);
    }

    public void stepSimulation() {
        for (Elevator e : elevators) {
            e.step();
        }
        printStatus();
    }

    public Elevator findElevator(int id) {
        for (Elevator e : elevators) if (e.getId() == id) return e;
        return null;
    }

    public void printStatus() {
        System.out.println("-- Controller Status --");
        for (Elevator e : elevators) {
            System.out.println(e.status());
        }
    }
}

```

## `LeastBusyStrategy.java`

```java
package elevator.system;

import java.util.List;

public class LeastBusyStrategy implements SchedulingStrategy {

    @Override
    public Elevator assignElevator(List<Elevator> elevators, Request request) {
        Elevator best = null;
        int minLoad = Integer.MAX_VALUE;
        for (Elevator e : elevators) {
            int load = e.pendingRequestCount();
            if (load < minLoad) {
                minLoad = load;
                best = e;
            }
        }
        return best;
    }
}

```

## `NearestSuitableStrategy.java`

```java
package elevator.system;

import java.util.List;

public class NearestSuitableStrategy implements SchedulingStrategy {

    @Override
    public Elevator assignElevator(List<Elevator> elevators, Request request) {
        Elevator best = null;
        int bestScore = Integer.MAX_VALUE;
        for (Elevator e : elevators) {
            int score = e.scoreForRequest(request);
            if (score < bestScore) {
                bestScore = score;
                best = e;
            }
        }
        return best;
    }
}

```

## `Request.java`

```java
package elevator.system;

import java.time.LocalDateTime;

public class Request {
    private final int floor;
    private final Direction direction;
    private final boolean internal;
    private final LocalDateTime time;

    public Request(int floor, Direction direction, boolean internal) {
        this.floor = floor;
        this.direction = direction;
        this.internal = internal;
        this.time = LocalDateTime.now();
    }

    public int getFloor() { return floor; }
    public Direction getDirection() { return direction; }
    public boolean isInternal() { return internal; }
    public LocalDateTime getTime() { return time; }

    @Override
    public String toString() {
        return "Request[floor=" + floor + ", dir=" + direction + ", internal=" + internal + "]";
    }
}

```

## `SchedulingStrategy.java`

```java
package elevator.system;

import java.util.List;

public interface SchedulingStrategy {
    Elevator assignElevator(List<Elevator> elevators, Request request);
}

```

