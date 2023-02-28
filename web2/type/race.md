# Race condition

The code example below vulnureable to Race condition

```java
public class BankAccount {
    private int balance = 100;

    public int withdraw(int amount) {
        if (balance >= amount) {
            try {
                Thread.sleep(100); // Simulate a long transaction
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            balance -= amount;
            return balance;
        }
        return -1;
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        BankAccount account = new BankAccount();
        Runnable r1 = () -> {
            int amount = 50;
            int result = account.withdraw(amount);
            if (result >= 0) {
                System.out.println("Withdrawal successful: " + amount);
            } else {
                System.out.println("Withdrawal failed: " + amount);
            }
        };
        Runnable r2 = () -> {
            int amount = 50;
            int result = account.withdraw(amount);
            if (result >= 0) {
                System.out.println("Withdrawal successful: " + amount);
            } else {
                System.out.println("Withdrawal failed: " + amount);
            }
        };
        Thread t1 = new Thread(r1);
        Thread t2 = new Thread(r2);
        t1.start();
        t2.start();
    }
}

```

# Why it's vulnerable?

We have a BankAccount class that simulates a bank account, with a withdraw method that subtracts the specified amount from the balance, after a simulated long transaction. We then have a Main class that creates two threads, each of which tries to withdraw $50 from the account. However, because the withdraw method is not synchronized, there is a race condition vulnerability, where both threads may try to withdraw funds at the same time, leading to an inconsistent or incorrect balance.

# How to fix?

Synchronize the withdraw method, so that only one thread can access it at a time

```java
public class BankAccount {
    private int balance = 100;

    public synchronized int withdraw(int amount) {
        if (balance >= amount) {
            try {
                Thread.sleep(100); // Simulate a long transaction
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            balance -= amount;
            return balance;
        }
        return -1;
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        BankAccount account = new BankAccount();
        Runnable r1 = () -> {
            int amount = 50;
            synchronized (account) {
                int result = account.withdraw(amount);
                if (result >= 0) {
                    System.out.println("Withdrawal successful: " + amount);
                } else {
                    System.out.println("Withdrawal failed: " + amount);
                }
            }
        };
        Runnable r2 = () -> {
            int amount = 50;
            synchronized (account) {
                int result = account.withdraw(amount);
                if (result >= 0) {
                    System.out.println("Withdrawal successful: " + amount);
                } else {
                    System.out.println("Withdrawal failed: " + amount);
                }
            }
        };
        Thread t1 = new Thread(r1);
        Thread t2 = new Thread(r2);
        t1.start();
        t2.start();
    }
}
```
