# Liskov Substitution Principle (LSP) - Correct Solution

## Problem with the Previous Approach

The previous violation of LSP occurred because `Square` altered the behavior of `Rectangle` by enforcing equal width and height. This breaks the expected behavior of a `Rectangle`.

## ✅ Correct Solution: Use Separate Hierarchies

Instead of having `Square` inherit from `Rectangle`, we can create an abstraction for **Shape** and implement `Rectangle` and `Square` separately.

```java
abstract class Shape {
    abstract int getArea();
}

class Rectangle extends Shape {
    protected int width, height;

    public void setWidth(int width) { this.width = width; }
    public void setHeight(int height) { this.height = height; }
    
    @Override
    public int getArea() {
        return width * height;
    }
}

class Square extends Shape {
    private int side;

    public void setSide(int side) { this.side = side; }

    @Override
    public int getArea() {
        return side * side;
    }
}
```

## ✅ Why is this Correct?

- `Rectangle` and `Square` no longer share an inappropriate inheritance relationship.
- Both classes extend a common abstraction `Shape`, ensuring they can be substituted wherever `Shape` is used.
- This design avoids unexpected behavior when dealing with `Rectangle` and `Square`.

By following this approach, we ensure that our code adheres to the **Liskov Substitution Principle (LSP)** while maintaining correctness and flexibility.
