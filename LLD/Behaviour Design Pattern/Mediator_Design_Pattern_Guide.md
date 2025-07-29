
# Mediator Design Pattern – A Beginner-Friendly Guide

## 🔷 Definition – What is the Mediator Design Pattern?

The **Mediator Design Pattern** is a **behavioral pattern** that provides a **centralized communication channel** between multiple objects (called **colleagues**). Instead of objects referring to each other directly, they **communicate via a Mediator** object.

### ✳ Goal:
To **reduce the direct dependencies** between interacting objects and **simplify communication logic**.

> "**Objects don't talk to each other directly — they talk to a mediator who coordinates everything.**"

---

## 🔷 Real-Life Analogy and Use Cases

### ✈️ Airline Traffic Control System
Imagine multiple airplanes trying to land or take off at an airport. If each airplane talks directly to others, there’s chaos and collision risk.

Instead, they all talk to **Air Traffic Control (ATC)** — a **Mediator** that knows the current status of all aircraft and manages coordination.

- **Planes** = Colleagues  
- **ATC** = Mediator

### 💸 Online Auction System
In an online auction:
- Buyers don’t directly talk to each other.
- They place bids via an **Auctioneer (Mediator)**.
- The auctioneer keeps track of the highest bid and notifies others when a new bid is placed.

> This helps **decouple** participants and maintain **clean coordination** logic.

---

## 🔷 Code Example – Java Implementation (Online Auction System)

### Step 1: Define the Mediator Interface

```java
interface AuctionMediator {
    void placeBid(Bidder bidder, int bidAmount);
    void registerBidder(Bidder bidder);
}
```

### Step 2: Concrete Mediator

```java
import java.util.ArrayList;
import java.util.List;

class Auction implements AuctionMediator {
    private List<Bidder> bidders = new ArrayList<>();
    private int highestBid = 0;
    private Bidder highestBidder = null;

    @Override
    public void registerBidder(Bidder bidder) {
        bidders.add(bidder);
    }

    @Override
    public void placeBid(Bidder bidder, int bidAmount) {
        if (bidAmount > highestBid) {
            highestBid = bidAmount;
            highestBidder = bidder;
            notifyBidders(bidder);
        } else {
            System.out.println(bidder.getName() + " tried to bid " + bidAmount + " but it's lower than current highest bid.");
        }
    }

    private void notifyBidders(Bidder newHighestBidder) {
        for (Bidder bidder : bidders) {
            if (bidder != newHighestBidder) {
                bidder.receiveNotification(newHighestBidder.getName(), highestBid);
            }
        }
    }
}
```

### Step 3: Colleague Interface and Class

```java
abstract class Bidder {
    protected AuctionMediator mediator;
    protected String name;

    public Bidder(AuctionMediator mediator, String name) {
        this.mediator = mediator;
        this.name = name;
        mediator.registerBidder(this);
    }

    public String getName() {
        return name;
    }

    public abstract void bid(int amount);
    public abstract void receiveNotification(String highestBidderName, int amount);
}

class ConcreteBidder extends Bidder {
    public ConcreteBidder(AuctionMediator mediator, String name) {
        super(mediator, name);
    }

    @Override
    public void bid(int amount) {
        System.out.println(name + " is bidding " + amount);
        mediator.placeBid(this, amount);
    }

    @Override
    public void receiveNotification(String highestBidderName, int amount) {
        System.out.println(name + " notified: Highest bid is now " + amount + " by " + highestBidderName);
    }
}
```

### Step 4: Run the Auction

```java
public class MediatorPatternDemo {
    public static void main(String[] args) {
        AuctionMediator auction = new Auction();

        Bidder bidder1 = new ConcreteBidder(auction, "Alice");
        Bidder bidder2 = new ConcreteBidder(auction, "Bob");
        Bidder bidder3 = new ConcreteBidder(auction, "Charlie");

        bidder1.bid(100);
        bidder2.bid(150);
        bidder3.bid(120); // Will be rejected
        bidder1.bid(200);
    }
}
```

---

## 🔷 Interview Insights

### 💬 How It’s Asked:
- **"Can you explain the Mediator Design Pattern with a real-world use case?"**
- **"How would you reduce tight coupling between objects in a chat app or flight system?"**
- **"Design an Auction System using a design pattern of your choice."**

### 🎯 How to Answer Concisely:
> “Mediator Pattern helps reduce tight coupling by centralizing communication between objects. Instead of objects talking directly to each other, they talk via a mediator, which coordinates and manages their interactions. This is useful in systems like auction platforms, chatrooms, and air traffic control.”

---

## 🔷 Key Benefits of Mediator Design Pattern

✅ **Loose Coupling** – Components don’t directly reference each other  
✅ **Centralized Control** – All interaction logic is placed in one place (Mediator)  
✅ **Easier Maintenance** – Changes in one component don’t impact others  
✅ **Improved Readability** – Cleaner and easier-to-read object communication  
✅ **Scalable Communication** – Easy to add new components without modifying existing ones

---

## 🔷 Best Practices

🔸 **Use Mediator when**:
- You have **many interacting classes** with **complex communication logic**.
- You want to **decouple** classes and **avoid spaghetti code**.

🔸 **Avoid Mediator when**:
- Only **2–3 objects** are communicating (adds unnecessary complexity).
- The **communication logic is too trivial** to warrant centralization.

🔸 **Common Use Cases**:
- GUI systems (buttons, forms)
- Chat rooms / messaging systems
- Auction platforms
- Microservice orchestration
- Air traffic control simulations

---

## 🎓 Summary for System Design Prep

When prepping for **system design interviews** or **LLD rounds**, keep in mind:

- Mediator is ideal for **orchestrating multiple components** that shouldn't know about each other directly.
- It **aligns well with real-world systems** like chat servers, pub-sub models, etc.
- Always show how this reduces complexity and improves scalability.
