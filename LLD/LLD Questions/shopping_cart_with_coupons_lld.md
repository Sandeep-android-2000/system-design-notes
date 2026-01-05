
# Shopping Cart with Coupons – Low Level Design (LLD)

## Problem Statement

Design a **Shopping Cart system** where:
- A cart contains multiple products.
- Multiple coupons can be applied **sequentially** to each product.
- Coupons may have **different conditions** (percentage off, type-based, nth-item, etc.).
- A product can be eligible for **multiple coupons**.
- The final payable amount is calculated after applying all valid coupons.

The design should:
- Be **extensible** (easy to add new coupon types).
- Avoid modifying existing product code when new coupons are introduced.
- Focus **only on cart, product, and coupon logic** (ignore payment, inventory, invoice, etc.).

---

## Key Objects in the System

- **ShoppingCart**
- **Product**
- **Coupon**
- **CouponDecorator**

---

## Design Pattern Used

### ✅ Decorator Pattern

**Why Decorator?**
- Coupons dynamically modify product price.
- Multiple coupons can be layered one after another.
- Product remains unchanged.
- New coupons can be added without changing existing code.

Each coupon **wraps** a product and applies additional logic on top of it.

---

## Class Design Overview

```
Product (abstract)
│
├── Item (Concrete Product)
│
└── CouponDecorator (abstract)
    ├── PercentageCouponDecorator
    └── TypeCouponDecorator
```

---

## Product Design

### Abstract Product

```java
abstract class Product {
    protected String name;
    protected double originalPrice;
    protected ProductType type;

    public Product(String name, double originalPrice, ProductType type) {
        this.name = name;
        this.originalPrice = originalPrice;
        this.type = type;
    }

    public abstract double getPrice();
}
```

### Concrete Product (Item)

```java
class Item extends Product {

    public Item(String name, double price, ProductType type) {
        super(name, price, type);
    }

    @Override
    public double getPrice() {
        return originalPrice;
    }
}
```

---

## Coupon Decorator

### Abstract CouponDecorator

```java
abstract class CouponDecorator extends Product {
    protected Product product;

    public CouponDecorator(Product product) {
        super(product.name, product.originalPrice, product.type);
        this.product = product;
    }
}
```

---

## Coupon Implementations

### 1. Percentage Coupon (Flat Discount)

```java
class PercentageCouponDecorator extends CouponDecorator {
    private double discountPercent;

    public PercentageCouponDecorator(Product product, double discountPercent) {
        super(product);
        this.discountPercent = discountPercent;
    }

    @Override
    public double getPrice() {
        double price = product.getPrice();
        return price - (price * discountPercent / 100);
    }
}
```

### 2. Type-Based Coupon

```java
class TypeCouponDecorator extends CouponDecorator {
    private double discountPercent;
    private static final Set<ProductType> ELIGIBLE_TYPES =
            Set.of(ProductType.FURNITURE, ProductType.DECORATIVE);

    public TypeCouponDecorator(Product product, double discountPercent) {
        super(product);
        this.discountPercent = discountPercent;
    }

    @Override
    public double getPrice() {
        double price = product.getPrice();
        if (ELIGIBLE_TYPES.contains(product.type)) {
            price -= price * discountPercent / 100;
        }
        return price;
    }
}
```

---

## Shopping Cart Design

```java
class ShoppingCart {
    private List<Product> products = new ArrayList<>();

    public void addToCart(Product product) {
        Product discountedProduct =
                new TypeCouponDecorator(
                    new PercentageCouponDecorator(product, 10),
                    5
                );
        products.add(discountedProduct);
    }

    public double getTotalPrice() {
        double total = 0;
        for (Product product : products) {
            total += product.getPrice();
        }
        return total;
    }
}
```

---

## Execution Flow Example

### Step 1: Create Products

```java
Product item1 = new Item("Fan", 1000, ProductType.ELECTRONIC);
Product item2 = new Item("Sofa", 2000, ProductType.FURNITURE);
```

### Step 2: Add to Cart

```java
ShoppingCart cart = new ShoppingCart();
cart.addToCart(item1);
cart.addToCart(item2);
```

### Step 3: Calculate Total

```java
double total = cart.getTotalPrice();
System.out.println(total);
```

---

## How Coupon Chaining Works

For **Item 2 (Furniture, ₹2000)**:

1. Original Price → ₹2000
2. 10% Percentage Coupon → ₹1800
3. 5% Type Coupon → ₹1710

For **Item 1 (Electronic, ₹1000)**:

1. Original Price → ₹1000
2. 10% Percentage Coupon → ₹900
3. Type Coupon not applicable → ₹900

### Final Total = ₹900 + ₹1710 = ₹2610 (approx)

---

## Extending the System

Adding a new coupon:
- Create a new class extending `CouponDecorator`
- Override `getPrice()`
- Plug it into cart logic

No existing code changes required ✔️

---

## Advantages of This Design

- ✔ Open/Closed Principle
- ✔ Clean separation of concerns
- ✔ Supports unlimited coupon combinations
- ✔ Interview-friendly and scalable

---

## Interview Tips

- Clearly explain **why Decorator pattern is used**
- Emphasize **extensibility**
- State that **payment, user, inventory** are intentionally excluded
- Walk interviewer through **price calculation flow**

---

## Summary

This LLD focuses purely on **coupon application logic** using the **Decorator Pattern**, enabling a clean, extensible, and scalable shopping cart design suitable for real-world systems and interviews.
