1) Compile the two files below and run the main method in the SimpleThreadExample

```java
$ javac *.java
$ java SimpleThreadExample
```
```java
/* SimpleThreadExample.java */
public class SimpleThreadExample {

    public static void main(String[] args) {
    
        
        Messages msg = new Messages();
        Thread morningThread = new GreetingsThread(msg, 0);
        Thread afternoonThread = new GreetingsThread(msg, 1);

        morningThread.start();
        afternoonThread.start();
        //MARK
        
    }
}
```
```java
/* Messages.java */
public class Messages {
    public String morning;
    public String afternoon;
    public String evening;
    public String night;

    public Messages() {
      morning = "Good morning!";
      afternoon = "Good afternnon!";
      evening = "Good evening!";
      night = "Good night!";
    }

    public String getMessage(int choice) {
      switch(choice) {
         case 0:
           return this.morning;
         case 1:
           return this.afternoon;
         case 3:
           return this.evening;
         case 4:
           return this.night;
         default:
           return "";        
      }
    }
}
```
```java
/* GreetingsThread.java */
public class GreetingsThread extends Thread {

    private Messages msg;
    private int choice;
    public GreetingsThread(Messages msg, int choice) {
        this.msg = msg;
        this.choice = choice;
    }

    public void run() {
        System.out.println("Started the " + msg.getMessage(choice) + " thread...");
            
        //print 10 times
        for (int i = 0; i < 10; i++) {
            System.out.println(msg.getMessage(choice));
        }

        System.out.println("Exiting the " + msg.getMessage(choice) + " thread...");
    }
}
```
Then:

Run the program a few times. Describe the output of this program. Is it consistent?
Draw a memory diagram of the program at MARK

It is **not** consistent! The order seems to be random.

2) Now let’s modify GreetingsThread to add a Thread.sleep() in the run() method. Recompile the GreatingThread class as below.

Run the program multiple times. Does the output change in any way? Does one thread always finish first, or does the order change?

The main change that I can percieve is the slow crawl of certain processes finishing their thread, the order seems to be more split rather than one finishing completely in front of the other.

/* GreetingsThread.java */
public class GreetingsThread extends Thread {
    private Messages msg;
    private int choice;

    public GreetingsThread(Messages msg, int choice) {
        this.msg = msg;
        this.choice = choice;
    }

    public void run() {
        System.out.println("Started the " + msg.getMessage(choice) + " thread...");

        for (int i = 0; i < 10; i++) {
            System.out.println(msg.getMessage(choice));

            try {
                // Sleep for 1 second (1000 milliseconds)
                Thread.sleep(1000); 
            } catch (InterruptedException e) {
                System.out.println("Interrupted while sleeping...");
            }
        }

        System.out.println("Exiting the " + msg.getMessage(choice) + " thread...");
    }
}

3) Now let’s add a System.out.println() at the end of the main method. Recompile the program with this addition, continuing from above. Explain how it is possible that the main method is complete but the program is still producing output.

```java
/* SimpleThreadExample.java */
public class SimpleThreadExample {
    public static void main(String[] args) {
            
        Messages msg = new Messages();
        Thread morningThread = new GreetingsThread(msg, 0);
        Thread afternoonThread = new GreetingsThread(msg, 1);

        morningThread.start();
        afternoonThread.start();

        System.out.println("Main method exiting...");
    }
}
```

It's possible because the parent thread is still taking place and finishing it's processes, there is no interference from the children towards the parents thread.

4) Finally let’s add a thread.join() to join the morningThread for 5 seconds before starting the afternoonThread. Recompile and rerun. Then describe the output of this program. Explain how attempting to join the first thread for 5 seconds affects the output of this program.

```java
/* SimpleThreadExample.java */
public class SimpleThreadExample {
    public static void main(String[] args) {
        Messages msg = new Messages();
        Thread morningThread = new GreetingsThread(msg, 0);
        Thread afternoonThread = new GreetingsThread(msg, 1);

        morningThread.start();

        try {
            System.out.println("Joining the morning thread for 5 seconds...");
            morningThread.join(5000);
        } catch (InterruptedException e) {
            System.out.println("Interrupted while joining a thread...");
        }

        afternoonThread.start();

        System.out.println("Main method exiting...");
    }
}
```

Only one thread runs at a time now, and we can see the direct difference between their outputs, as the print statements are completely seperated by the exiting message.

5) What does join() do compared to join(10)?
Join() is used for waiting until the thread being specified is **fully** complete, whereas Join(int) is used for waiting for the specified milliseconds amount of time.

6) What is the difference between isAlive() and join()? (note, no arguments to join.)
   
The isAlive() method is used to check if a thread is still currently running, whereas Join is just waiting for some time.

7) Compile the two files below and run the main method in the AnimalFootRace. Then describe the output of this program. Explain why the threads finish in the order that they do.

```java
$ javac *.java
$ java AnimalFootRace
```
```java
/* AnimalFootRace.java */
public class AnimalFootRace {
    public static void main(String[] args) {
        Thread tortoiseThread = new AnimalRacerThread("Tortoise", 5);
        Thread hareThread = new AnimalRacerThread("Hare", 20);
        Thread cheetahThread = new AnimalRacerThread("Cheetah", 50);

        System.out.println("On your marks, get set, go!");
        tortoiseThread.start();
        hareThread.start();
        cheetahThread.start();
    }
}
```
```java
/* AnimalRacerThread.java */
public class AnimalRacerThread extends Thread {
    public static final int NUM_LAPS = 8;
    private String animalName;
    private int animalSpeed;

    public AnimalRacerThread(String animalName, int animalSpeed) {
        this.animalName = animalName;
        this.animalSpeed = animalSpeed;
    }

    @Override
    public void run() {
        for (int i = 1; i <= NUM_LAPS; i++) {
            System.out.println(animalName + " lap " + i);
            try {
                Thread.sleep(1000 / animalSpeed);
            } catch (InterruptedException e) {
                System.out.println("Interrupted while sleeping...");
            }
        }
        System.out.println(animalName + "Finished!");
    }
}
```
The threads finish the way they do based on the speed of the animals, as they determine the time it takes for the threads to finish.

8) Modify the AnimialFootRace main method. Add additional code to give the hare a head start, without modifying the animal speeds. Then compile the two files below and run the main method in the AnimalFootRace. Provide the modified code below. Then describe the output of this program. Explain why your modification worked or did not work.

```java
/* AnimalRacerThread.java */
public class AnimalRacerThread extends Thread {
    public static final int NUM_LAPS = 8;
    private String animalName;
    private int animalSpeed;

    public AnimalRacerThread(String animalName, int animalSpeed) {
        this.animalName = animalName;
        this.animalSpeed = animalSpeed;
    }

    @Override
    public void run() {

        try
        {
            if (this.animalName == "Cheetah")
            {
                Thread.sleep(5000);
            }
        }
        catch (InterruptedException e)
        {
            System.out.println("Interrupted while sleeping...");
        }

        for (int i = 1; i <= NUM_LAPS; i++) {
            System.out.println(animalName + " lap " + i);
            try {
                Thread.sleep(1000 / animalSpeed);
            } catch (InterruptedException e) {
                System.out.println("Interrupted while sleeping...");
            }
        }
        System.out.println(animalName + "Finished!");
    }
}
```

I just added another try catch for specifically the Cheetah, so that the Hare would win, I did not need to change the sleep time of tortoise as it was slower. The new try catch checks for the name and activates if it equals "Cheetah"

9) Modify the AnimialFootRace main method. Add a new animal thread to the foot race. Then compile the two files below and run the main method in the AnimalFootRace. Provide the modified code below. Then describe the output of this program.

```java
/* AnimalFootRace.java */
public class AnimalFootRace {
    public static void main(String[] args) {
        Thread tortoiseThread = new AnimalRacerThread("Tortoise", 5);
        Thread hareThread = new AnimalRacerThread("Hare", 20);
        Thread cheetahThread = new AnimalRacerThread("Cheetah", 50);
        Thread humanThread = new AnimalRacerThread("Human", 15);

        System.out.println("On your marks, get set, go!");
        tortoiseThread.start();
        hareThread.start();
        cheetahThread.start();
        humanThread.start();
    }
}
```

This is the output: 
***On your marks, get set, go!
Human lap 1
Tortoise lap 1
Hare lap 1
Hare lap 2
Human lap 2
Hare lap 3
Human lap 3
Hare lap 4
Tortoise lap 2
Hare lap 5
Human lap 4
Hare lap 6
Human lap 5
Hare lap 7
Human lap 6
Hare lap 8
Tortoise lap 3
Human lap 7
HareFinished!
Human lap 8
HumanFinished!
Tortoise lap 4
Tortoise lap 5
Tortoise lap 6
Tortoise lap 7
Tortoise lap 8
TortoiseFinished!
Cheetah lap 1
Cheetah lap 2
Cheetah lap 3
Cheetah lap 4
Cheetah lap 5
Cheetah lap 6
Cheetah lap 7
Cheetah lap 8
CheetahFinished!***

10) The Singleton class below implements the Singleton pattern. The singleton pattern is a software engineering design pattern that restricts the instantiating of a class to a single instance. Review the Singleton class code below. Is it possible to create more than one instance of the Singleton class if two threads attempt to call the getInstance() method at the same time?

```java
// Java program to create a Singleton class.
public class Singleton {
    // This is a private member variable so that the singletonInstance
    // can only be accessed through the getInstance() method.
    private static Singleton singletonInstance;

    // Private constructor forces the class to be instantiated 
    // via the getInstance method.
    private Singleton() {
        // private constructor.
    }

    // Method to get an instance of this class.
    public static Singleton getInstance() {
        // If this singleton instance is null, 
        // then construct a new instance.
        // Otherwise return the existing instance.
        if (singletonInstance == null) {
            singletonInstance = new Singleton();
        }

        return singletonInstance;
    }
}
```

Well, you would create a new Singleton, but the variable would be the only thing changing, as the information/memory would be lost if the getInstance() is called by multiple threads without the synchronized key in java.

11) Review the modified thread safe Singleton class code below. Is it possible to create more than one instance of the Singleton class if two threads attempt to call the getInstance() method at the same time? How does the synchronized keyword affect the attempts to call the getInstance method from multiple threads at the same time?

```java
// Java program to create thread safe Singleton class.
public class Singleton {
    // This is a private member variable so that the singletonInstance
    // can only be accessed through the getInstance() method.
    private static Singleton singletonInstance;

    // Private constructor forces the class to be instantiated 
    // via the getInstance method.
    private Singleton() {
        // private constructor.
    }

    // Synchronized method to control simultaneous access 
    // to the getInstance method.
    synchronized public static Singleton getInstance() {
        // If this singleton instance is null, 
        // then construct a new instance.
        // Otherwise return the existing instance.
        if (singletonInstance == null) {
            singletonInstance = new Singleton();
        }

        return singletonInstance;
    }
}
```
It does not allow the calling of the method from multiple threads, as the synchronized key should be able to handle the memory issues between threads.

12) Review the ThreadRunner class code below. Compile and run the ThreadRunner main method. Describe the output. Does the execution halt? Does SimpleThreadTwo finish running? If not, why does SimpleThreadTwo get stuck in the while loop (hint: recall the compiler optimization example)?

```java
$ javac ThreadRunner.java
$ java ThreadRunner
```
```java
/* ThreadRunner.java */
public class ThreadRunner {

    private static boolean statusFlag = false;

    private static class SimpleThreadOne extends Thread {
        public void run() {
            for (int i = 1; i <= 2000; i++){
                System.out.println("Simple thread one counter - " + i);
            }
            // Change the status flag.
            statusFlag = true;
            System.out.println("Status flag changed to true in simple thread one.");
        }
    }

    private static class SimpleThreadTwo extends Thread {
        public void run() {
            int waitCounter = 1;
            while (!statusFlag) {
                waitCounter++;
            }
            System.out.println("Start simple thread two processing " + waitCounter);
        }
    }

    public static void main(String[] args) {
        SimpleThreadOne simpleThreadOne = new SimpleThreadOne();
        simpleThreadOne.start();
        SimpleThreadTwo simpleThreadTwo = new SimpleThreadTwo();
        simpleThreadTwo.start();
    }
}
```

The output prints two thousand lines of "Simple thread one counter - *line number*" before it reaches an infinite loop with the message "Status flag changed to true in simple thread one". This means that the program is being stuck on the while loop within **SimpleThreadTwo** class. This is due to the fact that the thread is unable to read the change in the statusFlag boolean.

13) Review the modified ThreadRunner class code below. Compile and run the ThreadRunner main method. Describe the output. Does the execution halt? Does SimpleThreadTwo finish running? If it does finish running, why does adding the volatile keyword before the boolean statusFlag change the behavior of the code compared to the previous question?

```java
$ javac ThreadRunner.java
$ java ThreadRunner
```
```java
/* ThreadRunner.java */
public class ThreadRunner {

    private static volatile boolean statusFlag = false;

    private static class SimpleThreadOne extends Thread {
        public void run() {
            for (int i = 1; i <= 2000; i++){
                System.out.println("Simple thread one counter - " + i);
            }
            // Change the status flag.
            statusFlag = true;
            System.out.println("Status flag changed to true in simple thread one.");
        }
    }

    private static class SimpleThreadTwo extends Thread {
        public void run() {
            int waitCounter = 1;
            while (!statusFlag) {
                waitCounter++;
            }
            System.out.println("Start simple thread two processing " + waitCounter);
        }
    }

    public static void main(String[] args) {
        SimpleThreadOne simpleThreadOne = new SimpleThreadOne();
        simpleThreadOne.start();
        SimpleThreadTwo simpleThreadTwo = new SimpleThreadTwo();
        simpleThreadTwo.start();
    }
}
```

This is the output: 
***Simple thread one counter - 1 ... Simple thread one counter - 2000
Status flag changed to true in simple thread one.
Start simple thread two processing 765555350***

The file and execution are complete due to the volatile keyword on the statusFlag boolean, as the volatile keyword tells the computer that the boolean should be read from the actual memory instead of the cpu cache, as the cpu cache does not show the actual value of the boolean.

14) A second price auction is an auction where the highest bidder only has to pay whatever the second highest bid was. For example if person A bids $1 and person B bids $2, then person B wins the auction, but only pays $1. Below are two classes that reflect this, SecondPriceAuction and Bidder.

Review the code below. What might happen if two threads call the makeBid method at the same time? How would you modify the code to protect the auction outcome?

```java
public class SecondPriceAuction {
    private int currentHighestBid = 0;
    private int secondHighestBid = 0;

    public int getSecondHighestBid() {
        return secondHighestBid;
    }

    public void makeBid(int amount) {
        if (amount > currentHighestBid) {
            secondHighestBid = currentHighestBid;
            currentHighestBid = amount;
        }
        else if(amount > secondHighestBid) {
            secondHighestBid = amount;
        }
    }
}
```
```java
import java.util.Random;

public class Bidder extends Thread {
    private SecondPriceAuction secondPriceAuction;
    private int maxBid;

    public Bidder(SecondPriceAuction secondPriceAuction, int maxBet) {
        this.secondPriceAuction = secondPriceAuction;
        this.maxBid = maxBet;
    }

    public void run() {
        try {
            Thread.sleep((new Random()).nextInt(100));
        } catch (InterruptedException e) {
            System.out.println("Interrupted while sleeping...");
        }

        secondPriceAuction.makeBid(maxBid);
    }
}
```
```java
public class AuctionRunner {
    public static void main(final String args[]) {
        SecondPriceAuction secondPriceAuction = new SecondPriceAuction();
        Bidder[] bidders = new Bidder[3];

        // Create new bidders.
        for (int i = 0; i < bidders.length; i++) {
            bidders[i] = new Bidder(secondPriceAuction, i + 1);
        }

        // Start bidding.
        for (int i = 0; i < bidders.length; i++) {
            bidders[i].start();
        }

        // Ensures all threads have finished before we print out the price
        for (Bidder bidder : bidders) {
            try {
                bidder.join();
            } catch (InterruptedException e) {
                System.out.println("Interrupted while joining a thread...");
            }
        }

        System.out.println("Final Price: $" + secondPriceAuction.getSecondHighestBid());
    }
}
```

If both threads call the makeBid at the same time, then the values of current amount highest bid, may be incorrect and lead to an incorrect and early ending auction. However, by using the synchronizer keyword operator may fix this issue, as both threads cannot access the method at the same time as one another.
