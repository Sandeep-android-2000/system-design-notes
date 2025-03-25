# Snake and Ladder Low-Level Design (LLD)

## Introduction
This document provides a low-level design (LLD) for the Snake and Ladder game in Java. The design ensures modularity, scalability, and easy extendability.

## Requirements Clarification
1. **Number of Dice**: 1 (but should be scalable).
2. **Number of Snakes and Ladders**: Should be dynamically configurable.
3. **Winning Condition**: The game ends as soon as one player reaches the last cell.

## Object Identification
- **Dice**: Handles rolling functionality.
- **Jump (Snake/Ladder)**: Represents a movement jump in the board.
- **Board**: Consists of cells and manages game logic.
- **Cell**: Represents a position on the board, potentially with a jump.
- **Player**: Tracks the player's position.
- **Game**: Manages the game loop, player turns, and declares a winner.

## Relationships
- The **game** has a **board**, a **dice**, a **winner**, and a list of **players**.
- A **jump** denotes either a **snake** or a **ladder** and is associated with a **cell**.
- The **board** consists of multiple **cells**.

## Class Implementations

### Dice
```java
import java.util.*;
class Dice {
    private int sides;
    private Random random;
    
    public Dice(int sides) {
        this.sides = sides;
        this.random = new Random();
    }
    
    public int roll() {
        return random.nextInt(sides) + 1;
    }
}
```

### Jump (Snake or Ladder)
```java
class Jump {
    private int start;
    private int end;
    
    public Jump(int start, int end) {
        this.start = start;
        this.end = end;
    }
    
    public int getStart() {
        return start;
    }
    
    public int getEnd() {
        return end;
    }
}
```

### Cell
```java
class Cell {
    private int position;
    private Jump jump;
    
    public Cell(int position) {
        this.position = position;
    }
    
    public void setJump(Jump jump) {
        this.jump = jump;
    }
    
    public int getFinalPosition() {
        return (jump != null) ? jump.getEnd() : position;
    }
}
```

### Board
```java
class Board {
    private int size;
    private Cell[] cells;
    
    public Board(int size, List<Jump> jumps) {
        this.size = size;
        cells = new Cell[size + 1];
        for (int i = 1; i <= size; i++) {
            cells[i] = new Cell(i);
        }
        for (Jump jump : jumps) {
            cells[jump.getStart()].setJump(jump);
        }
    }
    
    public int getNewPosition(int currentPosition) {
        return cells[currentPosition].getFinalPosition();
    }
    
    public int getSize() {
        return size;
    }
}
```

### Player
```java
class Player {
    private String name;
    private int position;
    
    public Player(String name) {
        this.name = name;
        this.position = 1;
    }
    
    public String getName() {
        return name;
    }
    
    public int getPosition() {
        return position;
    }
    
    public void setPosition(int position) {
        this.position = position;
    }
}
```

### Game Logic
```java
class SnakeLadderGame {
    private Board board;
    private Dice dice;
    private Queue<Player> players;
    private Player winner;
    
    public SnakeLadderGame(int boardSize, int diceSides, List<Player> players, List<Jump> jumps) {
        this.board = new Board(boardSize, jumps);
        this.dice = new Dice(diceSides);
        this.players = new LinkedList<>(players);
        this.winner = null;
    }
    
    public void play() {
        while (winner == null) {
            Player currentPlayer = players.poll();
            int diceRoll = dice.roll();
            int newPosition = currentPlayer.getPosition() + diceRoll;
            
            if (newPosition <= board.getSize()) {
                newPosition = board.getNewPosition(newPosition);
                currentPlayer.setPosition(newPosition);
                System.out.println(currentPlayer.getName() + " rolled " + diceRoll + " and moved to " + newPosition);
                
                if (newPosition == board.getSize()) {
                    winner = currentPlayer;
                }
            }
            
            players.offer(currentPlayer);
        }
        System.out.println("Winner is " + winner.getName() + "!");
    }
}
```

### Main Method
```java
public class SnakeLadder {
    public static void main(String[] args) {
        List<Player> players = Arrays.asList(new Player("Alice"), new Player("Bob"));
        List<Jump> jumps = Arrays.asList(
            new Jump(3, 22),  // Ladder
            new Jump(5, 8),   // Ladder
            new Jump(20, 14), // Snake
            new Jump(27, 1)   // Snake
        );
        
        SnakeLadderGame game = new SnakeLadderGame(30, 6, players, jumps);
        game.play();
    }
}
```

## Summary
- The design follows **OOP principles** with a clear separation of concerns.
- **Dice** rolls the number.
- **Board** handles movement logic.
- **Cells** track positions and jumps.
- **Players** take turns rolling dice.
- **Game** orchestrates the logic and declares a winner.

This implementation ensures flexibility and scalability for future enhancements.

---

Let me know if any modifications are needed! 🚀
