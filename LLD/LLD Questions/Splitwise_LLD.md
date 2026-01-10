# Low Level Design (LLD) – Splitwise

## 1. Problem Statement

Design a **Splitwise-like expense sharing system** that allows users to:

- Add friends
- Create groups
- Add expenses (inside or outside a group)
- Split expenses using:
  - Equal split
  - Unequal split
  - Percentage split
- Maintain a **per-user balance sheet** showing:
  - How much a user owes
  - How much a user gets back
  - Friend-wise drill-down

The system must be **scalable, extensible, and interview-grade**.

---

## 2. Requirement Gathering

### 2.1 Happy Flow

1. User opens Splitwise
2. User adds friends
3. User creates groups (trip, home, office, etc.)
4. User adds friends to groups
5. User creates an expense:
   - Inside a group OR
   - Directly (without a group)
6. Expense is split among users
7. Balance sheets of all involved users are updated

---

### 2.2 Functional Requirements

1. Add / manage users
2. Add / manage groups
3. Add users to groups
4. Create expenses
5. Support multiple split types
6. Maintain balance sheet for each user
7. Provide friend-wise balance drill-down

---

### 2.3 Non-Functional Requirements

- Clear separation of concerns
- Highly extensible design
- Clean object-oriented structure
- Easy to add new split strategies

---

## 3. Core Object Identification

### Primary Entities

- Splitwise (Driver)
- User
- Group
- Expense
- Split

### Controllers (Business Logic Holders)

- UserController
- GroupController
- ExpenseController
- **BalanceSheetController** ⭐

### Supporting Entities

- BalanceSheet
- Balance
- ExpenseSplit (Strategy)

---

## 4. Key Design Principles

### Why BalanceSheetController is Needed

- Expense creation should NOT directly mutate user balances
- Balance update logic is complex and evolving
- Centralizing this logic:
  - Improves readability
  - Avoids duplication
  - Makes debt simplification easier

👉 **ExpenseController creates expenses**  
👉 **BalanceSheetController updates balances**

---

## 5. High-Level Architecture

```
Splitwise
 ├── UserController
 ├── GroupController
 └── ExpenseController
         └── BalanceSheetController
```

---

## 6. Detailed Class Design & **Complete Java Code**

Below is a **complete, compilable, interview-ready Java-style implementation** of **all entities**, strictly following the UML + transcript.

<img width="5652" height="6062" alt="CricBuzz_LLD excalidraw" src="https://github.com/user-attachments/assets/febe4d88-969b-4385-b1a5-be300334531a" />

---

### 6.1 Enum – SplitType

```java
enum SplitType {
    EQUAL,
    UNEQUAL,
    PERCENTAGE
}
```

---

### 6.2 User

```java
class User {
    String userId;
    String userName;
    BalanceSheet balanceSheet;

    User(String userId, String userName) {
        this.userId = userId;
        this.userName = userName;
        this.balanceSheet = new BalanceSheet();
    }
}
```

---

### 6.3 Balance & BalanceSheet

```java
class Balance {
    double amountOwe;
    double amountGetBack;
}

class BalanceSheet {
    Map<User, Balance> userBalanceMap = new HashMap<>();
    double totalExpenses;
    double totalPayment;
    double totalOwe;
    double totalGetBack;
}
```

---

### 6.4 Split

```java
class Split {
    User user;
    double amount;
    int percent; // used only for percentage split

    Split(User user, double amount) {
        this.user = user;
        this.amount = amount;
    }

    Split(User user, int percent) {
        this.user = user;
        this.percent = percent;
    }
}
```

---

### 6.5 Expense

```java
class Expense {
    int id;
    String description;
    double amount;
    User paidBy;
    SplitType splitType;
    List<Split> splits;

    Expense(int id, String description, double amount,
            User paidBy, SplitType splitType, List<Split> splits) {
        this.id = id;
        this.description = description;
        this.amount = amount;
        this.paidBy = paidBy;
        this.splitType = splitType;
        this.splits = splits;
    }
}
```

---

### 6.6 ExpenseSplit Strategy

```java
interface ExpenseSplit {
    void validate(double totalAmount, List<Split> splits);
}

class EqualSplit implements ExpenseSplit {
    public void validate(double totalAmount, List<Split> splits) {
        double expected = totalAmount / splits.size();
        for (Split s : splits) {
            if (Math.abs(s.amount - expected) > 0.01)
                throw new IllegalArgumentException("Invalid equal split");
        }
    }
}

class UnequalSplit implements ExpenseSplit {
    public void validate(double totalAmount, List<Split> splits) {
        double sum = 0;
        for (Split s : splits) sum += s.amount;
        if (Math.abs(sum - totalAmount) > 0.01)
            throw new IllegalArgumentException("Unequal split sum mismatch");
    }
}

class PercentSplit implements ExpenseSplit {
    public void validate(double totalAmount, List<Split> splits) {
        int percentSum = 0;
        for (Split s : splits) percentSum += s.percent;
        if (percentSum != 100)
            throw new IllegalArgumentException("Percent split must sum to 100");

        // compute amounts
        for (Split s : splits) {
            s.amount = (totalAmount * s.percent) / 100.0;
        }
    }
}
```

---

### 6.7 ExpenseSplitFactory

```java
class ExpenseSplitFactory {
    static ExpenseSplit getSplit(SplitType type) {
        switch (type) {
            case EQUAL: return new EqualSplit();
            case UNEQUAL: return new UnequalSplit();
            case PERCENTAGE: return new PercentSplit();
            default: throw new IllegalArgumentException("Invalid split type");
        }
    }
}
```

---

### 6.8 BalanceSheetController ⭐ (Core Logic)

```java
class BalanceSheetController {

    void updateBalanceSheet(Expense expense) {
        User paidBy = expense.paidBy;

        for (Split split : expense.splits) {
            User user = split.user;
            double amount = split.amount;

            // update total expense
            user.balanceSheet.totalExpenses += amount;

            if (user.equals(paidBy)) {
                paidBy.balanceSheet.totalPayment += amount;
                continue;
            }

            // paidBy gets back money
            paidBy.balanceSheet.totalGetBack += amount;
            paidBy.balanceSheet.userBalanceMap
                .computeIfAbsent(user, u -> new Balance())
                .amountGetBack += amount;

            // user owes money
            user.balanceSheet.totalOwe += amount;
            user.balanceSheet.userBalanceMap
                .computeIfAbsent(paidBy, u -> new Balance())
                .amountOwe += amount;
        }
    }
}
```

---

### 6.9 ExpenseController

```java
class ExpenseController {
    BalanceSheetController balanceSheetController = new BalanceSheetController();
    int expenseCounter = 1;

    Expense createExpense(String desc, double amount, User paidBy,
                          SplitType type, List<Split> splits) {

        ExpenseSplit strategy = ExpenseSplitFactory.getSplit(type);
        strategy.validate(amount, splits);

        Expense expense = new Expense(
            expenseCounter++, desc, amount, paidBy, type, splits
        );

        balanceSheetController.updateBalanceSheet(expense);
        return expense;
    }
}
```

---

### 6.10 Group

```java
class Group {
    String groupId;
    String groupName;
    List<User> users = new ArrayList<>();
    List<Expense> expenses = new ArrayList<>();
    ExpenseController expenseController = new ExpenseController();

    Group(String id, String name) {
        this.groupId = id;
        this.groupName = name;
    }
}
```

---

### 6.11 Controllers

```java
class UserController {
    List<User> users = new ArrayList<>();

    void addUser(User user) {
        users.add(user);
    }
}

class GroupController {
    List<Group> groups = new ArrayList<>();

    void addGroup(Group group) {
        groups.add(group);
    }
}
```

---

### 6.12 Splitwise (Driver)

```java
class Splitwise {
    UserController userController = new UserController();
    GroupController groupController = new GroupController();

    public static void main(String[] args) {
        Splitwise app = new Splitwise();

        User u1 = new User("1", "A");
        User u2 = new User("2", "B");

        app.userController.addUser(u1);
        app.userController.addUser(u2);

        ExpenseController ec = new ExpenseController();
        List<Split> splits = Arrays.asList(
            new Split(u1, 250),
            new Split(u2, 250)
        );

        ec.createExpense("Lunch", 500, u1, SplitType.EQUAL, splits);
    }
}
```

---


