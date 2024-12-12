# Supermarket_management_system
#include <iostream>
#include <fstream>  // For file handling
#include <sstream>  // For string stream to parse file data
using namespace std;
struct Review {
    int rating;  // Rating out of 5
    string review;  // Review text
};
struct Item {
    int id;
    string name;
    int quantity;
    double price;
    string expiryDate;
    Review review;
};
struct Node {
    Item data;
    Node* next;
};
class SuperMarketInfo {
private:
    string name;
    string address;
    string contactNumber;
public:
    SuperMarketInfo(string n, string a, string num) {
        name = n;
        address = a;
        contactNumber = num;
    }
    void displayInfo() {
        cout << "Supermarket Details" << endl;
        cout << "Supermarket Name: " << name << endl;
        cout << "Address: " << address << endl;
        cout << "Contact Number: " << contactNumber << endl;
        cout << "___________________________" << endl;
    }
};
class SuperMarketInventory {
private:
    Node* inventoryHead;
    int inventoryCount;
    double initialSales;
public:
    SuperMarketInventory() {
        inventoryHead = NULL;
        inventoryCount = 0;
        initialSales = 16.0;
       // loadInventoryFromFile();  // Load inventory from file when the program starts
       // Initializing inventory
        addItemToInventory({1, "Apple", 50, 0.5, "2024-12-31"});
        addItemToInventory({2, "Banana", 100, 0.3, "2024-11-15"});
        addItemToInventory({3, "Milk", 30, 1.2, "2024-11-20"});
        addItemToInventory({4, "Bread", 40, 1.5, "2024-11-10"});
        addItemToInventory({5, "Eggs (Dozen)", 20, 2.0, "2024-11-25"});
        addItemToInventory({6, "Orange Juice", 25, 3.0, "2024-12-05"});
        addItemToInventory({7, "Butter", 15, 1.8, "2024-11-18"});
        addItemToInventory({8, "Cheese", 10, 2.5, "2024-11-22"});
        addItemToInventory({9, "Rice (kilogram)", 30, 3.8, "2024-9-18"});
        addItemToInventory({10, "Cake", 10, 2.5, "2024-11-22"});
    }
    ~SuperMarketInventory() {
        saveInventoryToFile();  // Save inventory to file when the program exits
    }
    void addItemToInventory(const Item& newItem) {
        Node* newNode = new Node{newItem, nullptr};
        if (inventoryHead == nullptr) {
            inventoryHead = newNode;
        } else {
            Node* temp = inventoryHead;
            while (temp->next != nullptr) {
                temp = temp->next;
            }
            temp->next = newNode;
        }
        inventoryCount++;
    }
    void saveInventoryToFile() {
        ofstream outFile("inventory.txt",ios::app);
        Node* temp = inventoryHead;
        while (temp != nullptr) {
            outFile << temp->data.id << ","
                    << temp->data.name << ","
                    << temp->data.quantity << ","
                    << temp->data.price << ","
                    << temp->data.expiryDate << "\n";
            temp = temp->next;
        }
        outFile.close();
    }
    void loadInventoryFromFile() {
        ifstream inFile("inventory.txt",ios::in);
        string line;
        while (getline(inFile, line)) {
            stringstream ss(line);
            Item item;
            string token;
            getline(ss, token, ',');
            item.id = stoi(token);  // Parse ID
            getline(ss, token, ',');
            item.name = token;  // Parse name
            getline(ss, token, ',');
            item.quantity = stoi(token);  // Parse quantity
            getline(ss, token, ',');
            item.price = stod(token);  // Parse price
            getline(ss, token, ',');
            item.expiryDate = token;  // Parse expiry date
            addItemToInventory(item);  // Add item to the inventory list
        }
        inFile.close();
    }
    void searchItemById(int id) {
        Node* temp = inventoryHead;
        while (temp != nullptr) {
            if (temp->data.id == id) {
                cout << "Item found: ID: " << temp->data.id << ", Name: " << temp->data.name
                     << ", Price: " << temp->data.price << ", Expiry: " << temp->data.expiryDate << endl;
                return;
            }
            temp = temp->next;
        }
        cout << "Item not found!" << endl;
    }
    void updateInventoryAfterPurchase(const string& itemName, int quantityPurchased) {
        Node* temp = inventoryHead;
        while (temp != nullptr) {
            if (temp->data.name == itemName) {
                if (temp->data.quantity >= quantityPurchased) {
                    temp->data.quantity -= quantityPurchased;
                    cout << "Updated quantity for " << itemName << ": " << temp->data.quantity << endl;
                    return;
                } else {
                    cout << "Not enough stock for " << itemName << endl;
                    return;
                }
            }
            temp = temp->next;
        }  
    }
    void purchaseItem() {
        string itemName;
        int quantity;
        // Ask user for input
        cout << "Enter the name of the item you want to buy: ";
        cin >> itemName;
        cout << "Enter the quantity of " << itemName << " you want to purchase: ";
        cin >> quantity;
        // Check if the item exists in the inventory and update the quantity
        updateInventoryAfterPurchase(itemName, quantity);
    }
    void changeItemPrice(int itemID, double newPrice) {
        Node* temp = inventoryHead;
        while (temp != nullptr) {
            if (temp->data.id == itemID) {
                temp->data.price = newPrice;
                cout << "Price of item with ID " << itemID << " has been updated to $" << newPrice << endl;
                return;
            }
            temp = temp->next;
        }
        cout << "Item with ID " << itemID << " not found in the inventory." << endl;
    }
    void checkPrice(int itemID) {
        Node* temp = inventoryHead;
        while (temp != nullptr) {
            if (temp->data.id == itemID) {
                cout << "Price of item with ID " << itemID << ": $" << temp->data.price << endl;
                return;
            }
            temp = temp->next;
        }
        cout << "Item with ID " << itemID << " not found in the inventory." << endl;
    }
    void checkExpiryDate(int itemID) {
        Node* temp = inventoryHead;
        while (temp != nullptr) {
            if (temp->data.id == itemID) {
                cout << "Expiry date of item with ID " << itemID << ": " << temp->data.expiryDate << endl;
                return;
            }
            temp = temp->next;
        }
        cout << "Item with ID " << itemID << " not found in the inventory." << endl;
    }
    void sortInventoryByPrice() {
        if (!inventoryHead || !inventoryHead->next) return;

        for (Node* i = inventoryHead; i != nullptr; i = i->next) {
            for (Node* j = i->next; j != nullptr; j = j->next) {
                if (i->data.price > j->data.price) {
                    swap(i->data, j->data);
                }
            }
        }
    }
    void addReview(int itemId, int rating, const string& review) {
        Node* temp = inventoryHead;
        while (temp != nullptr) {
            if (temp->data.id == itemId) {
                // Add the review to the Item's review field
                temp->data.review = {rating, review};
                cout << "Review added for item ID " << itemId << ": " << review << endl;
                return;
            }
            temp = temp->next;
        }
        cout << "Item with ID " << itemId << " not found in the inventory." << endl;
    }
    void displayReview(int itemId) {
        Node* temp = inventoryHead;
        while (temp != nullptr) {
            if (temp->data.id == itemId) {
                // Display the review for the item
                cout << "Review for item ID " << itemId << ": "
                     << "Rating: " << temp->data.review.rating << "/5, "
                     << "Review: " << temp->data.review.review << endl;
                return;
            }
            temp = temp->next;
        }
        cout << "No reviews for this item." << endl;
    }
    void displayInventory() {
        if (inventoryHead == nullptr) {
            cout << "Inventory is empty!" << endl;
            return;
        }
        Node* temp = inventoryHead;
        cout << "Current Inventory:" << endl;
        while (temp != nullptr) {
            cout << "ID: " << temp->data.id << endl;
            cout << "Name: " << temp->data.name << endl;
            cout << "Quantity: " << temp->data.quantity << endl;
            cout << "Price: $" << temp->data.price << endl;
            cout << "Expiry Date: " << temp->data.expiryDate << endl;
            temp = temp->next;
        }
    }
    bool isInventoryEmpty() const {
        return inventoryHead == nullptr;
    }
};
class Cart {
private:
    Node* cartHead;       
    int cartCount;        

public:
    Cart() {
        cartHead = nullptr;
        cartCount = 0;
        loadCartFromFile();  // Load cart items from file when the cart is created
    }
    ~Cart() {
        saveCartToFile();  // Save cart items to file when the cart is destroyed
    }
    void addItemToCart(const Item& newItem) {
        Node* newNode = new Node{newItem, nullptr};
        if (cartHead == nullptr) {
            cartHead = newNode;
        } else {
            Node* temp = cartHead;
            while (temp->next != nullptr) {
                temp = temp->next;
            }
            temp->next = newNode;
        }
        cartCount++;
        saveCartToFile();  // Save updated cart to file
    }
   void replaceItemInCart(const string& oldItemName, const Item& newItem) {
    Node* temp = cartHead;
    bool itemFound = false;

    while (temp != nullptr) {
        if (temp->data.name == oldItemName) {
            temp->data = newItem; // Replace the old item with the new item
            cout << "Replaced item: " << oldItemName << " with new item: " << newItem.name << endl;
            itemFound = true;
            saveCartToFile();  // Save updated cart to file
            break;
        }
        temp = temp->next;
    }

    if (!itemFound) {
        cout << "Item " << oldItemName << " not found in the cart." << endl;
    }
}
    void removeItemFromCart(const string& itemName) {
        if (!cartHead) {
            cout << "Cart is empty, no items to remove." << endl;
            return;
        }
        Node* temp = cartHead;
        Node* temp1 = nullptr;

        while (temp != nullptr && temp->data.name != itemName) {
            temp1 = temp;
            temp = temp->next;
        }
        if (temp != nullptr) {
            if (temp1 == nullptr) {
                cartHead = temp->next;
            } else {
                temp1->next = temp->next;
            }
            delete temp;
            cartCount--;
            cout << "Removed item: " << itemName << endl;
            saveCartToFile();  // Save updated cart to file
        } else {
            cout << "Item " << itemName << " not found in the cart." << endl;
        }
    }
    void sortCartByName() {
        if (!cartHead || !cartHead->next) return;
        for (Node* i = cartHead; i != nullptr; i = i->next) {
            for (Node* j = i->next; j != nullptr; j = j->next) {
                if (i->data.name > j->data.name) {
                    swap(i->data, j->data);
                }
            }
        }
        saveCartToFile();  // Save updated cart to file after sorting
    }
    void displayCart() {
        if (cartHead == nullptr) {
            cout << "Cart is empty!" << endl;
            return;
        }
        Node* temp = cartHead;
        cout << "Current Cart:" << endl;
        while (temp != nullptr) {
            cout << "ID: " << temp->data.id << endl;
            cout << "Name: " << temp->data.name << endl;
            cout << "Price: $" << temp->data.price << endl;
            cout << "Expiry Date: " << temp->data.expiryDate << endl;
            temp = temp->next;
        }
    }
    void clearCart() {
        Node* temp = cartHead;
        while (temp != nullptr) {
            Node* temp1 = temp;
            temp = temp->next;
            delete temp1;
        }
        cartHead = nullptr;
        cartCount = 0;
        saveCartToFile();  // Save empty cart to file
    }
    bool isEmpty() const {
        return cartHead == nullptr;
    }
    double calculateBill() {
        double totalBill = 0.0;
        Node* temp = cartHead;
        while (temp != nullptr) {
            totalBill += temp->data.quantity * temp->data.price;
            temp = temp->next;
        }
        return totalBill;
    }
    double applyDiscount(double totalBill) {
        return totalBill * 0.9;  // Assume a 10% discount
    }
Cart*next;
public:
    void saveCartToFile() {
        ofstream outFile("cart.txt",ios::app);
        Node* temp = cartHead;
        while (temp != nullptr) {
            outFile << temp->data.id << ","
                    << temp->data.name << ","
                    << temp->data.quantity << ","
                    << temp->data.price << ","
                    << temp->data.expiryDate << "\n";
            temp = temp->next;
        }
        outFile.close();
    }
    void loadCartFromFile() {
        ifstream inFile("cart.txt",ios::in);
        string line;
        while (getline(inFile, line)) {
            stringstream ss(line);
            Item item;
            string token;
            getline(ss, token, ',');
            item.id = stoi(token);

            getline(ss, token, ',');
            item.name = token;

            getline(ss, token, ',');
            item.quantity = stoi(token);

            getline(ss, token, ',');
            item.price = stod(token);

            getline(ss, token, ',');
            item.expiryDate = token;

            addItemToCart(item);
        }
        inFile.close();
    }
    
};
class CustomerQueue {
private:
    Cart* front;
    Cart* rear;

public:
    CustomerQueue() {
        front = nullptr;
        rear = nullptr;
    }

    void enqueue(Cart* customerCart) {
        if (!rear) {
            front = rear = customerCart;
        } else {
            rear->next = customerCart;
            rear = customerCart;
        }
    }

    void processFirstCustomer() {
        if (!front) {
            cout << "No customers in the queue!" << endl;
            return;
        }
        Cart* customer = front;
        cout << "\nProcessing first customer in the queue..." << endl;
        customer->displayCart();
        double totalBill = customer->calculateBill();
        double discountedBill = customer->applyDiscount(totalBill);
        cout << "\nTotal Bill after discount: $" << discountedBill << endl;
       selectPaymentMethod();

        customer->clearCart();
        front = front->next;
        if (!front) {
            rear = nullptr;
        }
        delete customer;
    }
     

    void selectPaymentMethod() {
        int paymentChoice;
        cout << "\nSelect Payment Method:\n";
        cout << "1. Cash\n";
        cout << "2. Card\n";
        cout << "Enter your choice (1 for Cash, 2 for Card): ";
        cin >> paymentChoice;

        switch (paymentChoice) {
            case 1:
                cout << "You chose Cash. Payment successful!" << endl;
                break;
            case 2:
                cout << "You chose Card. Payment successful!" << endl;
                break;
            default:
                cout << "Invalid choice, please try again!" << endl;
                selectPaymentMethod();  // Recursively ask for valid input
                break;
        }
    }

    bool isEmpty() const {
        return front == nullptr;
    }
    
    

};
    int main(){
    SuperMarketInfo supermarket("FreshMart", "123 Green Valley", "+123456789");
    SuperMarketInventory inventory;
    Cart customerCart;
    CustomerQueue customerQueue;
    int choice;
    bool exitProgram = false;

    while (!exitProgram) {
cout << "\nSupermarket System Menu" << endl;
    cout << "1. Display Supermarket Information" << endl;
    cout << "2. Display Inventory" << endl;
    cout << "3. Search Item by ID" << endl;
    cout << "4. Update Inventory After Purchase" << endl;
    cout << "5. Change Item Price" << endl;
    cout << "6. Check Price of an Item" << endl;
    cout << "7. Check Expiry Date of an Item" << endl;
    cout << "8. Sort Inventory by Price" << endl;
    cout << "9. Add Review for an Item" << endl;
    cout << "10. Display Item Review" << endl;
    cout << "11. Add Item to Cart" << endl;
    cout << "12. Remove Item from Cart" << endl;
    cout << "13. Replace Item in Cart" << endl;
    cout << "14. Sort Cart by Item Name" << endl;
    cout << "15. Calculate Total Bill" << endl;
    cout << "16. Apply Discount on Total Bill" << endl;
    cout<<  "17.selectPaymentMethod"<<endl;
    cout << "18. Process Customer Checkout" << endl;
    cout << "19. Clear Cart" << endl;
    cout << "20. save file inventory" << endl;
    cout << "21. save file to cart" << endl;
    cout << "22. Exit" << endl;
    cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1:
                supermarket.displayInfo();
                break;
            case 2:
                inventory.displayInventory();
                break;
            case 3: {
                int id;
                cout << "Enter Item ID to search: ";
                cin >> id;
                inventory.searchItemById(id);
                break;
            }
            case 4: {
                string itemName;
                int quantity;
                cout << "Enter item name to purchase: ";
                cin >> itemName;
                cout << "Enter quantity: ";
                cin >> quantity;
                inventory.updateInventoryAfterPurchase(itemName, quantity);
                break;
            }
            case 5: {
                int itemId;
                double newPrice;
                cout << "Enter Item ID to change price: ";
                cin >> itemId;
                cout << "Enter new price: ";
                cin >> newPrice;
                inventory.changeItemPrice(itemId, newPrice);
                break;
            }
            case 6: {
                int itemId;
                cout << "Enter Item ID to check price: ";
                cin >> itemId;
                inventory.checkPrice(itemId);
                break;
            }
            case 7: {
                int itemId;
                cout << "Enter Item ID to check expiry date: ";
                cin >> itemId;
                inventory.checkExpiryDate(itemId);
                break;
            }
            case 8:
                inventory.sortInventoryByPrice();
                inventory.displayInventory();
                break;
            case 9: {
                int itemId, rating;
                string review;
                cout << "Enter Item ID to review: ";
                cin >> itemId;
                cout << "Enter Rating (1-5): ";
                cin >> rating;
                cin.ignore();  // To clear the newline left by cin
                cout << "Enter Review: ";
                getline(cin, review);
                inventory.addReview(itemId, rating, review);
                break;
            }
            case 10: {
                int itemId;
                cout << "Enter Item ID to display review: ";
                cin >> itemId;
                inventory.displayReview(itemId);
                break;
            }
            case 11: {
                int id, quantity;
                string name;
                double price;
                string expiryDate;
                cout << "Enter Item ID to add to cart: ";
                cin >> id;
                cout << "Enter Item name: ";
                cin >> name;
                cout << "Enter Quantity: ";
                cin >> quantity;
                cout << "Enter Price: ";
                cin >> price;
                cout << "Enter Expiry Date: ";
                cin >> expiryDate;
                Item itemToAdd = {id, name, quantity, price, expiryDate};
                customerCart.addItemToCart(itemToAdd);
                break;
            }
            case 12: {
                string itemName;
                cout << "Enter Item name to remove from cart: ";
                cin >> itemName;
                customerCart.removeItemFromCart(itemName);
                break;
            }
            case 13: {
        string oldItemName, newItemName;
         cout << "Enter item name to replace in cart: ";
        cin >> oldItemName;

       cout << "Enter new item name to add: ";
      cin >> newItemName;

    int newItemId = 8;  // Set appropriate item ID
    int newItemQuantity = 1; // Set appropriate quantity
    double newItemPrice = 10.0; // Set appropriate price
    string newItemExpiryDate = "2025-12-31";  // Set appropriate expiry date

    // Create a new item object
    Item newItem = {newItemId, newItemName, newItemQuantity, newItemPrice, newItemExpiryDate};

    // Call replaceItemInCart with the old item's name and the new item object
    customerCart.replaceItemInCart(oldItemName, newItem);
    break;
}
            case 14:
                customerCart.sortCartByName();
                customerCart.displayCart();
                break;
            case 15: {
                double total = customerCart.calculateBill();
                cout << "Total bill before discount: $" << total << endl;
                break;
            }
            case 16: {
                double discount;
                cout << "Enter discount percentage: ";
                cin >> discount;
                double totalBill = customerCart.calculateBill();
                double discountedBill = customerCart.applyDiscount(totalBill);
                cout << "Total bill after " << discount << "% discount: $" << discountedBill << endl;
                break;
            }
             case 17: {
                 customerQueue.selectPaymentMethod();
                break;
            }
            case 18: {
                customerQueue.processFirstCustomer();
                break;
            }
            case 19: {
                customerCart.clearCart();
                break;
            }

            case 20: {
                inventory.saveInventoryToFile(); 
                break;
            }
            case 21: {
                customerCart.saveCartToFile();;
                break;
            }
            case 22:
                cout << "Exiting the program." << endl;
                exitProgram = true;
                break;
            default:
                cout << "Invalid choice, please try again." << endl;
        }
    }

    return 0;
}
