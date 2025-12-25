# Movie Ticket Booking System — Low Level Design (LLD)


## 1. Introduction

This document presents a **clean, interview-ready Low Level Design (LLD)** for a **Movie Ticket Booking System**

The design follows a **top-down, city-driven approach**, exactly how real-world movie ticket platforms (like BookMyShow) work.

---

## 2. Rough Flow (High-Level Understanding)

<img width="1181" height="403" alt="image" src="https://github.com/user-attachments/assets/1431b8c9-adc9-4bfa-8d46-501a40b9c93c" />


This flow drives **all object identification and relationships**.

---

## 3. Functional Requirements

1. User must select a **City** before browsing movies
2. System should display **movies available in the selected city**
3. For a movie, system should show **theaters running that movie**
4. For a theater, system should show **available shows**
5. For a show, system should show **seat availability**
6. User should be able to **book seats**
7. Payment must be completed to confirm booking

---

## 4. Non-Functional Requirements

1. **No double booking of seats**
2. High concurrency handling
3. Temporary seat lock during payment
4. Automatic seat release on failure/timeout
5. Scalable and performant under heavy traffic

---

## 5. Entities Involved

1. User
2. City
3. Movie
4. Theater
5. Screen (Hall)
6. Seat
7. Show
8. Booking
9. Payment

---

## 6. UML Diagram (PlantUML)

<img width="766" height="622" alt="image" src="https://github.com/user-attachments/assets/ea0a8ba8-a85d-4382-8f08-d57e8d26a556" />

---

## 7. Relationships Summary (As per UML)

- MovieController **has-a** Movie
- TheaterController **has-a** Theater
- Theater **has-a** Screen
- Theater **has-a** Show
- Screen **has-a** Seat
- Show **has-a** Movie
- Show **has-a** Screen
- Booking **has-a** Show
- Booking **has-a** Seat
- Booking **has-a** Payment

---

## 8. Entity Responsibilities

### Movie
Represents a movie that can be played in multiple cities.

```java
class Movie {
    String id;
    String name;
    int duration;
}
```

---

### MovieController
Maintains city-wise movie mapping.

```java
class MovieController {
    Map<String, List<Movie>> cityVsMovies;
    List<Movie> allMovies;
}
```

---

### Theater
Represents a physical theater.

```java
class Theater {
    String id;
    String address;
    List<Screen> screens;
    List<Show> shows;
}
```

---

### TheaterController
Maintains city-wise theater mapping.

```java
class TheaterController {
    Map<String, List<Theater>> cityVsTheaters;
}
```

---

### Screen (Hall)
Each theater contains multiple screens.

```java
class Screen {
    String id;
    List<Seat> seats;
}
```

---

### Seat & SeatCategory

```java
enum SeatCategory {
    SILVER,
    GOLD,
    PLATINUM
}

class Seat {
    int seatId;
    int rowNo;
    SeatCategory category;
    int price;
}
```

---

### Show
Represents a movie running on a screen at a specific time.

```java
class Show {
    String showId;
    Movie movie;
    Screen screen;
    String startTime;
    List<Integer> bookedSeatIds;
}
```

---

### Booking
Represents a confirmed seat booking.

```java
class Booking {
    Show show;
    List<Seat> bookedSeats;
    Payment payment;
}
```

---

### Payment

```java
class Payment {
    int id;
}
```

---



---

## 8. Driver Class (End-to-End Flow)

The driver simulates the complete booking journey.

```java
class BookMyShow {

    MovieController movieController = new MovieController();
    TheaterController theaterController = new TheaterController();

    public static void main(String[] args) {
        BookMyShow app = new BookMyShow();
        app.initialize();
        app.runFlow();
    }

    void initialize() {
        Movie bahubali = new Movie("M1", "Baahubali", 170);
        movieController.cityVsMovies.put("Bangalore", List.of(bahubali));

        Seat seat30 = new Seat(30, 3, SeatCategory.SILVER, 200);
        Screen screen = new Screen("S1", List.of(seat30));

        Show show = new Show("SH1", bahubali, screen, "4:00 PM", new ArrayList<>());
        Theater theater = new Theater("T1", "Bangalore", List.of(screen), List.of(show));

        theaterController.cityVsTheaters.put("Bangalore", List.of(theater));
    }

    void runFlow() {
        Show show = theaterController.cityVsTheaters.get("Bangalore").get(0).getShows().get(0);

        int seatId = 30;
        if (!show.bookedSeatIds.contains(seatId)) {
            show.bookedSeatIds.add(seatId);
            Booking booking = new Booking(show, List.of(show.screen.seats.get(0)), new Payment(1));
            System.out.println("Booking Confirmed");
        } else {
            System.out.println("Seat already booked");
        }
    }
}
```

---

## 9. Concurrency Handling (Interview Critical)

### Problem
Two users attempt to book the **same seat at the same time**.

### Solution: Optimistic Locking

- Multiple users can **read seat data concurrently**
- At update time, seat **version is checked**
- Only one update succeeds

```java
synchronized (seat) {
    if (version == dbVersion) {
        bookSeat();
        version++;
    } else {
        throw new SeatAlreadyBookedException();
    }
}
```

---

## 10. Temporary Seat Hold (Redis TTL)

- Seat locked for **10–15 minutes** during payment
- Redis key auto-expires

```
SET show:seat userId NX EX 900
```

---



---

## 11. Common Interview Follow-Up Questions

1. Why optimistic locking over pessimistic?
2. How will you scale this system?
3. What happens if payment fails?
4. How do you avoid seat starvation?

---

