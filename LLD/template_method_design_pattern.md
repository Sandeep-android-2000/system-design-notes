# Template Method Design Pattern

## 1. Pattern Category
- **Category**: **Behavioral Design Pattern**
- **Purpose**: Defines the **skeleton** of an algorithm in a method, deferring some steps to subclasses without changing the overall structure.

---

## 2. Why It Is Required and When to Use

**Why it is required**:
- When you have a **fixed sequence of steps** in an algorithm.
- But **some steps may differ** depending on the specific type of implementation (subclass).
- It promotes **code reuse** and **enforces a standard process**.

**When to use**:
- When multiple classes share the **same workflow** but implement **some specific steps differently**.
- When you want to **ensure a common sequence** is always followed.
- Ideal in **payment flows**, **data parsers**, **game engines**, etc.

---

## 3. Java Code Example: Payment Types (PayToFriend, PayToMerchant)

You want **all payment flows** to follow these steps:
> validate request → debit money → calculate fees → credit money

But:
- Validation logic may differ for friend vs merchant.
- Fee calculation may differ.
- Credit flow may differ.

We will make an **abstract base class** that defines the skeleton, and **subclasses** will fill in the details.

---

### Java Code Example

```java
// Step 1: Create the abstract class
abstract class PaymentTemplate {

    // Template method - defines the skeleton
    public final void processPayment() {
        validateRequest();
        debitMoney();
        calculateFees();
        creditMoney();
    }

    // Steps to be customized by subclasses
    protected abstract void validateRequest();
    protected abstract void debitMoney();
    protected abstract void calculateFees();
    protected abstract void creditMoney();
}
```

---

```java
// Step 2: Concrete class for Pay to Friend
class PayToFriend extends PaymentTemplate {

    @Override
    protected void validateRequest() {
        System.out.println("Validating pay to friend request...");
    }

    @Override
    protected void debitMoney() {
        System.out.println("Debiting money from user's account for friend...");
    }

    @Override
    protected void calculateFees() {
        System.out.println("No fee for paying to friend!");
    }

    @Override
    protected void creditMoney() {
        System.out.println("Crediting friend's account...");
    }
}
```

---

```java
// Step 3: Concrete class for Pay to Merchant
class PayToMerchant extends PaymentTemplate {

    @Override
    protected void validateRequest() {
        System.out.println("Validating pay to merchant request...");
    }

    @Override
    protected void debitMoney() {
        System.out.println("Debiting money from user's account for merchant...");
    }

    @Override
    protected void calculateFees() {
        System.out.println("Calculating and applying merchant fee...");
    }

    @Override
    protected void creditMoney() {
        System.out.println("Crediting merchant's account...");
    }
}
```

---

```java
// Step 4: Usage
public class PaymentProcessor {
    public static void main(String[] args) {
        PaymentTemplate friendPayment = new PayToFriend();
        friendPayment.processPayment();

        System.out.println("---------------------------");

        PaymentTemplate merchantPayment = new PayToMerchant();
        merchantPayment.processPayment();
    }
}
```

---

### Output

```
Validating pay to friend request...
Debiting money from user's account for friend...
No fee for paying to friend!
Crediting friend's account...
---------------------------
Validating pay to merchant request...
Debiting money from user's account for merchant...
Calculating and applying merchant fee...
Crediting merchant's account...
```

---

# Summary
- `PaymentTemplate` defines the **algorithm** (steps order is fixed).
- `PayToFriend` and `PayToMerchant` provide **specific implementations** of each step.
- Easy to add **more payment types** later (e.g., PayToCharity) without breaking the overall structure.
- Code remains **clean**, **reusable**, and **extensible**.

---

# Optional Enhancement
Would you also like to see **Hook Methods** for optional steps in Template Method pattern?

