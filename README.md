# Cafe-Management-System
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileWriter;
import java.io.IOException;
import java.util.*;
class MenuItem {
private String name;
private double price;
private int quantity;
public MenuItem(String name, double price, int quantity) {
this.name = name;
this.price = price;
this.quantity = quantity;
}
public String getName() {
return name;
}
public double getPrice() {
return price;
}
public int getQuantity() {
return quantity;
}
public void setPrice(double price) {
this.price = price;
}
public void setQuantity(int quantity) {
this.quantity = quantity;
}
}
class CafeManagementSystem {
private static final String INVENTORY_FILE = "inventory.txt";
private static final String USERS_FILE = "users.txt";
private static final String BILLING_FILE = "billing.txt";
private static final double GST_RATE = 0.18;
private Map<String, MenuItem> inventory;
private Map<String, String> users;
public CafeManagementSystem() {
inventory = new HashMap<>();
users = new HashMap<>();
loadInventory();
loadUsers();
}
private void loadInventory() {
try {
File file = new File(INVENTORY_FILE);
Scanner scanner = new Scanner(file);
while (scanner.hasNextLine()) {
String line = scanner.nextLine();
String[] parts = line.split(",");
String name = parts[0];
double price = Double.parseDouble(parts[1]);
int quantity = Integer.parseInt(parts[2]);
MenuItem item = new MenuItem(name, price, quantity);
inventory.put(name, item);
}
scanner.close();
} catch (FileNotFoundException e) {
System.out.println("Inventory file not found.");
}
}
private void loadUsers() {
try {
File file = new File(USERS_FILE);
Scanner scanner = new Scanner(file);
while (scanner.hasNextLine()) {
String line = scanner.nextLine();
String[] parts = line.split
String mobileNumber = parts[0];
String password = parts[1];
users.put(mobileNumber, password);
}
scanner.close();
} catch (FileNotFoundException e) {
System.out.println("Users file not found.");
}
}
private void saveInventory() {
try {
FileWriter writer = new FileWriter(INVENTORY_FILE);
for (MenuItem item : inventory.values()) {
String line = item.getName() + "," + item.getPrice() + "," + item.getQuantity() + "\n";
writer.write(line);
}
writer.close();
} catch (IOException e) {
System.out.println("Error saving inventory.");
}
}
private void saveUsers() {
try {
FileWriter writer = new FileWriter(USERS_FILE);
for (Map.Entry<String, String> entry : users.entrySet()) {
String line = entry.getKey() + "," + entry.getValue() + "\n";
writer.write(line);
}
writer.close();
} catch (IOException e) {
System.out.println("Error saving users.");
}
}
private void saveBilling(String mobileNumber, List<String> itemNames, List<Integer>
quantities, double gstRate) {
try {
FileWriter writer = new FileWriter("billing.txt", true);
writer.write("Mobile Number: " + mobileNumber + "\n");
writer.write("---------------------------------------\n");
writer.write("Item Name \t Quantity \t Total\n");
writer.write("---------------------------------------\n");
double grandTotal = 0;
for (int i = 0; i < itemNames.size(); i++) {
String itemName = itemNames.get(i);
int quantity = quantities.get(i);
MenuItem item = inventory.get(itemName);
double subtotal = item.getPrice() * quantity;
double gstAmount = subtotal * gstRate;
double total = subtotal + gstAmount;
writer.write(itemName + "\t\t" + quantity + "\t\t" + total + "\n");
grandTotal += total;
}
writer.write("-------------------------------------\n");
writer.write("Grand Total (with GST): " + grandTotal + "\n");
writer.write("=====================================\n");
writer.close();
System.out.println("Mobile Number: " + mobileNumber);
System.out.println("---------------------------------------");
System.out.println("Item Name \t Quantity \t Total");
System.out.println("---------------------------------------");
for (int i = 0; i < itemNames.size(); i++) {
String itemName = itemNames.get(i);
int quantity = quantities.get(i);
MenuItem item = inventory.get(itemName);
double subtotal = item.getPrice() * quantity;
double gstAmount = subtotal * gstRate;
double total = subtotal + gstAmount;
System.out.printf("%-15s %9d %13.2f\n",itemName,quantity, total);
}
System.out.println("---------------------------------------");
System.out.println("Grand Total (with GST): " + grandTotal);
System.out.println("=======================================");
System.out.println("Billing data saved successfully.");
} catch (IOException e) {
System.out.println("Error saving billing data.");
}
}
public void addMenuItem(String name, double price, int quantity) {
MenuItem item = new MenuItem(name, price, quantity);
inventory.put(name, item);
saveInventory();
}
public void removeMenuItem(String name) {
inventory.remove(name);
saveInventory();
}
public void addUser(String mobileNumber,String password) {
users.put(mobileNumber, password);
saveUsers();
}
public void editMenuItem(String name, double newPrice, int newQuantity) {
MenuItem item = inventory.get(name);
if (item != null) {
item.setPrice(newPrice);
item.setQuantity(newQuantity);
saveInventory();
System.out.println("Item edited successfully.");
}else {
System.out.println("Item not found in the inventory.");
}
}
public void displayMenu() {
System.out.println("Menu:");
System.out.println("-------");
System.out.println("Item\t\tPrice\t\tQuantity");
System.out.println("----------------------------------------");
for (MenuItem item : inventory.values()) {
System.out.printf("%-15s%.2f\t\t%d\n", item.getName(), item.getPrice(),
item.getQuantity());
}
System.out.println();
}
public void placeOrder(String mobileNumber, List<String> itemNames, List<Integer>
quantities) {
double total = 0;
for (int i = 0; i < itemNames.size(); i++) {
String itemName = itemNames.get(i);
int quantity = quantities.get(i);
MenuItem item = inventory.get(itemName);
if (item == null) {
System.out.println("Item not found: " + itemName);
return;
}
if (item.getQuantity() < quantity) {
System.out.println("Not enough quantity available for item: " + itemName);
return;
}
double subtotal = item.getPrice() * quantity;
double gstAmount = subtotal * GST_RATE;
total += subtotal + gstAmount;
item.setQuantity(item.getQuantity() - quantity);
}
saveBilling(mobileNumber, itemNames, quantities, GST_RATE);
saveInventory();
System.out.println("Order placed successfully. ");
System.out.println("Total amount: " + total);
}
public boolean login(String mobileNumber, String password) {
return authenticateUser(mobileNumber, password);
}
private boolean authenticateUser(String mobileNumber, String password) {
try {
File file = new File("users.txt");
Scanner scanner = new Scanner(file);
while (scanner.hasNextLine()) {
String line = scanner.nextLine();
String[] parts = line.split(",");
String storedMobileNo = parts[0];
String storedPassword = parts[1];
if ((storedMobileNo.equals(mobileNumber) &&
storedPassword.equals(password))||(mobileNumber.equals("admin") &&
password.equals("password"))) {
scanner.close();
return true;
}
}
scanner.close();
} catch (FileNotFoundException e) {
System.out.println("Users file not found.");
}
return false;
}
}
public class Main {
public static void main(String[] args) {
CafeManagementSystem cafe = new CafeManagementSystem();
Scanner scanner = new Scanner(System.in);
int choice;
do {
System.out.println("\n\n\t\tCAFE MANAGEMENT SYSTEM\n\n");
System.out.println("1. Login into the System");
System.out.println("0. Exit out of System\n\n");
System.out.print("Enter your choice: ");
choice = scanner.nextInt();
switch (choice) {
case 1:
System.out.print("\n\nEnter Admin UserName OR Enter User's MobileNumber: ");
String username = scanner.next();
System.out.print("Enter password: ");
String password = scanner.next();
if (cafe.login(username, password)) {
System.out.println("Login successfully!");
int choice1;
do {
System.out.println("\n\n\t\tCAFE MANAGEMENT SYSTEM\n\n");
System.out.println("1. Add Item to Inventory");
System.out.println("2. Remove Item from Inventory");
System.out.println("3. Add User");
System.out.println("4. Display Menu");
System.out.println("5. Place Order");
System.out.println("6. Edit Item in Inventory");
System.out.println("0. Exit\n\n");
System.out.print("Enter your choice: ");
choice1 = scanner.nextInt();
switch (choice1) {
case 1:
System.out.print("Enter admin password:");
String pass=scanner.next();
if (pass.equals("password")){
System.out.print("Enter item name: ");
String name = scanner.next();
System.out.print("Enter item price: ");
double price = scanner.nextDouble();
System.out.print("Enter item quantity: ");
int quantity = scanner.nextInt();
cafe.addMenuItem(name, price, quantity);
System.out.println("Item added to inventory.");
break;
}else {
System.out.println("Wrong admin password!!!");
}
break;
case 2:
System.out.print("Enter admin password:");
pass=scanner.next();
if (pass.equals("password")){
System.out.print("Enter item name: ");
String name = scanner.next();
cafe.removeMenuItem(name);
System.out.println("Item removed from inventory.");
break;
}else {
System.out.println("Wrong admin password!!!");
}break;
case 3:
System.out.print("Enter admin password:");
pass=scanner.next();
if (pass.equals("password")){
System.out.print("Enter mobile number: ");
String mobileNumber = scanner.next();
System.out.print("Enter password: ");
password = scanner.next();
cafe.addUser(mobileNumber, password);
System.out.println("User added.");
break;
}else {
System.out.println("Wrong admin password!!!");
}break;
case 4:
cafe.displayMenu();
break;
case 5:
cafe.displayMenu();
System.out.print("Enter mobile number: ");
String mobileNumber = scanner.next();
List<String> itemNames = new ArrayList<>();
List<Integer> quantities = new ArrayList<>();
char ch;
do{
itemNames.clear();
quantities.clear();
System.out.print("Enter number of items: ");
int numItems = scanner.nextInt();
for (int i = 1; i <= numItems; i++) {
System.out.println("Enter details for Item " + i);
System.out.print("Item name: ");
String name = scanner.next();
System.out.print("Quantity: ");
int quantity = scanner.nextInt();
itemNames.add(name);
quantities.add(quantity);
}
System.out.println("Do you want to place order?");
System.out.println("(For yes enter 'Y'/For No enter 'N')");
ch= scanner.next().charAt(0);
}while((ch!='Y')&&(ch!='y'));
cafe.placeOrder(mobileNumber, itemNames, quantities);
break;
case 6:
System.out.print("Enter admin password:");
pass=scanner.next();
if (pass.equals("password")){
cafe.displayMenu();
System.out.print("Enter item name: ");
String name = scanner.next();
System.out.print("Enter new item price: ");
double newPrice = scanner.nextDouble();
System.out.print("Enter new item quantity: ");
int newQuantity = scanner.nextInt();
cafe.editMenuItem(name, newPrice, newQuantity);
break;
}else {
System.out.println("Wrong admin password!!!");
}
break;
case 0:
System.out.println("Exiting...");
break;
default:
System.out.println("Invalid choice. Try again.");
}
} while (choice1 != 0);
} else {
System.out.println("Invalid username or password. Please try again.");
}
break;
case 0:
System.out.println("Exiting...");
break;
default:
System.out.println("Invalid choice. Try again.");
}
} while (choice != 0);
scanner.close();
}
}
