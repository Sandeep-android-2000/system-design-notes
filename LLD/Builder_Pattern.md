# Builder Design Pattern in Java

## 🧩 Problem It Solves

When a class has:
- Many parameters (especially optional ones)
- Parameters of the same type (risk of mixing them up)
- Telescoping constructors (constructors with more and more parameters)
- The need for immutability

**Builder Pattern** solves this by separating object construction from its representation.

## 🔧 Real-life Analogy

Imagine ordering a pizza:
- You choose size, crust, cheese, toppings, etc.
- You don’t pass all ingredients in one constructor.
- Use a builder to assemble the pizza step by step.

## 🧵 StringBuilder Example

```java
StringBuilder sb = new StringBuilder();
sb.append("Hello").append(" ").append("World");
System.out.println(sb.toString());
```

Not a classic builder pattern, but follows the fluent interface style.

## 🧰 Manual Builder Pattern in Java

```java
class User {
    private final String name;
    private final int age;
    private final String email;

    private User(UserBuilder builder) {
        this.name = builder.name;
        this.age = builder.age;
        this.email = builder.email;
    }

    public static class UserBuilder {
        private String name;
        private int age;
        private String email;

        public UserBuilder setName(String name) {
            this.name = name;
            return this;
        }

        public UserBuilder setAge(int age) {
            this.age = age;
            return this;
        }

        public UserBuilder setEmail(String email) {
            this.email = email;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }

    public String toString() {
        return "User{name='" + name + "', age=" + age + ", email='" + email + "'}";
    }
}
```

### Usage

```java
User user = new User.UserBuilder()
                .setName("Alice")
                .setAge(28)
                .setEmail("alice@example.com")
                .build();

System.out.println(user);
```

## 🌿 Lombok's `@Builder`

```java
import lombok.Builder;

@Builder
public class Student {
    private String name;
    private int age;
    private String course;
}
```

### Usage

```java
Student s = Student.builder()
                   .name("Bob")
                   .age(22)
                   .course("Physics")
                   .build();
```

## 🏷️ Type of Design Pattern

- **Category:** Creational Design Pattern
- **Goal:** Separate construction of a complex object from its representation
- **Used When:**
  - You want to build an object step-by-step
  - The object has many optional parameters
  - You want immutability or readability

## 📘 UML Diagram

![Builder UML Diagram](sandbox:/mnt/data/A_UML_(Unified_Modeling_Language)_class_diagram_in.png)

