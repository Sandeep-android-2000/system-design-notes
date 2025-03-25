import java.util.*;

// Class representing the Dice
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

// Class representing a Cell Jump (Snake or Ladder)
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

// Class representing a Cell on the Board
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

// Class representing the Board
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

// Class representing a Player
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

// Class representing the Game
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

// Main class to run the game
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
