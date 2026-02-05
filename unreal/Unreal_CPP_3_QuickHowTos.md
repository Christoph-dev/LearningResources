# Unreal C++ How To's

A beginner-friendly quick reference guide for common Unreal Engine C++ tasks.

---

## Contents

- [Unreal C++ How To's](#unreal-c-how-tos)
  - [Contents](#contents)
  - [Rotate an Actor Over Time](#rotate-an-actor-over-time)
  - [Move an Actor Over Time](#move-an-actor-over-time)
  - [Print Debug Messages to Screen](#print-debug-messages-to-screen)
  - [Print to Output Log](#print-to-output-log)
  - [Expose a Variable to the Editor](#expose-a-variable-to-the-editor)
  - [Expose a Variable to Blueprint](#expose-a-variable-to-blueprint)
  - [Expose a Function to Blueprint](#expose-a-function-to-blueprint)
  - [Spawn an Actor](#spawn-an-actor)
  - [Destroy an Actor](#destroy-an-actor)
  - [Get the Player Character](#get-the-player-character)
  - [Get the Player Controller](#get-the-player-controller)
  - [Find an Actor by Class](#find-an-actor-by-class)
  - [Cast to a Specific Class](#cast-to-a-specific-class)
  - [Add a Component in C++](#add-a-component-in-c)
  - [Detect Overlap Events](#detect-overlap-events)
  - [Create a Timer](#create-a-timer)
  - [Stop a Timer](#stop-a-timer)
  - [Play a Sound](#play-a-sound)
  - [Do a Line Trace (Raycast)](#do-a-line-trace-raycast)
  - [Get Distance Between Two Actors](#get-distance-between-two-actors)
  - [Smoothly Interpolate a Value (Lerp)](#smoothly-interpolate-a-value-lerp)
  - [Get a Random Number](#get-a-random-number)
  - [Clamp a Value](#clamp-a-value)
  - [Delay Before Running Code (Latent)](#delay-before-running-code-latent)
  - [Apply Damage to an Actor](#apply-damage-to-an-actor)
  - [Get Actor Forward/Right/Up Vectors](#get-actor-forwardrightup-vectors)
  - [Set Actor Location](#set-actor-location)
  - [Get Input Axis Values](#get-input-axis-values)
  - [Check if Key is Pressed](#check-if-key-is-pressed)
  - [Loop Through a TArray](#loop-through-a-tarray)
  - [Add/Remove from TArray](#addremove-from-tarray)
  - [Use a TMap (Dictionary)](#use-a-tmap-dictionary)
  - [Convert Between Types](#convert-between-types)
  - [Check if Running in Editor vs Game](#check-if-running-in-editor-vs-game)
  - [Get World Delta Seconds Outside of Tick](#get-world-delta-seconds-outside-of-tick)
  - [Spawn Particles/Effects](#spawn-particleseffects)
  - [Spawn Niagara Effects (UE5)](#spawn-niagara-effects-ue5)
  - [Load Level / Map](#load-level--map)
  - [Quit Game](#quit-game)

---

## Rotate an Actor Over Time

Your C++ class has two files: a **header file** (`MyClass.h`) for declarations and a **source file** (`MyClass.cpp`) for implementation.

1. Open your header file (`.h`) and add this member variable inside your class:
```cpp
FRotator RotationRate;
```

2. Open your source file (`.cpp`) and find the **constructor** (the function with the same name as your class). This runs once when the actor is created. Add:
```cpp
AMyClass::AMyClass()
{
    PrimaryActorTick.bCanEverTick = true; // Required for Tick to work
    RotationRate = FRotator(0, 90, 0);    // Pitch, Yaw, Roll (degrees per second)
}
```

3. Find the **Tick** function in your `.cpp` file. This runs every frame. Add:
```cpp
void AMyClass::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);
    AddActorLocalRotation(RotationRate * DeltaTime); // Multiply by DeltaTime for smooth movement
}
```

---

## Move an Actor Over Time

Your C++ class has two files: a **header file** (`MyClass.h`) for declarations and a **source file** (`MyClass.cpp`) for implementation.

1. Open your header file (`.h`) and add this member variable inside your class:
```cpp
FVector Velocity;
```

2. Open your source file (`.cpp`) and find the **constructor** (the function with the same name as your class). This runs once when the actor is created. Add:
```cpp
AMyClass::AMyClass()
{
    PrimaryActorTick.bCanEverTick = true; // Required for Tick to work
    Velocity = FVector(100, 0, 0);        // X, Y, Z (units per second)
}
```

3. Find the **Tick** function in your `.cpp` file. This runs every frame. Add:
```cpp
void AMyClass::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);
    AddActorLocalOffset(Velocity * DeltaTime); // Multiply by DeltaTime for smooth movement
}
```

---

## Print Debug Messages to Screen

This displays text on-screen during gameplay—useful for debugging.

Open your source file (`.cpp`) and add this code inside any function (like `BeginPlay` or `Tick`):

```cpp
// Simple message (displays for 5 seconds in green)
GEngine->AddOnScreenDebugMessage(-1, 5.f, FColor::Green, TEXT("Hello!"));

// With a variable
int32 Score = 100;
GEngine->AddOnScreenDebugMessage(-1, 5.f, FColor::Yellow, FString::Printf(TEXT("Score: %d"), Score));
```

**Parameters explained:**
- `-1` = Don't overwrite previous messages (use a unique number to overwrite a specific message)
- `5.f` = Display duration in seconds
- `FColor::Green` = Text color
- `TEXT("...")` = The message

---

## Print to Output Log

This prints to the Output Log panel in the Unreal Editor—useful for debugging without cluttering the screen.

Open your source file (`.cpp`) and add this code inside any function:

```cpp
UE_LOG(LogTemp, Display, TEXT("Normal message"));
UE_LOG(LogTemp, Warning, TEXT("Warning message"));  // Yellow in output log
UE_LOG(LogTemp, Error, TEXT("Error message"));      // Red in output log

// With variables
float Health = 75.5f;
UE_LOG(LogTemp, Display, TEXT("Health: %f"), Health);

int32 Score = 100;
UE_LOG(LogTemp, Display, TEXT("Score: %d"), Score);

FString Name = "Player1";
UE_LOG(LogTemp, Display, TEXT("Name: %s"), *Name);  // Note the * before FString
```

---

## Expose a Variable to the Editor

This makes a variable editable in the Unreal Editor's Details panel when you select your actor.

Open your header file (`.h`) and add `UPROPERTY` above your variable:

```cpp
// Editable in editor
UPROPERTY(EditAnywhere, Category = "My Settings")
float Speed = 100.f;

// Visible but not editable (read-only)
UPROPERTY(VisibleAnywhere, Category = "My Settings")
int32 CurrentScore;
```

**Common UPROPERTY options:**
- `EditAnywhere` = Editable on the class default AND on each instance in the level
- `EditDefaultsOnly` = Only editable on the class default (Blueprint), not per-instance
- `EditInstanceOnly` = Only editable on instances placed in the level
- `VisibleAnywhere` = Visible but read-only

---

## Expose a Variable to Blueprint

This lets Blueprint graphs read and/or write your C++ variable.

Open your header file (`.h`) and add `UPROPERTY` above your variable:

```cpp
// Blueprint can read and write
UPROPERTY(BlueprintReadWrite, Category = "My Settings")
float Health = 100.f;

// Blueprint can only read (not modify)
UPROPERTY(BlueprintReadOnly, Category = "My Settings")
int32 Ammo = 30;
```

**Tip:** You can combine with editor access:
```cpp
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "My Settings")
float Speed = 100.f;
```

---

## Expose a Function to Blueprint

This lets you call a C++ function from Blueprint graphs.

1. Open your header file (`.h`) and declare the function with `UFUNCTION`:
```cpp
// Regular function - can have side effects
UFUNCTION(BlueprintCallable, Category = "My Functions")
void TakeDamage(float Amount);

// Pure function - no side effects, just returns a value (no exec pins in Blueprint)
UFUNCTION(BlueprintPure, Category = "My Functions")
float GetHealthPercent();
```

2. Open your source file (`.cpp`) and implement the functions:
```cpp
void AMyClass::TakeDamage(float Amount)
{
    Health -= Amount;
}

float AMyClass::GetHealthPercent()
{
    return Health / MaxHealth;
}
```

---

## Spawn an Actor

This creates a new actor in the world at runtime.

1. Open your header file (`.h`) and add a variable to hold what class to spawn:
```cpp
UPROPERTY(EditAnywhere, Category = "Spawning")
TSubclassOf<AActor> ActorToSpawn;
```

2. Open your source file (`.cpp`) and spawn the actor (inside any function):
```cpp
void AMyClass::SpawnSomething()
{
    if (ActorToSpawn) // Always check if it's set
    {
        FVector Location = GetActorLocation() + FVector(200, 0, 0); // 200 units in front
        FRotator Rotation = FRotator::ZeroRotator;
        
        GetWorld()->SpawnActor<AActor>(ActorToSpawn, Location, Rotation);
    }
}
```

3. In the Editor, select your actor and set the **Actor To Spawn** property in the Details panel.

---

## Destroy an Actor

Open your source file (`.cpp`) and use one of these (inside any function):

```cpp
// Destroy this actor immediately
Destroy();

// Destroy another actor (if you have a reference to it)
OtherActor->Destroy();

// Destroy this actor after a delay (3 seconds)
SetLifeSpan(3.f);
```

---

## Get the Player Character

This retrieves a reference to the player's character (usually a Pawn or Character class).

1. Open your source file (`.cpp`) and add this include at the **top of the file** (with the other includes):
```cpp
#include "Kismet/GameplayStatics.h"
```

2. Use this code inside any function:
```cpp
ACharacter* Player = UGameplayStatics::GetPlayerCharacter(GetWorld(), 0);

if (Player) // Always check if valid
{
    FVector PlayerLocation = Player->GetActorLocation();
}
```

**Note:** The `0` is the player index (0 = first player, for local multiplayer use 1, 2, etc.)

---

## Get the Player Controller

This retrieves the player's controller, which handles input and camera.

1. Open your source file (`.cpp`) and add this include at the **top of the file**:
```cpp
#include "Kismet/GameplayStatics.h"
```

2. Use this code inside any function:
```cpp
APlayerController* PC = UGameplayStatics::GetPlayerController(GetWorld(), 0);

if (PC) // Always check if valid
{
    // Example: Get mouse position
    float MouseX, MouseY;
    PC->GetMousePosition(MouseX, MouseY);
}
```

---

## Find an Actor by Class

This finds actors of a specific class that exist in the world.

1. Open your source file (`.cpp`) and add this include at the **top of the file**:
```cpp
#include "Kismet/GameplayStatics.h"
```

2. Use this code inside any function:
```cpp
// Find the first actor of a class
AActor* Found = UGameplayStatics::GetActorOfClass(GetWorld(), AMyTargetClass::StaticClass());

if (Found)
{
    // Do something with it
}

// Find ALL actors of a class
TArray<AActor*> FoundActors;
UGameplayStatics::GetAllActorsOfClass(GetWorld(), AMyTargetClass::StaticClass(), FoundActors);

for (AActor* Actor : FoundActors)
{
    // Do something with each one
}
```

---

## Cast to a Specific Class

Casting converts a generic pointer (like `AActor*`) to a specific class (like `AEnemy*`) so you can access that class's functions and variables.

Open your source file (`.cpp`) and use this pattern:

```cpp
void AMyClass::HandleOverlap(AActor* OtherActor)
{
    // Try to cast OtherActor to AEnemy
    AEnemy* Enemy = Cast<AEnemy>(OtherActor);
    
    if (Enemy) // Cast returns nullptr if it fails, so always check!
    {
        Enemy->TakeDamage(10.f); // Now we can call AEnemy functions
    }
}
```

**Important:** Always check if the cast succeeded before using the result!

---

## Add a Component in C++

Components are things like meshes, collision boxes, audio, etc. that you attach to an actor.

1. Open your header file (`.h`) and add a pointer to hold the component:
```cpp
UPROPERTY(VisibleAnywhere)
UStaticMeshComponent* MeshComp;
```

2. Open your source file (`.cpp`) and create the component in the **constructor**:
```cpp
AMyClass::AMyClass()
{
    // Create the component
    MeshComp = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("MeshComp"));
    
    // Make it the root component (the base that other components attach to)
    RootComponent = MeshComp;
}
```

**Common component types:**
- `UStaticMeshComponent` - 3D mesh
- `USkeletalMeshComponent` - Animated mesh
- `USphereComponent` / `UBoxComponent` / `UCapsuleComponent` - Collision shapes
- `UAudioComponent` - Sound
- `UPointLightComponent` - Light

---

## Detect Overlap Events

This triggers code when another actor overlaps with your actor's collision.

1. Open your header file (`.h`) and add the collision component and callback function:
```cpp
UPROPERTY(VisibleAnywhere)
USphereComponent* CollisionComp;

// This function will be called when an overlap happens
UFUNCTION()
void OnOverlapBegin(UPrimitiveComponent* OverlappedComp, AActor* OtherActor, 
    UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, 
    bool bFromSweep, const FHitResult& SweepResult);
```

2. Open your source file (`.cpp`) and add the include at the **top of the file**:
```cpp
#include "Components/SphereComponent.h"
```

3. In the **constructor**, create the component and bind the event:
```cpp
AMyClass::AMyClass()
{
    CollisionComp = CreateDefaultSubobject<USphereComponent>(TEXT("CollisionComp"));
    CollisionComp->SetSphereRadius(100.f);
    RootComponent = CollisionComp;
    
    // Bind the overlap event to our function
    CollisionComp->OnComponentBeginOverlap.AddDynamic(this, &AMyClass::OnOverlapBegin);
}
```

4. Implement the callback function:
```cpp
void AMyClass::OnOverlapBegin(UPrimitiveComponent* OverlappedComp, AActor* OtherActor, 
    UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, 
    bool bFromSweep, const FHitResult& SweepResult)
{
    UE_LOG(LogTemp, Warning, TEXT("Overlapped with: %s"), *OtherActor->GetName());
}
```

**Note:** Make sure your collision component has "Generate Overlap Events" enabled in the Editor.

---

## Create a Timer

Timers let you run code after a delay or repeatedly at an interval.

1. Open your header file (`.h`) and add the timer handle and callback function:
```cpp
FTimerHandle MyTimerHandle;

void MyTimerFunction();
```

2. Open your source file (`.cpp`) and start the timer. The **BeginPlay** function runs once when the game starts:
```cpp
void AMyClass::BeginPlay()
{
    Super::BeginPlay();
    
    // SetTimer(Handle, Object, Function, Time, bLoop)
    GetWorldTimerManager().SetTimer(
        MyTimerHandle,                    // Timer handle
        this,                             // Object with the function
        &AMyClass::MyTimerFunction,       // Function to call
        2.f,                              // Time in seconds
        true                              // true = loop, false = one-shot
    );
}
```

3. Implement the callback function:
```cpp
void AMyClass::MyTimerFunction()
{
    UE_LOG(LogTemp, Warning, TEXT("Timer fired!"));
}
```

---

## Stop a Timer

To stop a running timer, you need the `FTimerHandle` that was used to create it (see [Create a Timer](#create-a-timer)).

Open your source file (`.cpp`) and use:

```cpp
GetWorldTimerManager().ClearTimer(MyTimerHandle);
```

---

## Play a Sound

1. Open your header file (`.h`) and add a variable to hold the sound asset:
```cpp
UPROPERTY(EditAnywhere, Category = "Audio")
USoundBase* MySound;
```

2. Open your source file (`.cpp`) and add this include at the **top of the file**:
```cpp
#include "Kismet/GameplayStatics.h"
```

3. Play the sound inside any function:
```cpp
if (MySound) // Always check if it's set
{
    // 2D sound (same volume everywhere - good for UI, music)
    UGameplayStatics::PlaySound2D(GetWorld(), MySound);
    
    // 3D sound at a location (volume decreases with distance)
    UGameplayStatics::PlaySoundAtLocation(GetWorld(), MySound, GetActorLocation());
}
```

4. In the Editor, select your actor and set the **My Sound** property in the Details panel.

---

## Do a Line Trace (Raycast)

A line trace shoots an invisible line and tells you what it hits—useful for shooting, visibility checks, etc.

Open your source file (`.cpp`) and use this inside any function:

```cpp
FHitResult HitResult;
FVector Start = GetActorLocation();
FVector End = Start + GetActorForwardVector() * 1000.f; // 1000 units forward

// Trace against the Visibility channel
bool bHit = GetWorld()->LineTraceSingleByChannel(
    HitResult,      // Results stored here
    Start,          // Start point
    End,            // End point
    ECC_Visibility  // Collision channel
);

if (bHit)
{
    AActor* HitActor = HitResult.GetActor();
    FVector HitLocation = HitResult.Location;
    
    UE_LOG(LogTemp, Warning, TEXT("Hit: %s at %s"), *HitActor->GetName(), *HitLocation.ToString());
}
```

**Common collision channels:**
- `ECC_Visibility` - Visibility traces
- `ECC_Camera` - Camera collision
- `ECC_Pawn` - Pawns/Characters
- `ECC_WorldStatic` - Static world geometry
- `ECC_WorldDynamic` - Dynamic objects

---

## Get Distance Between Two Actors

Open your source file (`.cpp`) and use this inside any function:

```cpp
float Distance = FVector::Dist(GetActorLocation(), OtherActor->GetActorLocation());

UE_LOG(LogTemp, Warning, TEXT("Distance: %f"), Distance);

// Example: Check if within range
if (Distance < 500.f)
{
    // Within 500 units
}
```

---

## Smoothly Interpolate a Value (Lerp)

Interpolation smoothly transitions a value from its current state toward a target—great for smooth movement, UI animations, etc.

1. Open your header file (`.h`) and add member variables:
```cpp
float CurrentValue;
float TargetValue;
```

2. Open your source file (`.cpp`) and interpolate in the **Tick** function (runs every frame):
```cpp
void AMyClass::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);
    
    // FInterpTo smoothly moves CurrentValue toward TargetValue
    // The last parameter (5.f) is the speed
    CurrentValue = FMath::FInterpTo(CurrentValue, TargetValue, DeltaTime, 5.f);
}
```

**Other interpolation functions:**
```cpp
// For FVector (positions)
CurrentLocation = FMath::VInterpTo(CurrentLocation, TargetLocation, DeltaTime, 5.f);

// For FRotator (rotations)
CurrentRotation = FMath::RInterpTo(CurrentRotation, TargetRotation, DeltaTime, 5.f);

// For FLinearColor (colors)
CurrentColor = FMath::CInterpTo(CurrentColor, TargetColor, DeltaTime, 5.f);
```

---

## Get a Random Number

Open your source file (`.cpp`) and use these inside any function:

```cpp
// Random integer between 1 and 100 (inclusive)
int32 RandomInt = FMath::RandRange(1, 100);

// Random float between 0.0 and 1.0
float RandomFloat = FMath::FRandRange(0.f, 1.f);

// Random float between -50.0 and 50.0
float RandomInRange = FMath::FRandRange(-50.f, 50.f);

// Random boolean (50% chance true)
bool bRandomBool = FMath::RandBool();

// Random point in a sphere
FVector RandomPoint = FMath::VRand() * 100.f; // Random direction * radius
```

---

## Clamp a Value

Clamping restricts a value to stay within a minimum and maximum range.

Open your source file (`.cpp`) and use:

```cpp
// Keep Health between 0 and 100
Health = FMath::Clamp(Health, 0.f, 100.f);

// Works with integers too
int32 Ammo = FMath::Clamp(Ammo, 0, 30);
```

---

## Delay Before Running Code (Latent)

This runs code once after a delay (unlike a looping timer).

1. Open your header file (`.h`) and add the timer handle and callback function:
```cpp
FTimerHandle DelayHandle;

void DelayedFunction();
```

2. Open your source file (`.cpp`) and start the timer with `false` for the loop parameter:
```cpp
void AMyClass::SomeFunction()
{
    // Run DelayedFunction once after 3 seconds
    GetWorldTimerManager().SetTimer(
        DelayHandle,
        this,
        &AMyClass::DelayedFunction,
        3.f,
        false  // false = don't loop (one-shot)
    );
}
```

3. Implement the callback:
```cpp
void AMyClass::DelayedFunction()
{
    UE_LOG(LogTemp, Warning, TEXT("This ran after 3 seconds!"));
}
```

---

## Apply Damage to an Actor

Unreal has a built-in damage system that works with the `TakeDamage` function.

**To deal damage:**

1. Open your source file (`.cpp`) and add this include at the **top of the file**:
```cpp
#include "Kismet/GameplayStatics.h"
```

2. Apply damage inside any function:
```cpp
UGameplayStatics::ApplyDamage(
    TargetActor,                // Actor to damage
    25.f,                       // Damage amount
    GetInstigatorController(),  // Who caused it (can be nullptr)
    this,                       // What caused it (this actor)
    nullptr                     // Damage type (can be nullptr)
);
```

**To receive damage:**

1. Open your header file (`.h`) and declare the override:
```cpp
virtual float TakeDamage(float DamageAmount, struct FDamageEvent const& DamageEvent, 
    class AController* EventInstigator, AActor* DamageCauser) override;
```

2. Open your source file (`.cpp`) and implement it:
```cpp
float AMyClass::TakeDamage(float DamageAmount, FDamageEvent const& DamageEvent, 
    AController* EventInstigator, AActor* DamageCauser)
{
    Health -= DamageAmount;
    
    UE_LOG(LogTemp, Warning, TEXT("Took %f damage! Health: %f"), DamageAmount, Health);
    
    if (Health <= 0)
    {
        Destroy();
    }
    
    return DamageAmount;
}
```

---

## Get Actor Forward/Right/Up Vectors

These return direction vectors based on your actor's rotation—useful for movement and spawning.

Open your source file (`.cpp`) and use inside any function:

```cpp
FVector Forward = GetActorForwardVector(); // Direction actor is facing (X axis)
FVector Right = GetActorRightVector();     // Direction to actor's right (Y axis)
FVector Up = GetActorUpVector();           // Direction above actor (Z axis)

// Example: Get a point 200 units in front of the actor
FVector InFront = GetActorLocation() + Forward * 200.f;
```

---

## Set Actor Location

Open your source file (`.cpp`) and use inside any function:

```cpp
// Teleport to an exact location
SetActorLocation(FVector(0, 0, 100));

// Teleport with collision checking (will stop if something is in the way)
SetActorLocation(FVector(0, 0, 100), true);

// Move relative to current position
FVector NewLocation = GetActorLocation() + FVector(100, 0, 0);
SetActorLocation(NewLocation);
```

---

## Get Input Axis Values

This binds keyboard/controller input to a function. You need to set up Input Mappings in Project Settings > Input first.

1. Open your header file (`.h`) and declare the function:
```cpp
void MoveForward(float Value);
```

2. Open your source file (`.cpp`) and bind the input in `SetupPlayerInputComponent`, then implement your function:
```cpp
void AMyClass::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
    Super::SetupPlayerInputComponent(PlayerInputComponent);
    
    // "MoveForward" must match the Axis Mapping name in Project Settings > Input
    PlayerInputComponent->BindAxis("MoveForward", this, &AMyClass::MoveForward);
}

void AMyClass::MoveForward(float Value)
{
    // Value is -1 to 1 (e.g., -1 for S key, +1 for W key)
    if (Value != 0.f)
    {
        AddMovementInput(GetActorForwardVector(), Value);
    }
}
```

---

## Check if Key is Pressed

Open your source file (`.cpp`) and use inside any function:

```cpp
APlayerController* PC = Cast<APlayerController>(GetController());

if (PC && PC->IsInputKeyCurrentlyPressed(EKeys::SpaceBar))
{
    // Space bar is currently held down
}
```

**Common key names:** `EKeys::SpaceBar`, `EKeys::LeftMouseButton`, `EKeys::W`, `EKeys::Escape`, `EKeys::LeftShift`

---

## Loop Through a TArray

`TArray` is Unreal's dynamic array (like `std::vector` in standard C++).

1. Open your header file (`.h`) and declare the array:
```cpp
TArray<AActor*> MyActors;
```

2. Open your source file (`.cpp`) and loop through it:
```cpp
// Range-based for loop (preferred)
for (AActor* Actor : MyActors)
{
    UE_LOG(LogTemp, Warning, TEXT("Actor: %s"), *Actor->GetName());
}

// Index-based loop (when you need the index)
for (int32 i = 0; i < MyActors.Num(); i++)
{
    UE_LOG(LogTemp, Warning, TEXT("Actor %d: %s"), i, *MyActors[i]->GetName());
}
```

---

## Add/Remove from TArray

`TArray` is Unreal's dynamic array (like `std::vector` in standard C++).

1. Open your header file (`.h`) and declare the array:
```cpp
TArray<int32> Numbers;
```

2. Open your source file (`.cpp`) and modify it:
```cpp
Numbers.Add(5);           // Add to end: [5]
Numbers.Add(10);          // Add to end: [5, 10]
Numbers.Insert(7, 1);     // Insert at index 1: [5, 7, 10]

Numbers.Remove(7);        // Remove first occurrence of 7: [5, 10]
Numbers.RemoveAt(0);      // Remove at index 0: [10]

Numbers.Empty();          // Remove all elements: []

// Check if contains a value
if (Numbers.Contains(5))
{
    // 5 is in the array
}

// Get the number of elements
int32 Count = Numbers.Num();
```

---

## Use a TMap (Dictionary)

`TMap` stores key-value pairs (like `std::map` or `Dictionary` in other languages).

1. Open your header file (`.h`) and declare the map:
```cpp
TMap<FString, int32> PlayerScores; // Key: player name, Value: score
```

2. Open your source file (`.cpp`) and use it:
```cpp
// Add entries
PlayerScores.Add("Alice", 100);
PlayerScores.Add("Bob", 250);

// Access a value by key
int32 AliceScore = PlayerScores["Alice"];

// Check if a key exists before accessing
if (PlayerScores.Contains("Alice"))
{
    int32 Score = PlayerScores["Alice"];
}

// Update a value
PlayerScores["Alice"] = 150;

// Remove an entry
PlayerScores.Remove("Bob");

// Loop through all entries
for (auto& Pair : PlayerScores)
{
    UE_LOG(LogTemp, Warning, TEXT("%s: %d"), *Pair.Key, Pair.Value);
}
```

---

## Convert Between Types

Open your source file (`.cpp`) and use these conversions:

```cpp
// FString to int32
FString StringNum = "123";
int32 IntNum = FCString::Atoi(*StringNum);

// int32 to FString
int32 Number = 456;
FString NumString = FString::FromInt(Number);

// float to FString
float FloatNum = 3.14f;
FString FloatString = FString::SanitizeFloat(FloatNum);

// FName to FString
FName MyName = "SomeName";
FString NameString = MyName.ToString();

// FString to FName
FString MyString = "SomeName";
FName NewName = FName(*MyString);

// FString to FText (for UI)
FText MyText = FText::FromString(MyString);
```

---

## Check if Running in Editor vs Game

Open your source file (`.cpp`) and use:

```cpp
// Compile-time check (code is removed in packaged builds)
#if WITH_EDITOR
    UE_LOG(LogTemp, Warning, TEXT("This only exists in editor builds"));
#endif

// Runtime check
if (GIsEditor)
{
    // Currently running in the editor (PIE or editor itself)
}

// Check if playing in editor vs standalone game
if (GetWorld()->IsPlayInEditor())
{
    // Playing in editor (PIE)
}
```

---

## Get World Delta Seconds Outside of Tick

The **Tick** function gives you `DeltaTime` as a parameter, but sometimes you need it elsewhere.

Open your source file (`.cpp`) and use inside any function:

```cpp
float DeltaTime = GetWorld()->GetDeltaSeconds();
```

---

## Spawn Particles/Effects

This spawns a legacy Cascade particle system (UE4 style).

1. Open your header file (`.h`) and add a variable to hold the particle asset:
```cpp
UPROPERTY(EditAnywhere, Category = "Effects")
UParticleSystem* MyParticleSystem;
```

2. Open your source file (`.cpp`) and add this include at the **top of the file**:
```cpp
#include "Kismet/GameplayStatics.h"
```

3. Spawn the particles inside any function:
```cpp
if (MyParticleSystem)
{
    // Spawn at a location
    UGameplayStatics::SpawnEmitterAtLocation(GetWorld(), MyParticleSystem, GetActorLocation());
    
    // Spawn attached to a component (follows it)
    UGameplayStatics::SpawnEmitterAttached(MyParticleSystem, MeshComp);
}
```

4. In the Editor, select your actor and set the **My Particle System** property in the Details panel.

---

## Spawn Niagara Effects (UE5)

Niagara is the modern particle system in UE5 (replacement for Cascade).

1. Open your header file (`.h`) and add a variable to hold the Niagara asset:
```cpp
UPROPERTY(EditAnywhere, Category = "Effects")
UNiagaraSystem* MyNiagaraSystem;
```

2. Open your source file (`.cpp`) and add this include at the **top of the file**:
```cpp
#include "NiagaraFunctionLibrary.h"
```

3. Spawn the effect inside any function:
```cpp
if (MyNiagaraSystem)
{
    UNiagaraFunctionLibrary::SpawnSystemAtLocation(GetWorld(), MyNiagaraSystem, GetActorLocation());
}
```

4. In the Editor, select your actor and set the **My Niagara System** property in the Details panel.

**Note:** You may need to add "Niagara" to your module's dependencies in your `.Build.cs` file.

---

## Load Level / Map

1. Open your source file (`.cpp`) and add this include at the **top of the file**:
```cpp
#include "Kismet/GameplayStatics.h"
```

2. Load the level inside any function:
```cpp
// Load by map name (without path or extension)
UGameplayStatics::OpenLevel(GetWorld(), "MyMapName");

// Load with a full path
UGameplayStatics::OpenLevel(GetWorld(), "/Game/Maps/MyMapName");
```

---

## Quit Game

1. Open your source file (`.cpp`) and add this include at the **top of the file**:
```cpp
#include "Kismet/KismetSystemLibrary.h"
```

2. Quit the game inside any function:
```cpp
UKismetSystemLibrary::QuitGame(
    GetWorld(),
    nullptr,                    // Player controller (can be nullptr)
    EQuitPreference::Quit,      // Quit (vs Background on mobile)
    false                       // Ignore platform restrictions
);
```

---
