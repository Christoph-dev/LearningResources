# C++ Speedrun 
*A confidence-building crash course, with Unreal Engine context*

---

## Contents

0. [Your First Program](#0-your-first-program)
1. [Variables & Types](#1-variables--types)
2. [Basic Math](#2-basic-math)
3. [Conditions](#3-conditions)
4. [Loops](#4-loops)
5. [Arrays & Vectors](#5-arrays--vectors)
6. [Functions](#6-functions)
7. [References & Pointers](#7-references--pointers)
8. [Classes](#8-classes)
9. [Public vs Private](#9-public-vs-private)
10. [Inheritance](#10-inheritance)
11. [Structs](#11-structs)
12. [Header Files](#12-header-files-organizing-code)
13. [Quick Reference](#13-quick-reference)
14. [Extra Practice](#want-more-practice)

---

## 0. Your First Program

```cpp
#include <iostream>

int main()
{
    std::cout << "Hello, Game Dev!" << std::endl;
    return 0;
}
```

**What's happening:**
- `#include <iostream>` - imports input/output tools
- `int main()` - every program starts here
- `std::cout` - prints to console
- `return 0` - tells the OS "all good!"

###  Practice

1. Change the message to print your game studio name
2. Make the program print three lines: your name, your favorite game, and why you're learning C++

---

## 1. Variables & Types

```cpp
int health = 100;           // whole numbers
float speed = 5.5f;         // decimals (the 'f' matters!)
double preciseValue = 3.14159265359;  // more precision
bool isAlive = true;        // true or false
char grade = 'A';           // single character
std::string playerName = "Hero";  // text (needs #include <string>)
```

**UE Context:** Unreal uses similar types: `int32`, `float`, `bool`, `FString`

###  Practice

1. Create a character stat sheet with variables for: `playerName`, `health`, `mana`, `stamina`, `level`, and `isPlayerOne`
2. Create variables to describe a weapon: its name, damage, fire rate, and whether it's automatic
3. Print all your variables using `std::cout`

---

## 2. Basic Math

```cpp
int damage = 25;
int health = 100;

health = health - damage;   // health is now 75
health -= damage;           // same thing, shorter

int double_damage = damage * 2;
int half_damage = damage / 2;
int remainder = damage % 10;  // modulo: gives 5
```

For square roots and exponents, include `<cmath>`:
```cpp
#include <cmath>

float distance = sqrt(100.0f);       // square root: 10
float squared = pow(5.0f, 2.0f);     // 5^2 = 25
float cubed = pow(2.0f, 3.0f);       // 2^3 = 8
```

###  Practice

1. A player has 150 HP. They get hit 4 times for 35 damage each. Calculate and print their remaining health
2. Create a shop scenario: player has 500 gold, buys 3 health potions at 45 gold each. Print gold remaining
3. A critical hit does 2.5x damage. Base damage is 40. Calculate and print the crit damage
4. Calculate the hypotenuse of a right triangle with sides 3 and 4 using `sqrt` and `pow`

---

## 3. Conditions

```cpp
int health = 25;

if (health <= 0)
{
    std::cout << "Game Over" << std::endl;
}
else if (health < 30)
{
    std::cout << "Low Health!" << std::endl;
}
else
{
    std::cout << "You're doing fine" << std::endl;
}
```

**Comparison operators:** `==` `!=` `<` `>` `<=` `>=`

**Logical operators:** `&&` (and), `||` (or), `!` (not)

```cpp
bool hasKey = true;
bool doorIsLocked = true;

if (hasKey && doorIsLocked)
{
    std::cout << "You unlocked the door!" << std::endl;
}
```

###  Practice

1. Write a level-up check: if `experience >= 100`, print "Level Up!" and set experience back to 0
2. Create an attack validator: player can only attack if `isAlive == true` AND `stamina > 10` AND `weaponEquipped == true`
3. Write a loot rarity checker: print "Legendary!" if `rarity >= 90`, "Rare" if `>= 60`, "Common" otherwise

---

## 4. Loops

### While Loop
```cpp
int countdown = 3;

while (countdown > 0)
{
    std::cout << countdown << "..." << std::endl;
    countdown--;
}
std::cout << "GO!" << std::endl;
```

### For Loop
```cpp
// Spawn 5 enemies
for (int i = 0; i < 5; i++)
{
    std::cout << "Spawning enemy " << i << std::endl;
}
```

###  Practice

1. Print a respawn countdown: "Respawning in 5... 4... 3... 2... 1... You're back!"
2. Simulate collecting 10 coins in a loop, adding 25 gold per coin. Print the total after
3. Create a wave spawner that prints "Wave 1: 5 enemies", "Wave 2: 10 enemies", up to Wave 5 (enemies increase by 5 each wave)
4. Using a while loop, simulate a player taking 15 damage per turn until health reaches 0. Print health each turn

---

## 5. Arrays & Vectors

### C-Style Arrays (the old way)
```cpp
int scores[5] = {100, 95, 80, 70, 60};  // fixed size of 5

scores[0] = 105;  // access by index (starts at 0)
std::cout << scores[2];  // prints 80
```

The problem: size is fixed at compile time, can't grow or shrink, easy to go out of bounds.

### std::vector (use this)
```cpp
#include <vector>

std::vector<int> scores = {100, 95, 80, 70, 60};

scores.push_back(55);           // add to end - now has 6 elements
scores.pop_back();              // remove from end
int size = scores.size();       // get count
scores[0] = 105;                // access by index
```

Looping through a vector:
```cpp
std::vector<std::string> inventory = {"Sword", "Shield", "Potion"};

for (int i = 0; i < inventory.size(); i++)
{
    std::cout << inventory[i] << std::endl;
}
```

**When to use which?**
- **C-style array** - almost never in modern C++
- **std::vector** - almost always. It grows, shrinks, and tracks its own size.

**UE Context:** Unreal uses `TArray<T>` which works just like `std::vector`

###  Practice

1. Create a vector of enemy names, add 3 enemies, then print them all with a loop
2. Create a vector of `int` for player scores. Add 5 scores, then find and print the highest one
3. Create an inventory system: start with 3 items, add one, remove one, print what's left
4. Create a vector of health values for a party of 4 players, then loop through and print anyone below 50 HP

---

## 6. Functions

```cpp
// Declaration
int CalculateDamage(int baseDamage, float multiplier)
{
    return baseDamage * multiplier;
}

// Usage
int main()
{
    int finalDamage = CalculateDamage(50, 1.5f);
    std::cout << "Damage dealt: " << finalDamage << std::endl;
    return 0;
}
```

**Void functions** don't return anything:
```cpp
void PlaySound(std::string soundName)
{
    std::cout << "Playing: " << soundName << std::endl;
}
```

**UE Context:** Unreal functions often use `UFUNCTION()` macro, but the structure is identical.

###  Practice

1. Write `int Heal(int currentHealth, int healAmount, int maxHealth)` that returns new health without exceeding max
2. Write `float CalculateDistance(float x1, float y1, float x2, float y2)` using the distance formula: $\sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}$
3. Write `void PrintPlayerStats(std::string name, int health, int level)` that displays a formatted stat block
4. Write `bool CanAfford(int gold, int itemPrice)` that returns true or false

---

## 7. References & Pointers

### References (use these most of the time)
```cpp
void TakeDamage(int& health, int damage)  // & means reference
{
    health -= damage;  // modifies the original!
}

int main()
{
    int playerHealth = 100;
    TakeDamage(playerHealth, 25);
    // playerHealth is now 75
}
```

### Pointers (for when you need them)
```cpp
int health = 100;
int* healthPtr = &health;  // pointer stores address

std::cout << *healthPtr << std::endl;  // prints 100 (dereferencing)
*healthPtr = 75;  // health is now 75
```

**UE Context:** Unreal uses pointers constantly: `AActor*`, `UObject*`. You'll see `->` a lot.

###  Practice

1. Write `void AddExperience(int& currentXP, int amount)` that modifies XP directly. Test it
2. Write `void SwapWeapons(std::string& primary, std::string& secondary)` that swaps two weapon names
3. Create an `int score` variable, then a pointer to it. Use the pointer to add 100 to the score and print the result
4. Write `void ResetStats(int& health, int& mana, int& stamina)` that sets all three to 100

---

## 8. Classes

```cpp
class Player
{
public:  // accessible from outside
    std::string Name;
    int Health;
    int MaxHealth;

    // Constructor
    Player(std::string name, int maxHealth)
    {
        Name = name;
        MaxHealth = maxHealth;
        Health = maxHealth;
    }

    void TakeDamage(int damage)
    {
        Health -= damage;
        if (Health < 0) Health = 0;
        std::cout << Name << " takes " << damage << " damage!" << std::endl;
    }

    bool IsAlive()
    {
        return Health > 0;
    }
};
```

**Using it:**
```cpp
int main()
{
    Player hero("Knight", 100);
    hero.TakeDamage(30);
    
    if (hero.IsAlive())
    {
        std::cout << hero.Name << " has " << hero.Health << " HP" << std::endl;
    }
    
    return 0;
}
```

**UE Context:** Everything in Unreal is classes. `ACharacter`, `APawn`, `AActor` - all classes you'll inherit from.

###  Practice

1. Add a `void Heal(int amount)` method to the Player class that doesn't exceed MaxHealth
2. Create a `Weapon` class with: `Name`, `Damage`, `FireRate`, and a method `void Fire()` that prints "Firing [weapon name]!"
3. Create an `Inventory` class with: a `Gold` variable, `void AddGold(int amount)`, `bool SpendGold(int amount)` (returns false if not enough)
4. Create a `Vector3` class with `float X, Y, Z` and a method `void Print()` that displays the coordinates

---

## 9. Public vs Private

By default, keep things simple with `public`. But as your classes grow, you'll want to protect some data.

- **Public** - anyone can access it from outside the class
- **Private** - only the class itself can access it

```cpp
class Player
{
public:
    std::string Name;
    
    void TakeDamage(int amount)
    {
        health -= amount;
        if (health < 0) health = 0;
    }
    
    // Getter - lets outsiders READ the value
    int GetHealth()
    {
        return health;
    }
    
    // Setter - lets outsiders WRITE the value (with protection)
    void SetHealth(int value)
    {
        if (value < 0) value = 0;
        if (value > maxHealth) value = maxHealth;
        health = value;
    }

private:
    int health = 100;
    int maxHealth = 100;
};
```

```cpp
int main()
{
    Player hero;
    hero.Name = "Knight";        // OK - Name is public
    hero.TakeDamage(30);         // OK - TakeDamage is public
    
    // hero.health = -999;       // ERROR! health is private
    
    hero.SetHealth(50);          // OK - uses the setter
    std::cout << hero.GetHealth();  // OK - uses the getter
}
```

**Why bother?** Private prevents bugs. No one can accidentally set `health = -999` and break your game.

**UE Context:** You'll see this everywhere - `GetActorLocation()`, `SetActorRotation()`, etc.

###  Practice

1. Create a `BankAccount` class with private `balance`. Add `Deposit(int amount)`, `Withdraw(int amount)`, and `GetBalance()`
2. Create an `Enemy` class with private `health` and `maxHealth`. Add a setter that clamps health between 0 and max
3. Create a `Weapon` class with private `ammo`. Add `GetAmmo()`, `Reload(int amount)`, and `Fire()` that decreases ammo

---

## 10. Inheritance

```cpp
class Enemy
{
public:
    int Health = 50;
    
    virtual void Attack()  // 'virtual' allows overriding
    {
        std::cout << "Enemy attacks!" << std::endl;
    }
};

class Zombie : public Enemy  // Zombie inherits from Enemy
{
public:
    void Attack() override  // override the parent's version
    {
        std::cout << "Zombie bites!" << std::endl;
    }
};

class Skeleton : public Enemy
{
public:
    void Attack() override
    {
        std::cout << "Skeleton slashes!" << std::endl;
    }
};
```

**UE Context:** You'll write `class AMyCharacter : public ACharacter` constantly.

###  Practice

1. Create a `Boss` class inheriting from `Enemy` with 500 health and an `Attack()` that prints something intimidating
2. Create a `Vehicle` base class, then `Car` and `Motorcycle` that inherit from it. Give each a unique `void Drive()` method
3. Create a `Pickup` base class with `void OnCollect()`. Create `HealthPickup`, `AmmoPickup`, and `GoldPickup` that override it
4. Create a `Character` base class, then `Warrior`, `Mage`, and `Archer` classes. Each should have unique `void UseAbility()` implementations

---

## 11. Structs

A struct is basically a class where everything is `public` by default. Use them for simple data bundles.

```cpp
struct Vector3
{
    float X = 0.0f;
    float Y = 0.0f;
    float Z = 0.0f;
};

struct PlayerStats
{
    int Strength = 10;
    int Agility = 10;
    int Intelligence = 10;
};
```

```cpp
int main()
{
    Vector3 position;
    position.X = 100.0f;
    position.Y = 50.0f;
    
    // Or initialize directly
    Vector3 velocity = {5.0f, 0.0f, -2.0f};
    
    PlayerStats warrior = {15, 8, 5};
}
```

Structs can have methods too:
```cpp
struct Vector3
{
    float X, Y, Z;
    
    void Print()
    {
        std::cout << X << ", " << Y << ", " << Z << std::endl;
    }
};
```

**When to use which?**
- **Struct** - simple data, everything public, no complex logic (coordinates, colors, stats)
- **Class** - has behavior, needs private data, more complex (player, enemy, weapon)

**UE Context:** Unreal structs start with `F` - `FVector`, `FRotator`, `FColor`, `FHitResult`

###  Practice

1. Create a `Color` struct with `int R, G, B, A` (0-255 each)
2. Create a `Transform` struct containing a `Vector3 Position`, `Vector3 Rotation`, and `Vector3 Scale`
3. Create a `DamageInfo` struct with: `float Damage`, `std::string DamageType`, `bool IsCritical`
4. Add a `void Print()` method to your `Color` struct

---

## 12. Header Files (Organizing Code)

**Player.h** - declarations
```cpp
#pragma once

#include <string>

class Player
{
public:
    std::string Name;
    int Health;
    
    Player(std::string name, int health);
    void TakeDamage(int damage);
};
```

**Player.cpp** - definitions
```cpp
#include "Player.h"
#include <iostream>

Player::Player(std::string name, int health)
{
    Name = name;
    Health = health;
}

void Player::TakeDamage(int damage)
{
    Health -= damage;
    std::cout << Name << " takes " << damage << " damage!" << std::endl;
}
```

**UE Context:** Unreal follows this pattern religiously. `.h` and `.cpp` pairs everywhere.

###  Practice

1. Split your `Weapon` class from Module 7 into `Weapon.h` and `Weapon.cpp`
2. Create `Enemy.h` and `Enemy.cpp` files for a complete Enemy class with `Name`, `Health`, `TakeDamage()`, and `IsAlive()`
3. Create a mini-project with separate files: `Player.h/.cpp`, `Enemy.h/.cpp`, and a `main.cpp` that uses both

---

## 13. Quick Reference

| Concept | Plain C++ | Unreal Equivalent |
|---------|-----------|-------------------|
| String | `std::string` | `FString` |
| Array | `std::vector` | `TArray` |
| Print | `std::cout` | `UE_LOG` |
| Integer | `int` | `int32` |
| Struct | `struct Vector3` | `FVector` |
| Pointer check | `if (ptr != nullptr)` | `if (IsValid(ptr))` |

---

##  Want more Practice?

Create a simple text-based combat system:

1. Create an `Entity` base class with `Name`, `Health`, `AttackPower`
2. Create `Hero` and `Monster` classes that inherit from `Entity`
3. Add an `Attack(Entity& target)` method
4. Make them fight until one dies
5. Print the winner

**Bonus challenges:**
- Add a `Defend()` method that reduces incoming damage for one turn
- Add multiple monsters the hero must defeat in sequence
- Add a `Heal()` ability the hero can use every 3 turns

---
