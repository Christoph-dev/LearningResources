# C++ Basic Quick-Ref

> Plain C++ fundamentals.  
> Full In-Depth Tutorial: https://www.learncpp.com  
> C++ Documentation: https://devdocs.io/cpp/
> 

## Contents

- [Variables & Types](#variables--types)
- [Input & Output](#input--output)
- [If / Else](#if--else)
- [Loops](#loops)
- [Arrays & Vectors](#arrays--vectors)
- [Functions](#functions)
- [Classes](#classes)
- [Structs](#structs)
- [Pointers & References](#pointers--references)
- [Dynamic Memory](#dynamic-memory)
- [Strings](#strings)
- [File I/O](#file-io)
- [Enums](#enums)
- [Switch](#switch)
- [Common Includes](#common-includes)
- [Minimal Program Structure](#minimal-program-structure)
---

## Variables & Types

| How To | Code |
|---|---|
| Declare a variable | `int health = 100;` |
| Declare a constant | `const float PI = 3.14159f;` |
| Declare without initial value | `int score;` (uninitialized — dangerous) |
| Use auto type deduction | `auto name = "Player";` |
| Modern C++ | `int mana {100};`

### Common Types

| Type | What It Stores |
|---|---|
| `int` | Whole numbers (usually 32-bit) |
| `float` | Decimal numbers (32-bit) |
| `double` | Decimal numbers (64-bit, more precision) |
| `bool` | `true` or `false` |
| `char` | Single character |
| `std::string` | Text (needs `#include <string>`) |

---

## Input & Output

```cpp
#include <iostream>
#include <string>

// Print to console
std::cout << "Hello" << std::endl;

// Print variable
int score = 42;
std::cout << "Score: " << score << std::endl;

// Read input
std::string name;
std::cin >> name;  // stops at whitespace

// Read full line
std::getline(std::cin, name);
```

---

## If / Else

```cpp
if (health <= 0) {
    std::cout << "Dead" << std::endl;
} else if (health < 30) {
    std::cout << "Critical" << std::endl;
} else {
    std::cout << "Alive" << std::endl;
}
```
Ternary Operator (If / Else)

```cpp
// Variable = Condition ? <Value if True> : <Value if False>;
status = health <= 0 ? "Dead" : "Alive";
```


### Comparison Operators

| Operator | Meaning |
|---|---|
| `==` | Equal to |
| `!=` | Not equal to |
| `<` `>` | Less / greater than |
| `<=` `>=` | Less / greater or equal |
| `&&` | AND |
| `\|\|` | OR |
| `!` | NOT |

---

## Loops

```cpp
// For loop - when you know the start/end
for (int i = 0; i < 10; i++) {
    std::cout << i << std::endl;
}

// While loop - until a condition
while (health > 0) {
    health--;
}

// Do-while — until a condition but always runs once
do {
    std::cout << "Menu" << std::endl;
} while (choice != 0);

// iterates over a collection
for (int num : numbers) {
    std::cout << num << std::endl;
}
```

---

## Arrays & Vectors

```cpp
#include <vector>

// Fixed-size array
int scores[5] = {10, 20, 30, 40, 50};
scores[0] = 15;  // access by index

// Dynamic array (use this instead)
std::vector<int> items;
items.push_back(42);       // add to end
items.pop_back();          // remove from end
items.size();              // get count
items[0];                  // access by index
items.clear();             // remove all
```

---

## Functions

```cpp
// Basic function
void sayHello() {
    std::cout << "Hello" << std::endl;
}

// Function with parameters
int add(int a, int b) {
    return a + b;
}

// Function with default parameter
void greet(std::string name = "Player") {
    std::cout << "Hi " << name << std::endl;
}

// Pass by reference (modifies original)
void doubleIt(int& value) {
    value *= 2;
}

// Pass by const reference (read-only, no copy)
void print(const std::string& text) {
    std::cout << text << std::endl;
}
```

---

## Classes

```cpp
class Player {
private:
    int health;
    std::string name;

public:
    // Constructor
    Player(std::string n, int h) {
        name = n;
        health = h;
    }

    // Member function
    void takeDamage(int amount) {
        health -= amount;
        if (health < 0) health = 0;
    }

    // Getter
    int getHealth() const {
        return health;
    }
};

// Usage
Player p("Hero", 100);
p.takeDamage(25);
std::cout << p.getHealth() << std::endl;
```

---

## Structs

```cpp
// Use for plain data bundles
struct Vector2 {
    float x;
    float y;
};

Vector2 pos = {10.0f, 20.0f};
pos.x = 15.0f;
```

---

## Pointers & References

```cpp
int value = 42;

// Pointer — stores memory address
int* ptr = &value;   // & = "address of"
*ptr = 100;          // * = "value at address" (dereference)

// Reference — alias for existing variable
int& ref = value;
ref = 200;           // changes value directly

// Null check before using pointer
if (ptr != nullptr) {
    std::cout << *ptr << std::endl;
}
```

| Symbol | Meaning |
|---|---|
| `*` (in declaration) | "This is a pointer" |
| `*` (in expression) | "Get value at this address" |
| `&` (in declaration) | "This is a reference" |
| `&` (in expression) | "Get address of this" |

---

## Dynamic Memory

```cpp
// Allocate single object
int* num = new int(42);
delete num;

// Allocate array
int* arr = new int[10];
delete[] arr;

// Better: use smart pointers (C++11+)
#include <memory>

std::unique_ptr<int> ptr = std::make_unique<int>(42);
// automatically deleted when out of scope
```

---

## Strings

```cpp
#include <string>

std::string name = "Player";

name.length();              // get length
name += " One";             // concatenate
name.substr(0, 4);          // "Play"
name.find("ayer");          // returns index or npos
name.empty();               // true if ""

// Compare strings
if (name == "Player One") { }

// C-style string (avoid unless needed)
const char* cstr = name.c_str();
```

---

## File I/O

```cpp
#include <fstream>
#include <string>

// Write to file
std::ofstream outFile("save.txt");
outFile << "Score: 100" << std::endl;
outFile.close();

// Read from file
std::ifstream inFile("save.txt");
std::string line;
while (std::getline(inFile, line)) {
    std::cout << line << std::endl;
}
inFile.close();
```

---

## Enums

```cpp
// Old style
enum Color { RED, GREEN, BLUE };
Color c = RED;

// Modern (scoped) — prefer this
enum class Weapon { SWORD, BOW, STAFF };
Weapon w = Weapon::SWORD;

if (w == Weapon::BOW) { }
```

---

## Switch

```cpp
switch (choice) {
    case 1:
        std::cout << "New Game" << std::endl;
        break;
    case 2:
        std::cout << "Load Game" << std::endl;
        break;
    default:
        std::cout << "Invalid" << std::endl;
        break;
}
```

---

## Common Includes

| Include | What You Get |
|---|---|
| `<iostream>` | `std::cout`, `std::cin` |
| `<string>` | `std::string` |
| `<vector>` | `std::vector` |
| `<array>` | `std::array` (fixed-size) |
| `<map>` | `std::map` (key-value pairs) |
| `<fstream>` | File read/write |
| `<cmath>` | `sqrt`, `pow`, `abs`, `sin`, `cos` |
| `<algorithm>` | `sort`, `find`, `min`, `max` |
| `<memory>` | `std::unique_ptr`, `std::shared_ptr` |

---

## Minimal Program Structure

```cpp
#include <iostream>

int main() {
    std::cout << "Hello World" << std::endl;
    return 0;
}
```

---

*Standard C++ (C++11 and later)*
