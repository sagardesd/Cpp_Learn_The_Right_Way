# Abstract Classes and Pure Virtual Functions

## Table of Contents
- [What is an Abstract Class?](#what-is-an-abstract-class)
  - [Pure Virtual Function](#pure-virtual-function)
  - [Example: Basic Abstract Class](#example-basic-abstract-class)
- [Benefits and Use Cases](#benefits-and-use-cases)
  - [1. Enforcing a Contract (Interface)](#1-enforcing-a-contract-interface)
  - [2. Code Reusability with Polymorphism](#2-code-reusability-with-polymorphism)
  - [3. Framework Design](#3-framework-design)
- [OOP Concept: Abstraction](#oop-concept-abstraction)
  - [What is Abstraction?](#what-is-abstraction)
  - [How Abstract Classes Achieve Abstraction](#how-abstract-classes-achieve-abstraction)
- [Special Notes: Non-Pure Virtual Functions in Abstract Classes](#special-notes-non-pure-virtual-functions-in-abstract-classes)
  - [Example: Mixed Functions](#example-mixed-functions)
  - [How to Invoke Non-Pure Virtual Functions?](#how-to-invoke-non-pure-virtual-functions)
  - [Why Use Non-Pure Virtual Functions in Abstract Classes?](#why-use-non-pure-virtual-functions-in-abstract-classes)
- [Key Takeaways](#key-takeaways)

---

## What is an Abstract Class?

An **abstract class** is a class that cannot be instantiated directly and is designed to serve as a base class for other classes. It acts as a blueprint that defines the interface (contract) that derived classes must implement.

A class becomes abstract when it contains at least one **pure virtual function**.

### Pure Virtual Function

A **pure virtual function** is a virtual function that has no implementation in the base class and must be overridden by derived classes. It is declared by assigning `= 0` to the function declaration.

**Syntax:**
```cpp
virtual return_type function_name(parameters) = 0;
```

### Example: Basic Abstract Class

```cpp
#include <iostream>
#include <string>
using namespace std;

// Abstract class - cannot be instantiated
class Shape {
protected:
    string color;
    
public:
    Shape(string c) : color(c) {}
    
    // Pure virtual function - makes Shape abstract
    virtual double calculateArea() = 0;
    
    // Pure virtual function
    virtual void draw() = 0;
    
    // Regular member function
    void setColor(string c) {
        color = c;
    }
    
    string getColor() {
        return color;
    }
};

// Concrete class - must implement all pure virtual functions
class Circle : public Shape {
private:
    double radius;
    
public:
    Circle(string c, double r) : Shape(c), radius(r) {}
    
    // Must override pure virtual function
    double calculateArea() override {
        return 3.14159 * radius * radius;
    }
    
    void draw() override {
        cout << "Drawing a " << color << " circle" << endl;
    }
};

class Rectangle : public Shape {
private:
    double width, height;
    
public:
    Rectangle(string c, double w, double h) : Shape(c), width(w), height(h) {}
    
    double calculateArea() override {
        return width * height;
    }
    
    void draw() override {
        cout << "Drawing a " << color << " rectangle" << endl;
    }
};

int main() {
    // Shape s("red");  // ERROR! Cannot instantiate abstract class
    
    Circle c("blue", 5.0);
    Rectangle r("green", 4.0, 6.0);
    
    cout << "Circle area: " << c.calculateArea() << endl;
    c.draw();
    
    cout << "Rectangle area: " << r.calculateArea() << endl;
    r.draw();
    
    // Polymorphism with abstract class pointers
    Shape* shapes[2];
    shapes[0] = &c;
    shapes[1] = &r;
    
    cout << "\nUsing polymorphism:" << endl;
    for(int i = 0; i < 2; i++) {
        cout << "Area: " << shapes[i]->calculateArea() << endl;
        shapes[i]->draw();
    }
    
    return 0;
}
```

**Output:**
```
Circle area: 78.5397
Drawing a blue circle
Rectangle area: 24
Drawing a green rectangle

Using polymorphism:
Area: 78.5397
Drawing a blue circle
Area: 24
Drawing a green rectangle
```

[⬆ Back to Table of Contents](#table-of-contents)

---

## Benefits and Use Cases

### 1. Enforcing a Contract (Interface)

Abstract classes ensure that all derived classes implement specific methods, creating a consistent interface.

```cpp
class Database {
public:
    // All database implementations must provide these operations
    virtual void connect(string connectionString) = 0;
    virtual void disconnect() = 0;
    virtual void executeQuery(string query) = 0;
    virtual ~Database() {}
};

class MySQLDatabase : public Database {
public:
    void connect(string connectionString) override {
        cout << "Connecting to MySQL: " << connectionString << endl;
    }
    
    void disconnect() override {
        cout << "Disconnecting from MySQL" << endl;
    }
    
    void executeQuery(string query) override {
        cout << "Executing MySQL query: " << query << endl;
    }
};

class PostgreSQLDatabase : public Database {
public:
    void connect(string connectionString) override {
        cout << "Connecting to PostgreSQL: " << connectionString << endl;
    }
    
    void disconnect() override {
        cout << "Disconnecting from PostgreSQL" << endl;
    }
    
    void executeQuery(string query) override {
        cout << "Executing PostgreSQL query: " << query << endl;
    }
};
```

### 2. Code Reusability with Polymorphism

Abstract classes allow you to write generic code that works with any derived class.

```cpp
void performDatabaseOperations(Database* db) {
    db->connect("server=localhost");
    db->executeQuery("SELECT * FROM users");
    db->disconnect();
}

int main() {
    MySQLDatabase mysql;
    PostgreSQLDatabase postgres;
    
    performDatabaseOperations(&mysql);      // Works with MySQL
    performDatabaseOperations(&postgres);   // Works with PostgreSQL
    
    return 0;
}
```

### 3. Framework Design

Abstract classes are perfect for creating frameworks where the core structure is defined but implementation details are left to users.

```cpp
class GameCharacter {
protected:
    string name;
    int health;
    
public:
    GameCharacter(string n, int h) : name(n), health(h) {}
    
    // Framework defines the game loop
    void takeTurn() {
        cout << name << "'s turn:" << endl;
        performAction();  // Specific to each character type
        if(canUseSpecialAbility()) {
            useSpecialAbility();
        }
    }
    
    // Must be implemented by each character type
    virtual void performAction() = 0;
    virtual void useSpecialAbility() = 0;
    virtual bool canUseSpecialAbility() = 0;
    virtual ~GameCharacter() {}
};

class Warrior : public GameCharacter {
public:
    Warrior(string n) : GameCharacter(n, 150) {}
    
    void performAction() override {
        cout << "Warrior attacks with sword!" << endl;
    }
    
    void useSpecialAbility() override {
        cout << "Warrior uses RAGE mode!" << endl;
    }
    
    bool canUseSpecialAbility() override {
        return health < 50;  // Can rage when low health
    }
};

class Mage : public GameCharacter {
private:
    int mana = 100;
    
public:
    Mage(string n) : GameCharacter(n, 80) {}
    
    void performAction() override {
        cout << "Mage casts fireball!" << endl;
    }
    
    void useSpecialAbility() override {
        cout << "Mage teleports!" << endl;
        mana -= 30;
    }
    
    bool canUseSpecialAbility() override {
        return mana >= 30;
    }
};
```

## OOP Concept: Abstraction

**Abstraction** is one of the four pillars of Object-Oriented Programming (encapsulation, inheritance, polymorphism, and abstraction).

### Real-World Example: Driving a Car

Think about driving a car. When you drive, you interact with simple controls:
- **Steering wheel** - turn it to change direction
- **Accelerator pedal** - press it to go faster
- **Brake pedal** - press it to slow down
- **Gear shift** - move it to change gears

As a driver, you don't need to know:
- How the engine combusts fuel
- How the transmission system works
- How the braking system applies friction to the wheels
- How the power steering mechanism functions

The car's interface (steering wheel, pedals) **abstracts away** all the complex mechanical and electronic systems underneath. You focus on **what** you want to do (turn, accelerate, stop) rather than **how** the car makes it happen.

This is exactly what abstraction does in programming - it hides the complex implementation details and provides a simple interface to interact with.

### What is Abstraction?

Abstraction means hiding complex implementation details and showing only the essential features of an object. It allows you to focus on **what** an object does rather than **how** it does it.

### How Abstract Classes Achieve Abstraction

Abstract classes are the primary mechanism for achieving abstraction in C++:

1. **Hide Implementation Details**: Users of the abstract class don't need to know how each operation is implemented.
2. **Define Clear Interfaces**: The pure virtual functions define what operations are available.
3. **Allow Multiple Implementations**: Different derived classes can implement the same interface in different ways.

```cpp
// User only sees this interface - internal details are hidden
class PaymentProcessor {
public:
    virtual bool processPayment(double amount) = 0;
    virtual string getTransactionId() = 0;
    virtual void refund(string transactionId) = 0;
    virtual ~PaymentProcessor() {}
};

// Implementation details are hidden in derived classes
class CreditCardProcessor : public PaymentProcessor {
private:
    // Complex credit card processing logic hidden from users
    string encryptCardData(string cardNumber) {
        // Encryption implementation
        return "encrypted_data";
    }
    
    bool validateCard(string cardNumber) {
        // Validation logic
        return true;
    }
    
public:
    bool processPayment(double amount) override {
        // User doesn't need to know about encryption or validation
        cout << "Processing credit card payment: $" << amount << endl;
        return true;
    }
    
    string getTransactionId() override {
        return "CC-12345";
    }
    
    void refund(string transactionId) override {
        cout << "Refunding transaction: " << transactionId << endl;
    }
};

class PayPalProcessor : public PaymentProcessor {
private:
    // Different implementation with different internal details
    void connectToPayPalAPI() {
        // API connection logic
    }
    
public:
    bool processPayment(double amount) override {
        cout << "Processing PayPal payment: $" << amount << endl;
        return true;
    }
    
    string getTransactionId() override {
        return "PP-67890";
    }
    
    void refund(string transactionId) override {
        cout << "Refunding via PayPal: " << transactionId << endl;
    }
};

// Client code uses abstraction - doesn't care about implementation
void checkout(PaymentProcessor* processor, double amount) {
    if(processor->processPayment(amount)) {
        cout << "Transaction ID: " << processor->getTransactionId() << endl;
    }
}
```

The client code using `checkout()` doesn't need to know whether it's processing a credit card or PayPal payment - it just knows it can process payments. This is abstraction in action.

[⬆ Back to Table of Contents](#table-of-contents)

---

## Special Notes: Non-Pure Virtual Functions in Abstract Classes

An abstract class can have a mix of pure virtual functions and regular (non-pure) virtual or non-virtual functions. This is useful for providing default behavior while still enforcing implementation of critical methods.

### Example: Mixed Functions

```cpp
class Document {
protected:
    string title;
    string content;
    
public:
    Document(string t) : title(t) {}
    
    // Pure virtual - MUST be implemented
    virtual void save() = 0;
    
    // Non-pure virtual - CAN be overridden, has default implementation
    virtual void print() {
        cout << "Title: " << title << endl;
        cout << "Content: " << content << endl;
    }
    
    // Regular function - shared by all derived classes
    void setContent(string c) {
        content = c;
    }
    
    virtual ~Document() {}
};

class PDFDocument : public Document {
public:
    PDFDocument(string t) : Document(t) {}
    
    // Must implement pure virtual function
    void save() override {
        cout << "Saving as PDF file: " << title << ".pdf" << endl;
    }
    
    // Can optionally override non-pure virtual function
    void print() override {
        cout << "=== PDF Document ===" << endl;
        Document::print();  // Call base class implementation
        cout << "===================" << endl;
    }
};

class WordDocument : public Document {
public:
    WordDocument(string t) : Document(t) {}
    
    void save() override {
        cout << "Saving as Word file: " << title << ".docx" << endl;
    }
    
    // Uses default print() from Document class
};
```

### How to Invoke Non-Pure Virtual Functions?

Since you cannot instantiate an abstract class, you access non-pure virtual functions through:

#### 1. Derived Class Objects

```cpp
int main() {
    PDFDocument pdf("Report");
    pdf.setContent("This is the report content");
    pdf.print();  // Calls PDFDocument's overridden version
    pdf.save();
    
    WordDocument word("Letter");
    word.setContent("This is a letter");
    word.print();  // Calls Document's default implementation
    word.save();
    
    return 0;
}
```

#### 2. Calling Base Class Implementation from Derived Class

```cpp
class AdvancedPDFDocument : public Document {
public:
    AdvancedPDFDocument(string t) : Document(t) {}
    
    void save() override {
        cout << "Saving with advanced compression..." << endl;
    }
    
    void print() override {
        // Call the base class non-pure virtual function
        Document::print();
        cout << "Additional PDF metadata..." << endl;
    }
};
```

#### 3. Through Polymorphic Pointers/References

```cpp
void processDocument(Document* doc) {
    doc->setContent("Sample content");  // Regular function
    doc->print();                       // Non-pure virtual (uses derived or base)
    doc->save();                        // Pure virtual (must be derived)
}

int main() {
    PDFDocument pdf("Test");
    WordDocument word("Test");
    
    processDocument(&pdf);
    processDocument(&word);
    
    return 0;
}
```

### Why Use Non-Pure Virtual Functions in Abstract Classes?

#### 1. Provide Default Behavior

Not every derived class needs custom implementation of every method.

```cpp
class Vehicle {
public:
    virtual void start() = 0;  // Every vehicle starts differently
    
    virtual void honk() {       // Most vehicles honk the same way
        cout << "Beep beep!" << endl;
    }
};

class Car : public Vehicle {
public:
    void start() override {
        cout << "Turning key..." << endl;
    }
    // Uses default honk()
};

class Bicycle : public Vehicle {
public:
    void start() override {
        cout << "Start pedaling..." << endl;
    }
    
    void honk() override {
        cout << "Ring ring!" << endl;  // Bicycles need different honk
    }
};
```

#### 2. Code Reuse

Common logic can be shared while critical parts are enforced.

```cpp
class Logger {
protected:
    string timestamp() {
        return "[2025-11-12 10:30:00]";
    }
    
public:
    // Must implement - each logger writes differently
    virtual void write(string message) = 0;
    
    // Shared logic - adds timestamp automatically
    virtual void log(string message) {
        write(timestamp() + " " + message);
    }
};

class FileLogger : public Logger {
public:
    void write(string message) override {
        cout << "Writing to file: " << message << endl;
    }
};

class ConsoleLogger : public Logger {
public:
    void write(string message) override {
        cout << "Console: " << message << endl;
    }
    
    // Can override log() if needed for different behavior
};
```

#### 3. Template Method Pattern

*This design pattern will be covered in a separate section.*

## Key Takeaways

1. **Abstract classes** cannot be instantiated and must have at least one pure virtual function
2. **Pure virtual functions** are declared with `= 0` and must be implemented by derived classes
3. Abstract classes enforce a **contract** that derived classes must follow
4. They are essential for achieving **abstraction** in OOP
5. Abstract classes can have **non-pure virtual functions** for default behavior
6. Non-pure virtual functions are accessed through derived class objects or polymorphic pointers
7. Mixing pure and non-pure virtual functions provides flexibility: enforce critical implementations while sharing common code

Abstract classes are powerful tools for designing extensible, maintainable systems where you want to define clear interfaces while allowing flexibility in implementation.

[⬆ Back to Table of Contents](#table-of-contents)