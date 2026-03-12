# Implementing a Hash Set in C++

## What Is a Hash Set?

A hash set is a data structure that stores a collection of **unique values** (no duplicates allowed). You can add items, remove items, and quickly check if something is in the set.

The main advantage of a hash set is **speed**. With a regular array or list, checking if a value exists means potentially scanning every single element. That's **O(n)** time, which gets slow as the data grows. A hash set can do the same lookup in near **O(1)** (constant) time, no matter how much data you have.

## Why Use a Hash Function?

The reason for that speed is the **hash function**. Instead of searching for a value, we *calculate* where it should be stored.

A hash function takes a value and converts it into an array index. In our case, we use a simple modulo operation: `index = key % size`. So if our set has 100 buckets and we insert the value `342`, the hash function tells us `342 % 100 = 42`, so go straight to position 42. No searching needed.

This is what gives us O(1) performance. We don't scan, we don't compare, we just compute and jump.

## Why Use a Hash Set Over Other Structures?

- **Arrays** - Fast access by index, but checking if a value exists requires scanning the whole thing. No built-in duplicate prevention.
- **Sorted arrays** - You can binary search in O(log n), but inserting and removing means shifting elements around.
- **Linked lists** - Easy to insert and remove, but lookups require walking the entire list.

A hash set gives you the best of all worlds for the specific task of "does this value exist?". If you need fast membership checking, a hash set is usually the right tool.

## How It Works

The idea is simple: instead of searching through every element to find something, we use the hash function to jump straight to where it *should* be.

But what happens when two different values hash to the same index? That's called a **collision**. Our hash set handles this using **separate chaining**. Each position (called a "bucket") in the array holds a linked list. If two keys land in the same bucket, they're both stored in that bucket's list.

```
hash("apple") → bucket 3 → [ "apple" ]
hash("banana") → bucket 7 → [ "banana" ]
hash("cherry") → bucket 3 → [ "apple", "cherry" ]  ← collision, both in bucket 3
```

# Implementation

## The Class Structure

```cpp
#include <vector>
#include <list>
#include <cmath>

class HashSet {
private:
    std::vector<std::list<int>> buckets;
    int size;
    int hash(int key) { return std::abs(key) % size; }

public:
    HashSet(int s) : buckets(s), size(s) {} // Parameterised Constructor
    HashSet() : HashSet(100) {} // Default Constructor (no argument given)

    void insert(int key);
    void remove(int key);
    bool contains(int key);
};
```

- `buckets` - A vector of linked lists. Each list is one "bucket" that holds values.
- `size` - The number of buckets. More buckets = fewer collisions.
- `hash(key)` - Takes a key and returns an index using modulo (`%`). This guarantees the index is always within `[0, size - 1]`.

The **parameterised constructor** creates the vector with `s` empty lists, ready to go. The **default constructor** delegates to it with a default size of 100.

## Methods

### `insert(int key)`

Adds a key to the set. If it already exists, it does nothing (sets don't allow duplicates).

```cpp
void HashSet::insert(int key) {
    int idx = hash(key);
    for (int val : buckets[idx]) {
        if (val == key) return; // already exists, skip
    }
    buckets[idx].push_back(key); // not found, add it
}
```

**Time complexity:** O(1) average, O(n) worst case (if every key lands in the same bucket).

### `remove(int key)`

Removes a key from the set. If it doesn't exist, nothing happens.

```cpp
void HashSet::remove(int key) {
    int idx = hash(key);
    buckets[idx].remove(key);
}
```

`std::list::remove` handles searching and deleting for us.

### `contains(int key)`

Returns `true` if the key is in the set, `false` otherwise.

```cpp
bool HashSet::contains(int key) {
    int idx = hash(key);
    for (int val : buckets[idx]) {
        if (val == key) return true;
    }
    return false;
}
```

## Testing It

```cpp
#include <iostream> // Add to top of source file

int main() {
    HashSet set;

    // Insert some values
    set.insert(10);
    set.insert(20);
    set.insert(10); // duplicate, should be ignored

    // Check membership
    std::cout << set.contains(10) << std::endl; // 1 (true)
    std::cout << set.contains(20) << std::endl; // 1 (true)
    std::cout << set.contains(30) << std::endl; // 0 (false)

    // Remove and verify
    set.remove(10);
    std::cout << set.contains(10) << std::endl; // 0 (false)

    // Test collision: 5 and 105 both hash to bucket 5 (with size 100)
    set.insert(5);
    set.insert(105);
    std::cout << set.contains(5) << std::endl;   // 1 (true)
    std::cout << set.contains(105) << std::endl; // 1 (true)

    return 0;
}
```

Things worth testing: duplicates are ignored, removing a non-existent key doesn't crash, and keys that collide (same hash) are both stored correctly.

## Pros and Cons

### Pros

- **Fast** - O(1) average time for insert, remove, and lookup.
- **Simple** - Easy to understand and implement with basic data structures.
- **Handles collisions** - Separate chaining means collisions don't lose data.

### Cons

- **Wasted space** - Many buckets may sit empty, using memory for nothing.
- **No ordering** - Elements aren't stored in any sorted or insertion order.
- **Worst case is slow** - If the hash function is poor and everything lands in one bucket, performance drops to O(n).
- **Fixed size** - Our implementation doesn't resize. A real-world hash set would grow dynamically as it fills up.

## Possible Improvements

- Track element count and **resize** the bucket array when the load factor (elements / buckets) gets too high.
- Guard against `size = 0` in the constructor to avoid division by zero.
- Support types other than `int` using templates.
