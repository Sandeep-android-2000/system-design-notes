# Low Level Design (LLD) – Cricbuzz / Cricinfo
---

## 1. LLD Problem Statement

Design a **Cricbuzz / Cricinfo–like live scoring system** that:

- Displays multiple ongoing cricket matches
- Updates score **ball-by-ball**
- Maintains accurate **batting and bowling scorecards**
- Supports different match formats (T20, ODI)
- Mimics **real cricket flow**: toss → innings → overs → balls

The system must be **object-oriented, extensible, and interview-ready**.

---

## 2. Requirement Gathering

### Functional Requirements

1. A match consists of **two teams**
2. Toss happens before the match
   - Toss winner chooses **bat or bowl**
3. A match has **two innings**
4. Each innings has:
   - One batting team
   - One bowling team
5. Each innings consists of **overs**
6. Each over consists of **balls** (normal, wide, no-ball)
7. Each ball updates:
   - Batting scorecard
   - Bowling scorecard
8. Track striker & non-striker
9. Track bowler rotation with over limits
10. Support formats:
    - T20 → 20 overs, 5 overs per bowler
    - ODI → 50 overs, 10 overs per bowler

### Non-Functional Requirements

- Clean separation of responsibility
- Use of design patterns
- Easy to extend (Test match, Super Over, etc.)

---

## 3. Core Objects (As per Diagram)

### Primary Entities

- Match
- Team
- Player
- Innings
- Over
- Ball

### Supporting Entities

- Person
- BattingScore
- BowlingScore
- BattingController
- BowlingController

### Enums

- PlayerType
- BallType
- RunType

---

## 4. Relationships (Exact Mapping)

### Composition / Ownership

- Match → Team (Team A, Team B)
- Match → Innings[2]
- Team → PlayingXI + Extras
- Team → BattingController
- Team → BowlingController
- Player → Person
- Player → BattingScore
- Player → BowlingScore
- Innings → Overs
- Over → Balls
- Ball → Observers

### Design Patterns

| Pattern | Where Used |
|------|-----------|
| Strategy | MatchType (T20 / ODI) |
| Observer | Ball → Score Updaters |
| Controller | Batting & Bowling Controllers |

---

## 5. UML / Class Diagram Reference

<img width="5652" height="6062" alt="CricBuzz_LLD excalidraw" src="https://github.com/user-attachments/assets/cb7b9d70-9ffa-4873-8c3c-d26296027c62" />

---

## 6. Detailed Class Design & Java Code

---

### 6.1 Enums

```java
enum PlayerType {
    BATSMAN, BOWLER, CAPTAIN, WICKET_KEEPER, ALL_ROUNDER
}

enum BallType {
    NO_BALL, WIDE, NORMAL
}

enum RunType {
    ONE, TWO, THREE, FOUR, SIX, ZERO
}
```

---

### 6.2 Person

```java
class Person {
    String name;
    int age;
}
```

---

### 6.3 Score Entities

```java
class BattingScore {
    int totalRuns;
    int totalBallsPlayed;
    int totalFours;
    int totalSixes;

    double strikeRate() {
        return totalBallsPlayed == 0 ? 0 : (totalRuns * 100.0) / totalBallsPlayed;
    }
}

class BowlingScore {
    int totalOversDelivered;
    int totalRunsGiven;
    int totalWicketsTaken;
    int noBalls;
    int wideBalls;

    double economyRate() {
        return totalOversDelivered == 0 ? 0 : (double) totalRunsGiven / totalOversDelivered;
    }
}
```

---

### 6.4 Player

```java
class Player {
    Person person;
    PlayerType playerType;
    BattingScore battingScore;
    BowlingScore bowlingScore;
}
```

---

### 6.5 BattingController (Team-level)

Tracks:
- Yet-to-bat players
- Current striker
- Current non-striker

```java
class BattingController {
    Queue<Player> playerYetToBat;
    Player striker;
    Player nonStriker;

    void initializeOpeners() {
        striker = playerYetToBat.poll();
        nonStriker = playerYetToBat.poll();
    }

    Player nextBatsman() {
        return playerYetToBat.poll();
    }

    void swapStrike() {
        Player temp = striker;
        striker = nonStriker;
        nonStriker = temp;
    }
}
```

---

### 6.6 BowlingController (Team-level)

Tracks:
- Deque of bowlers
- Bowler → over count
- Current bowler

```java
class BowlingController {
    Deque<Player> bowlers;
    Map<Player, Integer> bowlerVsOverCount;
    Player currentBowler;
    int maxOversAllowed;

    Player chooseNextBowler() {
        while (true) {
            Player bowler = bowlers.pollFirst();
            int overs = bowlerVsOverCount.getOrDefault(bowler, 0);

            if (overs < maxOversAllowed) {
                currentBowler = bowler;
                bowlerVsOverCount.put(bowler, overs + 1);
                bowlers.offerLast(bowler);
                return bowler;
            }
        }
    }
}
```

---

### 6.7 Team

```java
class Team {
    String teamName;
    Queue<Player> playingEleven;
    List<Player> extras;

    BattingController battingController;
    BowlingController bowlingController;
}
```

---

### 6.8 MatchType (Strategy)

```java
interface MatchType {
    int noOfOvers();
    int maxOverPerBowlerAllowed();
}

class T20 implements MatchType {
    public int noOfOvers() { return 20; }
    public int maxOverPerBowlerAllowed() { return 5; }
}

class OneDay implements MatchType {
    public int noOfOvers() { return 50; }
    public int maxOverPerBowlerAllowed() { return 10; }
}
```

---

### 6.9 Match (With Real Toss Logic)

```java
class Match {
    Team teamA;
    Team teamB;
    Date matchDate;
    String venue;
    MatchType matchType;
    Team tossWinner;
    Team battingFirst;
    Team bowlingFirst;
    Innings[] innings = new Innings[2];

    void startMatch() {
        performToss();
        startInnings();
    }

    void performToss() {
        tossWinner = Math.random() < 0.5 ? teamA : teamB;
        boolean chooseBat = Math.random() < 0.5;

        if (chooseBat) {
            battingFirst = tossWinner;
            bowlingFirst = tossWinner == teamA ? teamB : teamA;
        } else {
            bowlingFirst = tossWinner;
            battingFirst = tossWinner == teamA ? teamB : teamA;
        }
    }

    void startInnings() {
        innings[0] = new Innings(battingFirst, bowlingFirst, matchType);
        innings[0].startInnings();

        innings[1] = new Innings(bowlingFirst, battingFirst, matchType);
        innings[1].startInnings();
    }
}
```

---

### 6.10 Innings

```java
class Innings {
    Team battingTeam;
    Team bowlingTeam;
    List<Over> overs;
    MatchType matchType;

    Innings(Team bat, Team bowl, MatchType type) {
        battingTeam = bat;
        bowlingTeam = bowl;
        matchType = type;
        overs = new ArrayList<>();
    }

    void startInnings() {
        battingTeam.battingController.initializeOpeners();

        for (int i = 0; i < matchType.noOfOvers(); i++) {
            Over over = new Over(i + 1, this);
            over.startOver();
            overs.add(over);
        }
    }
}
```

---

### 6.11 Over

```java
class Over {
    int overNo;
    List<Ball> balls = new ArrayList<>();
    Innings innings;

    Over(int no, Innings innings) {
        this.overNo = no;
        this.innings = innings;
    }

    void startOver() {
        Player bowler = innings.bowlingTeam.bowlingController.chooseNextBowler();
        int validBalls = 0;

        while (validBalls < 6) {
            Ball ball = new Ball(innings, bowler);
            ball.startBowling();
            balls.add(ball);

            if (ball.type == BallType.NORMAL) validBalls++;
        }
    }
}
```

---

### 6.12 Observer Pattern (Ball)

```java
interface BallUpdateObserver {
    void update(Ball ball);
}

class Ball {
    BallType type;
    RunType runType;
    Player playedBy;
    Player bowledBy;
    List<BallUpdateObserver> observers = new ArrayList<>();

    Ball(Innings innings, Player bowler) {
        this.bowledBy = bowler;
        this.playedBy = innings.battingTeam.battingController.striker;
    }

    void startBowling() {
        notifyObservers();
    }

    void notifyObservers() {
        for (BallUpdateObserver o : observers) {
            o.update(this);
        }
    }
}
```

---

## 7. Interview Summary (How to Explain)

- **Ball is the event source**
- **Team controls match state**
- **Player is a data entity**
- Observer pattern ensures **decoupled score updates**
- Strategy pattern ensures **format extensibility**
  
