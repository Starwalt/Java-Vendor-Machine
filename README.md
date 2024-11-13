Designing a vending machine using Object-Oriented Programming (OOP) principles involves applying concepts like abstraction, encapsulation, inheritance, and polymorphism to structure the solution. Here's a breakdown of how to implement a vending machine using these principles:

Requirements for the Vending Machine

1. Accepts coins or notes.


2. Displays available items.


3. Handles item selection and dispenses items.


4. Returns change if necessary.


5. Allows restocking of items and cash.


6. Handles refunds in case of errors or canceled transactions.



Key OOP Concepts Applied

Encapsulation: Keep data (like inventory, inserted money) private and provide methods to interact with it.

Abstraction: Hide the complex logic (like handling change, transactions) inside relevant classes.

Polymorphism: Use interfaces and polymorphic behavior for different payment methods.

Inheritance: Extend common functionality if you want to support different types of vending machines.


Class Design

Here’s an example of how to design the system:

1. VendingMachine Interface: Defines the general operations of a vending machine.


2. Item Class: Represents an item inside the vending machine.


3. Coin Enum: Represents different denominations accepted by the vending machine.


4. VendingMachineImpl Class: Implements the VendingMachine interface and handles the core logic.


5. Inventory Class: Handles stock of items and cash.


6. Payment Interface (Optional): To support different payment methods (like Coin, Card, etc.).



Implementation Example

Step 1: Item Class

public class Item {
    private String name;
    private double price;

    public Item(String name, double price) {
        this.name = name;
        this.price = price;
    }

    // Getters
    public String getName() {
        return name;
    }

    public double getPrice() {
        return price;
    }
}

Step 2: Coin Enum

public enum Coin {
    PENNY(0.01), NICKEL(0.05), DIME(0.10), QUARTER(0.25), DOLLAR(1.00);

    private double value;

    Coin(double value) {
        this.value = value;
    }

    public double getValue() {
        return value;
    }
}

Step 3: VendingMachine Interface

import java.util.List;

public interface VendingMachine {
    void selectItemAndInsertMoney(Item item, List<Coin> payment);
    void dispenseItem();
    void cancelTransaction();
    void showAvailableItems();
    void reset();
}

Step 4: Inventory Class

import java.util.HashMap;
import java.util.Map;

public class Inventory<T> {
    private Map<T, Integer> inventory = new HashMap<>();

    // Add an item to the inventory
    public void add(T item) {
        inventory.put(item, inventory.getOrDefault(item, 0) + 1);
    }

    // Remove an item from the inventory
    public void remove(T item) {
        if (hasItem(item)) {
            inventory.put(item, inventory.get(item) - 1);
        }
    }

    // Check if an item is in the inventory
    public boolean hasItem(T item) {
        return inventory.getOrDefault(item, 0) > 0;
    }

    // Get item count
    public int getQuantity(T item) {
        return inventory.getOrDefault(item, 0);
    }

    // Reset inventory
    public void clear() {
        inventory.clear();
    }
}

Step 5: VendingMachineImpl Class

import java.util.ArrayList;
import java.util.List;

public class VendingMachineImpl implements VendingMachine {
    private Inventory<Item> itemInventory = new Inventory<>();
    private Inventory<Coin> cashInventory = new Inventory<>();
    private Item currentItem;
    private double currentBalance;
    private List<Coin> currentCoins = new ArrayList<>();

    public VendingMachineImpl() {
        initialize();
    }

    private void initialize() {
        // Initialize with some items and cash
        itemInventory.add(new Item("Soda", 1.25));
        itemInventory.add(new Item("Chips", 1.50));
        itemInventory.add(new Item("Candy", 0.75));

        cashInventory.add(Coin.QUARTER);
        cashInventory.add(Coin.DIME);
        cashInventory.add(Coin.NICKEL);
        cashInventory.add(Coin.DOLLAR);
    }

    @Override
    public void selectItemAndInsertMoney(Item item, List<Coin> payment) {
        if (!itemInventory.hasItem(item)) {
            System.out.println("Item is not available");
            return;
        }
        currentItem = item;
        currentCoins = payment;
        currentBalance = payment.stream().mapToDouble(Coin::getValue).sum();

        if (currentBalance < item.getPrice()) {
            System.out.println("Insufficient payment. Insert more coins.");
        }
    }

    @Override
    public void dispenseItem() {
        if (currentItem == null || currentBalance < currentItem.getPrice()) {
            System.out.println("Please select an item and insert enough money.");
            return;
        }

        System.out.println("Dispensing item: " + currentItem.getName());
        itemInventory.remove(currentItem);
        currentBalance -= currentItem.getPrice();

        // Return change if needed
        if (currentBalance > 0) {
            System.out.println("Returning change: $" + currentBalance);
        }

        // Reset transaction
        currentItem = null;
        currentBalance = 0;
        currentCoins.clear();
    }

    @Override
    public void cancelTransaction() {
        System.out.println("Transaction canceled. Returning money: $" + currentBalance);
        currentItem = null;
        currentBalance = 0;
        currentCoins.clear();
    }

    @Override
    public void showAvailableItems() {
        System.out.println("Available items:");
        for (Item item : itemInventory.getItems()) {
            System.out.println(item.getName() + " - $" + item.getPrice());
        }
    }

    @Override
    public void reset() {
        itemInventory.clear();
        cashInventory.clear();
        currentItem = null;
        currentBalance = 0;
        currentCoins.clear();
        initialize();
    }
}

Explanation of the Design

1. Encapsulation: Data like inventory and balance are private, accessed only through public methods.


2. Abstraction: Complex operations like handling item selection, dispensing, and cash management are hidden within the methods.


3. Polymorphism: You can extend this design to support different types of payment methods by creating a Payment interface and implementing multiple payment strategies.


4. Inheritance: You can extend VendingMachineImpl for specialized vending machines (like a SnackVendingMachine or DrinkVendingMachine).



Additional Enhancements

1. Payment Interface: Support card payments or QR code payments.


2. Exception Handling: Add custom exceptions for errors like InsufficientFundsException or OutOfStockException.


3. Admin Interface: Add methods to restock items or view transaction logs.


4. Persistence: Store the state in a database to keep track of inventory and transactions.



This design provides a solid foundation, applying OOP principles to create a maintainable, scalable vending machine implementation.

