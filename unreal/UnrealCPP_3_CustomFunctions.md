# Unreal C++ Blueprint Nodes How To's

A reference guide for creating custom Blueprint nodes using C++.

---

## Contents

- [Unreal C++ Blueprint Nodes How To's](#unreal-c-blueprint-nodes-how-tos)
  - [Contents](#contents)
  - [Add C++ to a Blueprint Project](#add-c-to-a-blueprint-project)
  - [Create a Blueprint Function Library](#create-a-blueprint-function-library)
  - [Create a Basic Blueprint Node](#create-a-basic-blueprint-node)
  - [Create a Pure Node (No Exec Pins)](#create-a-pure-node-no-exec-pins)
  - [Add a Tooltip to Your Node](#add-a-tooltip-to-your-node)
  - [Organize Nodes into Categories](#organize-nodes-into-categories)
  - [Change the Display Name of a Node](#change-the-display-name-of-a-node)
  - [Create a Node with Multiple Inputs](#create-a-node-with-multiple-inputs)
  - [Create a Node with Multiple Outputs](#create-a-node-with-multiple-outputs)
  - [Create a Node with an Array Input](#create-a-node-with-an-array-input)
  - [Create a Node with an Array Output](#create-a-node-with-an-array-output)
  - [Create a Node with an Enum Input](#create-a-node-with-an-enum-input)
  - [Create a Node with an Object Reference Input](#create-a-node-with-an-object-reference-input)
  - [Create a Node with a Class Input](#create-a-node-with-a-class-input)
  - [Create a Node with Default Parameter Values](#create-a-node-with-default-parameter-values)
  - [Create a Compact Node](#create-a-compact-node)
  - [Create a Node That Works with Any Actor](#create-a-node-that-works-with-any-actor)
  - [Create a Latent Node (Async/Delay)](#create-a-latent-node-asyncdelay)
  - [Create a Node with Exec Output Pins](#create-a-node-with-exec-output-pins)
  - [Hide a Pin from the Node](#hide-a-pin-from-the-node)

---

## Add C++ to a Blueprint Project

If your project is Blueprint-only, you need to add C++ support first.

1. In the Unreal Editor, go to **Tools > New C++ Class**

2. Select **None (Empty Class)** or any parent class, then click **Next**

3. Name your class anything (e.g., `MyFirstClass`) and click **Create Class**

4. Unreal will generate the C++ files and open Visual Studio (or your IDE)

5. Wait for the project to compile. Your project now supports C++!

**Tip:** After adding C++, you can find your source files in your project folder under `Source/YourProjectName/`.

---

## Create a Blueprint Function Library

A **Blueprint Function Library** is a collection of static functions that appear as nodes in any Blueprint. This is the easiest way to add custom nodes.

1. In the Unreal Editor, go to **Tools > New C++ Class**

2. Check **Show All Classes**, search for `BlueprintFunctionLibrary`, select it, and click **Next**

3. Name it something descriptive (e.g., `MyBlueprintLibrary`) and click **Create Class**

4. Unreal creates two files:
   - `MyBlueprintLibrary.h` (header file - declare functions here)
   - `MyBlueprintLibrary.cpp` (source file - implement functions here)

Your header file will look like this:

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Kismet/BlueprintFunctionLibrary.h"
#include "MyBlueprintLibrary.generated.h"

UCLASS()
class YOURPROJECT_API UMyBlueprintLibrary : public UBlueprintFunctionLibrary
{
    GENERATED_BODY()
    
public:
    // Your functions go here
};
```

---

## Create a Basic Blueprint Node

This creates a simple node with an exec (flow) pin that you can call from Blueprint.

1. Open your Blueprint Function Library header file (`.h`) and add a function declaration:

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions")
static void PrintHelloWorld();
```

2. Open the source file (`.cpp`) and implement the function:

```cpp
void UMyBlueprintLibrary::PrintHelloWorld()
{
    UE_LOG(LogTemp, Warning, TEXT("Hello World!"));
}
```

3. Compile your project (Build > Build Solution in Visual Studio, or Ctrl+Alt+F11 in Unreal)

4. In any Blueprint, right-click and search for "Print Hello World" to find your node

**Note:** The `static` keyword is required for Blueprint Function Library functions.

---

## Create a Pure Node (No Exec Pins)

Pure nodes have no exec (white) pins—they just return a value. Use these for calculations and getters.

1. Open your header file (`.h`) and add:

```cpp
UFUNCTION(BlueprintPure, Category = "My Functions")
static float AddTwoNumbers(float A, float B);
```

2. Open the source file (`.cpp`) and implement:

```cpp
float UMyBlueprintLibrary::AddTwoNumbers(float A, float B)
{
    return A + B;
}
```

**When to use Pure vs Callable:**
- `BlueprintPure` = No side effects, just returns a value (math, getters, checks)
- `BlueprintCallable` = Does something (spawns actors, plays sounds, modifies state)

---

## Add a Tooltip to Your Node

Tooltips appear when you hover over a node in Blueprint. Add helpful descriptions for your users.

```cpp
// Tooltip for the entire function
UFUNCTION(BlueprintPure, Category = "My Functions", meta = (ToolTip = "Adds two numbers together and returns the result"))
static float AddNumbers(float A, float B);
```

You can also add tooltips to individual parameters:

```cpp
// Separate comment style (above the UFUNCTION)
/**
 * Calculates the damage after applying armor reduction.
 * @param BaseDamage The raw damage before armor
 * @param ArmorValue The target's armor value (0-100)
 * @return The final damage to apply
 */
UFUNCTION(BlueprintPure, Category = "Combat")
static float CalculateDamage(float BaseDamage, float ArmorValue);
```

---

## Organize Nodes into Categories

Categories control where your node appears in the right-click menu.

```cpp
// Simple category
UFUNCTION(BlueprintCallable, Category = "My Functions")
static void MyFunction();

// Nested category (creates submenus)
UFUNCTION(BlueprintCallable, Category = "My Functions|Combat|Damage")
static void ApplyDamage();

// Another nested example
UFUNCTION(BlueprintCallable, Category = "My Functions|Utilities|Math")
static float MyMathFunction();
```

In the Blueprint right-click menu, this creates: `My Functions > Combat > Damage > Apply Damage`

---

## Change the Display Name of a Node

By default, nodes use the function name with spaces added. You can customize this.

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions", meta = (DisplayName = "Do Something Cool"))
static void InternalFunctionName();
```

The node will appear as **"Do Something Cool"** in Blueprint, even though the C++ function is named `InternalFunctionName`.

---

## Create a Node with Multiple Inputs

Just add more parameters to your function.

1. Open your header file (`.h`):

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions")
static void SpawnEnemyAtLocation(FVector Location, FRotator Rotation, int32 EnemyLevel, bool bIsElite);
```

2. Open the source file (`.cpp`):

```cpp
void UMyBlueprintLibrary::SpawnEnemyAtLocation(FVector Location, FRotator Rotation, int32 EnemyLevel, bool bIsElite)
{
    // Your implementation here
    UE_LOG(LogTemp, Warning, TEXT("Spawning enemy at level %d, Elite: %s"), EnemyLevel, bIsElite ? TEXT("Yes") : TEXT("No"));
}
```

Each parameter becomes an input pin on the node.

---

## Create a Node with Multiple Outputs

Use reference parameters or a struct to return multiple values.

**Method 1: Reference parameters (adds output pins)**

1. Open your header file (`.h`):

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions")
static void GetPlayerStats(float& Health, float& MaxHealth, int32& Level);
```

2. Open the source file (`.cpp`):

```cpp
void UMyBlueprintLibrary::GetPlayerStats(float& Health, float& MaxHealth, int32& Level)
{
    Health = 75.f;
    MaxHealth = 100.f;
    Level = 5;
}
```

The `&` makes these **output** pins instead of input pins.

**Method 2: Return value (single output)**

```cpp
// Header
UFUNCTION(BlueprintPure, Category = "My Functions")
static float GetPlayerHealth();

// Source
float UMyBlueprintLibrary::GetPlayerHealth()
{
    return 75.f;
}
```

---

## Create a Node with an Array Input

1. Open your header file (`.h`):

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions")
static float GetAverageOfNumbers(const TArray<float>& Numbers);
```

2. Open the source file (`.cpp`):

```cpp
float UMyBlueprintLibrary::GetAverageOfNumbers(const TArray<float>& Numbers)
{
    if (Numbers.Num() == 0)
    {
        return 0.f;
    }
    
    float Sum = 0.f;
    for (float Num : Numbers)
    {
        Sum += Num;
    }
    
    return Sum / Numbers.Num();
}
```

**Note:** Use `const TArray<>&` for input arrays (pass by reference, can't modify). This creates an array input pin.

---

## Create a Node with an Array Output

1. Open your header file (`.h`):

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions")
static void GetFirstThreeMultiples(int32 Number, TArray<int32>& Multiples);
```

2. Open the source file (`.cpp`):

```cpp
void UMyBlueprintLibrary::GetFirstThreeMultiples(int32 Number, TArray<int32>& Multiples)
{
    Multiples.Empty(); // Clear any existing elements
    Multiples.Add(Number * 1);
    Multiples.Add(Number * 2);
    Multiples.Add(Number * 3);
}
```

**Note:** `TArray<>&` without `const` becomes an **output** pin.

---

## Create a Node with an Enum Input

First, you need a `UENUM` (or use an existing one).

1. Define the enum in your header file (`.h`) **above** the class:

```cpp
UENUM(BlueprintType)
enum class EDifficulty : uint8
{
    Easy,
    Medium,
    Hard
};
```

2. Use it as a parameter:

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions")
static float GetDifficultyMultiplier(EDifficulty Difficulty);
```

3. Implement in the source file (`.cpp`):

```cpp
float UMyBlueprintLibrary::GetDifficultyMultiplier(EDifficulty Difficulty)
{
    switch (Difficulty)
    {
        case EDifficulty::Easy:   return 0.5f;
        case EDifficulty::Medium: return 1.0f;
        case EDifficulty::Hard:   return 2.0f;
        default:                  return 1.0f;
    }
}
```

The node will have a dropdown for Easy/Medium/Hard.

---

## Create a Node with an Object Reference Input

This lets you pass in a reference to an actor or other UObject.

1. Open your header file (`.h`):

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions")
static void HealActor(AActor* TargetActor, float HealAmount);
```

2. Open the source file (`.cpp`):

```cpp
void UMyBlueprintLibrary::HealActor(AActor* TargetActor, float HealAmount)
{
    if (!TargetActor)
    {
        UE_LOG(LogTemp, Warning, TEXT("HealActor: No target provided!"));
        return;
    }
    
    // Your healing logic here
    UE_LOG(LogTemp, Warning, TEXT("Healing %s for %f"), *TargetActor->GetName(), HealAmount);
}
```

**Important:** Always check for `nullptr` when using object pointers!

---

## Create a Node with a Class Input

This lets users select a class (like a Blueprint class) from a dropdown.

1. Open your header file (`.h`):

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions")
static void SpawnEnemy(TSubclassOf<AActor> EnemyClass, FVector Location);
```

2. Open the source file (`.cpp`):

```cpp
void UMyBlueprintLibrary::SpawnEnemy(TSubclassOf<AActor> EnemyClass, FVector Location)
{
    if (!EnemyClass)
    {
        UE_LOG(LogTemp, Warning, TEXT("SpawnEnemy: No class provided!"));
        return;
    }
    
    // Note: You need a world context to spawn - see "Create a Node That Works with Any Actor"
    UE_LOG(LogTemp, Warning, TEXT("Would spawn %s at %s"), *EnemyClass->GetName(), *Location.ToString());
}
```

`TSubclassOf<AActor>` creates a pin that only accepts Actor classes or subclasses.

---

## Create a Node with Default Parameter Values

This lets pins have pre-filled values that users can optionally change.

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions", meta = (AdvancedDisplay = "2"))
static void SpawnParticle(FVector Location, float Scale = 1.0f, bool bAutoDestroy = true);
```

**Explanation:**
- `Scale = 1.0f` gives it a default value of 1.0
- `bAutoDestroy = true` gives it a default value of true
- `AdvancedDisplay = "2"` hides pins starting at index 2 under an "Advanced" dropdown (optional)

In the source file (`.cpp`):

```cpp
void UMyBlueprintLibrary::SpawnParticle(FVector Location, float Scale, bool bAutoDestroy)
{
    // Implementation - use the values as normal
}
```

---

## Create a Compact Node

Compact nodes are smaller and show only the symbol/title. Great for math operations.

```cpp
UFUNCTION(BlueprintPure, Category = "My Functions", meta = (CompactNodeTitle = "×2"))
static float DoubleValue(float Value);
```

This creates a small node that just shows "×2" instead of the full function name.

```cpp
// More examples
UFUNCTION(BlueprintPure, meta = (CompactNodeTitle = "±"))
static float PlusMinus(float Value, float Range);

UFUNCTION(BlueprintPure, meta = (CompactNodeTitle = "%%"))
static float PercentOf(float Value, float Percent);
```

---

## Create a Node That Works with Any Actor

Static functions don't have access to the world. Use `WorldContextObject` to get world access.

1. Open your header file (`.h`):

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions", meta = (WorldContext = "WorldContextObject"))
static void SpawnExplosion(UObject* WorldContextObject, FVector Location, float Radius);
```

2. Open the source file (`.cpp`):

```cpp
void UMyBlueprintLibrary::SpawnExplosion(UObject* WorldContextObject, FVector Location, float Radius)
{
    if (!WorldContextObject)
    {
        return;
    }
    
    UWorld* World = WorldContextObject->GetWorld();
    if (!World)
    {
        return;
    }
    
    // Now you can use World to spawn actors, play sounds, etc.
    // Example: Draw debug sphere
    DrawDebugSphere(World, Location, Radius, 12, FColor::Red, false, 2.f);
}
```

**Note:** The `WorldContext` meta tag automatically hides the `WorldContextObject` pin in Blueprint—it gets filled in automatically.

---

## Create a Latent Node (Async/Delay)

Latent nodes pause execution and resume later (like the built-in Delay node).

1. Open your header file (`.h`):

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions", meta = (Latent, LatentInfo = "LatentInfo", WorldContext = "WorldContextObject"))
static void DelayedPrint(UObject* WorldContextObject, float Duration, FString Message, FLatentActionInfo LatentInfo);
```

2. Open the source file (`.cpp`). First add the include at the top:

```cpp
#include "Engine/World.h"
#include "Engine/LatentActionManager.h"
```

3. Implement the function:

```cpp
void UMyBlueprintLibrary::DelayedPrint(UObject* WorldContextObject, float Duration, FString Message, FLatentActionInfo LatentInfo)
{
    if (UWorld* World = GEngine->GetWorldFromContextObject(WorldContextObject, EGetWorldErrorMode::LogAndReturnNull))
    {
        FLatentActionManager& LatentManager = World->GetLatentActionManager();
        
        // You'll need to create a custom FPendingLatentAction class for complex behavior
        // For simple delays, use FDelayAction:
        LatentManager.AddNewAction(LatentInfo.CallbackTarget, LatentInfo.UUID, 
            new FDelayAction(Duration, LatentInfo));
    }
}
```

**Note:** Latent actions are more advanced. For simple delays, consider using timers instead (see the main How To's document).

---

## Create a Node with Exec Output Pins

This creates a node with multiple execution paths (like a Branch node).

1. Open your header file (`.h`):

```cpp
UFUNCTION(BlueprintCallable, Category = "My Functions", meta = (ExpandEnumAsExecs = "Result"))
static void CheckHealth(float Health, EHealthStatus& Result);
```

2. Define the enum above your class:

```cpp
UENUM(BlueprintType)
enum class EHealthStatus : uint8
{
    Healthy,
    Wounded,
    Critical,
    Dead
};
```

3. Implement in the source file (`.cpp`):

```cpp
void UMyBlueprintLibrary::CheckHealth(float Health, EHealthStatus& Result)
{
    if (Health <= 0)
    {
        Result = EHealthStatus::Dead;
    }
    else if (Health < 25)
    {
        Result = EHealthStatus::Critical;
    }
    else if (Health < 75)
    {
        Result = EHealthStatus::Wounded;
    }
    else
    {
        Result = EHealthStatus::Healthy;
    }
}
```

The node will have 4 output exec pins: Healthy, Wounded, Critical, and Dead.

---

## Hide a Pin from the Node

Sometimes you want a parameter in C++ but don't want it visible in Blueprint.

```cpp
// Hide the pin and give it a default value
UFUNCTION(BlueprintCallable, Category = "My Functions", meta = (HidePin = "DebugMode", DefaultToSelf = "Target"))
static void DoSomething(AActor* Target, bool DebugMode = false);
```

**Common meta specifiers for hiding/auto-filling pins:**

- `HidePin = "ParamName"` - Hides the pin completely
- `DefaultToSelf = "ParamName"` - Auto-fills with "self" reference
- `WorldContext = "ParamName"` - Auto-fills with world context (hidden)
- `HideSelfPin` - Hides the self/target pin on non-static functions

---
