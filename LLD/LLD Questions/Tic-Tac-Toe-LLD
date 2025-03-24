# **Tic-Tac-Toe Game (LLD) - Java Implementation**
A **console-based** Tic-Tac-Toe game using **Object-Oriented Design (OOD)** principles in Java.

## **1️⃣ Features**
✔ Supports a **3x3 Tic-Tac-Toe board**  
✔ **Two players** take turns placing their pieces (`X` and `O`)  
✔ Checks for **winning conditions** (rows, columns, diagonals)  
✔ Declares a **winner** or **tie** if the board is full  
✔ Uses **Encapsulation, Inheritance, and Polymorphism**  

---

## **2️⃣ Class Diagram**
```
                ┌──────────────────┐
                │    TicTacToeGame  │
                ├──────────────────┤
                │ - players: Queue │
                │ - gameBoard: Board │
                │ + initializeGame() │
                │ + startGame() │
                │ + isThereWinner() │
                └──────────────────┘
                          ▲
                          │
                ┌──────────────────┐
                │      Player       │
                ├──────────────────┤
                │ - name: String   │
                │ - playingPiece   │
                │ + getName()      │
                │ + getPiece()     │
                └──────────────────┘
                          ▲
                          │
                ┌──────────────────┐
                │   PlayingPiece    │
                ├──────────────────┤
                │ - pieceType       │
                │ + getPieceType()  │
                └──────────────────┘
                          ▲
                          │
                ┌──────────────────┐
                │    PieceType      │
                ├──────────────────┤
                │  X, O            │
                └──────────────────┘

                ┌──────────────────┐
                │      Board        │
                ├──────────────────┤
                │ - size: int      │
                │ - board[][]      │
                │ + addPiece()     │
                │ + getFreeCells() │
                │ + printBoard()   │
                └──────────────────┘
```

---

## **3️⃣ Class Breakdown**

### **🔹 `TicTacToeGame` (Main Game Logic)**
Handles **game initialization**, **turn-based play**, and **winning conditions**.

```java
public class TicTacToeGame {
    private Deque<Player> players;
    private Board gameBoard;

    public void initializeGame() {
        players = new LinkedList<>();
        players.add(new Player("Player1", new PlayingPiece(PieceType.X)));
        players.add(new Player("Player2", new PlayingPiece(PieceType.O)));
        gameBoard = new Board(3);
    }

    public String startGame() {
        while (true) {
            Player currentPlayer = players.removeFirst();
            gameBoard.printBoard();
            
            List<Pair<Integer, Integer>> freeCells = gameBoard.getFreeCells();
            if (freeCells.isEmpty()) return "It's a tie!";

            System.out.print(currentPlayer.getName() + " Enter row,column: ");
            Scanner scanner = new Scanner(System.in);
            String[] input = scanner.nextLine().split(",");
            int row = Integer.parseInt(input[0]);
            int col = Integer.parseInt(input[1]);

            if (!gameBoard.addPiece(row, col, currentPlayer.getPlayingPiece())) {
                System.out.println("Invalid move, try again!");
                players.addFirst(currentPlayer);
                continue;
            }
            players.addLast(currentPlayer);

            if (isThereWinner(row, col, currentPlayer.getPlayingPiece().getPieceType())) {
                return currentPlayer.getName() + " wins!";
            }
        }
    }

    private boolean isThereWinner(int row, int col, PieceType pieceType) {
        return gameBoard.checkRow(row, pieceType) ||
               gameBoard.checkColumn(col, pieceType) ||
               gameBoard.checkDiagonals(pieceType);
    }
}
```

---

### **🔹 `Player` (Player Representation)**
Each player has a **name** and a **playing piece** (`X` or `O`).

```java
public class Player {
    private String name;
    private PlayingPiece playingPiece;

    public Player(String name, PlayingPiece playingPiece) {
        this.name = name;
        this.playingPiece = playingPiece;
    }

    public String getName() { return name; }
    public PlayingPiece getPlayingPiece() { return playingPiece; }
}
```

---

### **🔹 `PlayingPiece` (Represents X or O)**
Encapsulates a **piece type** (`X` or `O`).

```java
public class PlayingPiece {
    private final PieceType pieceType;

    public PlayingPiece(PieceType pieceType) {
        this.pieceType = pieceType;
    }

    public PieceType getPieceType() { return pieceType; }
}
```

---

### **🔹 `PieceType` (Enum for X and O)**
Defines allowed **piece types**.

```java
public enum PieceType {
    X, O;
}
```

---

### **🔹 `Board` (Game Board)**
Manages the **grid**, **piece placement**, and **win conditions**.

```java
public class Board {
    private int size;
    private PlayingPiece[][] board;

    public Board(int size) {
        this.size = size;
        board = new PlayingPiece[size][size];
    }

    public boolean addPiece(int row, int col, PlayingPiece piece) {
        if (board[row][col] != null) return false;
        board[row][col] = piece;
        return true;
    }

    public void printBoard() {
        for (PlayingPiece[] row : board) {
            for (PlayingPiece cell : row)
                System.out.print(cell == null ? "   | " : cell.getPieceType() + " | ");
            System.out.println();
        }
    }
}
```

---

## **4️⃣ How to Run**
### **Compile & Run**
```sh
javac LLDTicTacToe/*.java
java LLDTicTacToe.Main
```

### **Input Example**
```
Player1 Enter row,column: 0,0
Player2 Enter row,column: 0,1
...
```

---

## **5️⃣ Summary**
- **Encapsulated Classes** for `Player`, `PieceType`, and `Board`.  
- **Validations** prevent invalid moves.  
- **Clean OOP Structure** using **Encapsulation & Polymorphism**.  
- **Easily Extendable** for different board sizes.

Would you like to add **AI or Multiplayer mode?** 🚀
