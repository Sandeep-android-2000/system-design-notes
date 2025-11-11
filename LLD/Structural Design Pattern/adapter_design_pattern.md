
# 🔌 Adapter Design Pattern

## 📘 Definition

The **Adapter Pattern** acts as a **bridge between two incompatible interfaces**. It allows classes to work together that couldn’t otherwise because of incompatible interfaces.

---

## 🎯 Purpose

- To **convert the interface** of a class into another interface the client expects.
- Helps to **reuse existing code** without modifying it.

---

## 📦 Real-World Analogy

Think of a **mobile charger** adapter. If you have a U.S. plug but you're in India, you use a plug adapter to connect your charger to the Indian socket. The adapter **makes things compatible**.

---

## 🛠️ Structure (in Java)

Suppose you have a legacy media player that only plays `.mp3` files, but you now want to support `.mp4` and `.vlc` using an adapter.

### 1. Target Interface

```java
public interface MediaPlayer {
    void play(String audioType, String fileName);
}
```

### 2. Adaptee Class (Incompatible class)

```java
public class AdvancedMediaPlayer {
    public void playVlc(String fileName) {
        System.out.println("Playing vlc file: " + fileName);
    }

    public void playMp4(String fileName) {
        System.out.println("Playing mp4 file: " + fileName);
    }
}
```

### 3. Adapter Class

```java
public class MediaAdapter implements MediaPlayer {
    private AdvancedMediaPlayer advancedMediaPlayer = new AdvancedMediaPlayer();

    public void play(String audioType, String fileName) {
        if (audioType.equalsIgnoreCase("vlc")) {
            advancedMediaPlayer.playVlc(fileName);
        } else if (audioType.equalsIgnoreCase("mp4")) {
            advancedMediaPlayer.playMp4(fileName);
        }
    }
}
```

### 4. Client Class

```java
public class AudioPlayer implements MediaPlayer {
    private MediaAdapter mediaAdapter;

    public void play(String audioType, String fileName) {
        if (audioType.equalsIgnoreCase("mp3")) {
            System.out.println("Playing mp3 file: " + fileName);
        } else if (audioType.equalsIgnoreCase("vlc") || audioType.equalsIgnoreCase("mp4")) {
            mediaAdapter = new MediaAdapter();
            mediaAdapter.play(audioType, fileName);
        } else {
            System.out.println("Invalid media type: " + audioType);
        }
    }
}
```

---

## 🧪 Usage Example

```java
public class Main {
    public static void main(String[] args) {
        AudioPlayer player = new AudioPlayer();

        player.play("mp3", "song.mp3");
        player.play("mp4", "movie.mp4");
        player.play("vlc", "clip.vlc");
        player.play("avi", "video.avi");
    }
}
```

---

## 🔧 Types of Adapters

### 1. **Class Adapter (uses inheritance)**
- Adapter extends the adaptee class and implements the target interface.

### 2. **Object Adapter (uses composition)**
- Adapter has a reference to the adaptee object and implements the target interface.

> In Java, object adapters are more common because Java doesn’t support multiple inheritance.

---

## ✅ Advantages

- Makes two incompatible interfaces work together.
- Promotes reusability of legacy code.
- Follows **Open/Closed Principle** (open for extension, closed for modification).

---

## ❌ Disadvantages

- Increases the number of classes.
- Code might get more complex due to extra layers of abstraction.
