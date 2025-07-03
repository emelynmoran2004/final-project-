import java.io.*;
import java.util.ArrayList;
import java.util.Scanner;

public class Main {
    static ArrayList<String> inventory = new ArrayList<>();
    static String[] soldOut = new String[100]; // Fixed-size array for simplicity
    static int soldOutCount = 0;

    public static void main(String[] args) {
        loadData();

        Scanner scanner = new Scanner(System.in);
        int choice;
        do {
            printMenu();
            choice = getValidatedInteger(scanner);
            switch (choice) {
                case 1 -> viewInventory();
                case 2 -> addItem(scanner);
                case 3 -> sellItem(scanner);
                case 4 -> System.out.println("Exiting and saving...");
                default -> System.out.println("Invalid option. Try again.");
            }
        } while (choice != 4);

        saveData();
    }

    public static void printMenu() {
        System.out.println("\n=== Inventory Manager ===");
        System.out.println("1. View Inventory");
        System.out.println("2. Add Item");
        System.out.println("3. Sell Item");
        System.out.println("4. Exit");
        System.out.print("Choose an option: ");
    }

    public static int getValidatedInteger(Scanner scanner) {
        String input = scanner.nextLine();
        try {
            return Integer.parseInt(input);
        } catch (NumberFormatException e) {
            System.out.println("Invalid number. Try again.");
            return getValidatedInteger(scanner); // Recursive validation
        }
    }

    public static void viewInventory() {
        System.out.println("\n-- In Stock --");
        if (inventory.isEmpty()) {
            System.out.println("No items in inventory.");
        } else {
            for (int i = 0; i < inventory.size(); i++) {
                System.out.println(i + ": " + inventory.get(i));
            }
        }

        System.out.println("\n-- Sold Out --");
        for (int i = 0; i < soldOutCount; i++) {
            System.out.println(i + ": " + soldOut[i]);
        }
    }

    public static void addItem(Scanner scanner) {
        System.out.print("Enter item name to add: ");
        String item = scanner.nextLine();
        inventory.add(item);
        System.out.println(item + " added to inventory.");
    }

    public static void sellItem(Scanner scanner) {
        System.out.print("Enter item index to sell: ");
        int index = getValidatedInteger(scanner);
        try {
            String soldItem = inventory.remove(index);
            if (soldOutCount < soldOut.length) {
                soldOut[soldOutCount++] = soldItem;
                System.out.println(soldItem + " marked as sold.");
            } else {
                System.out.println("Sold out array is full!");
            }
        } catch (IndexOutOfBoundsException e) {
            System.out.println("Invalid index. Item not sold.");
        }
    }

    public static void saveData() {
        FileHandler.saveList(inventory, "inventory.txt");
        FileHandler.saveArray(soldOut, soldOutCount, "soldOut.txt");
    }

    public static void loadData() {
        inventory = FileHandler.loadList("inventory.txt");
        soldOutCount = FileHandler.loadArray(soldOut, "soldOut.txt");
    }
}
import java.io.*;
import java.util.ArrayList;

public class FileHandler {

    public static void saveList(ArrayList<String> list, String filename) {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(filename))) {
            for (String item : list) {
                writer.write(item);
                writer.newLine();
            }
        } catch (IOException e) {
            System.out.println("Error saving list: " + e.getMessage());
        }
    }

    public static void saveArray(String[] array, int count, String filename) {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(filename))) {
            for (int i = 0; i < count; i++) {
                writer.write(array[i]);
                writer.newLine();
            }
        } catch (IOException e) {
            System.out.println("Error saving array: " + e.getMessage());
        }
    }

    public static ArrayList<String> loadList(String filename) {
        ArrayList<String> list = new ArrayList<>();
        try (BufferedReader reader = new BufferedReader(new FileReader(filename))) {
            String line;
            while ((line = reader.readLine()) != null) {
                list.add(line);
            }
        } catch (IOException e) {
            System.out.println("Error loading list: " + e.getMessage());
        }
        return list;
    }

    public static int loadArray(String[] array, String filename) {
        int count = 0;
        try (BufferedReader reader = new BufferedReader(new FileReader(filename))) {
            String line;
            while ((line = reader.readLine()) != null && count < array.length) {
                array[count++] = line;
            }
        } catch (IOException e) {
            System.out.println("Error loading array: " + e.getMessage());
        }
        return count;
    }
}
