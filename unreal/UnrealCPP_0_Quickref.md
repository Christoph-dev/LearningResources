# Unreal Engine C++ - Quick Reference

> Unreal Engine C++ Quickref

---

## C++ Fundamentals

| Term | What It Is |
|---|---|
| **Pointer** (`T*`) | Stores the memory address of an object. Can be `nullptr`. Always null-check. |
| **Reference** (`T&`) | Alias for an existing variable. Can't be null, can't be reassigned. |
| **`const`** | Marks something as read-only. `const FString&` = "pass by reference, don't modify." |
| **Class** | Bundles data + behavior. Members are `private` by default. |
| **Struct** | Same as a class but members are `public` by default. Used for plain data. |
| **Inheritance** | Child class acquires parent's members/functions. Unreal's hierarchy is deep. |
| **Virtual Function** | A function the child class can override. Declared with `virtual`, overridden with `override`. |
| **`override`** | Tells the compiler you're intentionally overriding a parent function. Always use it. |
| **Access Specifiers** | `public` = anyone, `protected` = class + children, `private` = class only. |
| **Template** (`<T>`) | Generic code that works with any type. Why you write `TArray<int32>`. |
| **Smart Pointer** | Pointer wrapper that manages lifetime. Unreal has its own (`TSharedPtr`, `TWeakPtr`) — don't use `std::` versions. |
| **Lambda** (`[](){}`) | Anonymous inline function. Used in `Sort`, `FindByPredicate`, timers, delegate binding. |
| **Casting** | Type conversion. Unreal uses `Cast<T>()` for UObjects — returns `nullptr` on failure. |
| **Enum Class** | Named set of constants. Scoped: `EWeaponType::Rifle`. |
| **Header / Source** | `.h` declares, `.cpp` defines. Unreal requires this split strictly. |

---

## Unreal Macros & Reflection

| Macro | Purpose |
|---|---|
| **`UCLASS()`** | Registers a class with the engine. Required on all UObject-derived classes. |
| **`GENERATED_BODY()`** | Placeholder replaced by UHT-generated code. Must be first in the class body. |
| **`UPROPERTY()`** | Registers a variable. Enables GC tracking, editor exposure, Blueprint access, replication. **Any `UObject*` without this can dangle.** |
| **`UFUNCTION()`** | Registers a function. Enables Blueprint access and RPCs. |
| **`USTRUCT()`** | Registers a struct. Use for data bundles that need Blueprint/serialization support. |
| **`UENUM()`** | Registers an enum. Must use `uint8` for Blueprint compatibility. |

### Common `UPROPERTY` Specifiers

| Specifier | Meaning |
|---|---|
| `EditAnywhere` | Editable on defaults and instances |
| `EditDefaultsOnly` | Editable on Blueprint defaults only |
| `VisibleAnywhere` | Shown in editor, not editable |
| `BlueprintReadWrite` | Read + write in Blueprints |
| `BlueprintReadOnly` | Read only in Blueprints |
| `Category = "..."` | Groups in the Details panel |
| `Replicated` | Synced server → clients |
| `Transient` | Not saved to disk |

### Common `UFUNCTION` Specifiers

| Specifier | Meaning |
|---|---|
| `BlueprintCallable` | Callable from Blueprint graphs |
| `BlueprintPure` | No side effects, shows as value node |
| `BlueprintImplementableEvent` | Defined in Blueprint only (no C++ body) |
| `BlueprintNativeEvent` | C++ default, overridable in Blueprint |
| `Server` / `Client` / `NetMulticast` | RPC direction |
| `Reliable` / `Unreliable` | RPC delivery guarantee |

---

## Unreal Types

| Type | Replaces | Notes |
|---|---|---|
| `FString` | `std::string` | Mutable string. Use `*MyString` to get `TCHAR*` for logging. |
| `FName` | — | Immutable identifier, fast comparison (integer-based). Case-insensitive. |
| `FText` | — | Localization-aware. Use for anything the player sees. |
| `TArray<T>` | `std::vector` | Dynamic array. The container you'll use most. |
| `TMap<K,V>` | `std::unordered_map` | Hash map. `Find()` returns pointer, null if missing. |
| `TSet<T>` | `std::unordered_set` | Unique unordered collection. |
| `TSubclassOf<T>` | — | Reference to a *class* (not an instance). For spawning later. |
| `TSoftObjectPtr<T>` | — | Lazy asset reference. Not loaded until you request it. |
| `TWeakObjectPtr<T>` | `std::weak_ptr` | Weak reference to a UObject. Auto-nulls if target is destroyed. |

---

## Naming Conventions

| Prefix | Means | Examples |
|---|---|---|
| `A` | Actor | `AActor`, `ACharacter`, `APlayerController` |
| `U` | UObject (non-Actor) | `UActorComponent`, `UUserWidget` |
| `F` | Struct / non-UObject | `FVector`, `FString`, `FHitResult` |
| `E` | Enum | `ECollisionChannel` |
| `I` | Interface | `IInteractable` |
| `T` | Template | `TArray`, `TMap` |

---

## Object Lifecycle

| Stage | Use For | Don't Do |
|---|---|---|
| **Constructor** | Set defaults, `CreateDefaultSubobject` | Access world, other actors, gameplay logic |
| **`BeginPlay()`** | Gameplay init, find actors, bind delegates | Forget `Super::BeginPlay()` |
| **`Tick(DeltaTime)`** | Per-frame logic. Multiply by `DeltaTime`. | Forget to disable when unused (`bCanEverTick = false`) |
| **`EndPlay()`** | Cleanup, unbind delegates | — |

### Creating & Destroying Objects

| What | How |
|---|---|
| Create a UObject | `NewObject<T>(Outer)` |
| Create an Actor | `GetWorld()->SpawnActor<T>(Class, Location, Rotation)` |
| Destroy an Actor | `Actor->Destroy()` or `Actor->SetLifeSpan(Seconds)` |
| **Never** | Use raw `new` / `delete` on UObjects |

---

## Game Framework

```
UGameInstance              — persists across levels, global state
  └─ UWorld
       ├─ AGameMode       — server-only rules (spawning, scoring)
       │    └─ AGameState  — replicated game state (scores, phase)
       └─ APlayerController — player's "brain", receives input
            ├─ APlayerState — replicated per-player data
            └─ APawn / ACharacter — the physical entity
                 └─ UComponents — modular functionality
```

**Key rules:** GameMode is server-only. GameInstance survives level loads. PlayerController possesses Pawns.

---

## Delegates (Event System)

```cpp
// Declare
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnHealthChanged, float, NewHealth);

// Expose
UPROPERTY(BlueprintAssignable)
FOnHealthChanged OnHealthChanged;

// Broadcast
OnHealthChanged.Broadcast(CurrentHealth);

// Bind (listener must be a UFUNCTION)
HealthComp->OnHealthChanged.AddDynamic(this, &AMyHUD::HandleHealthChanged);
```

---

## Common Patterns

```cpp
// Logging
UE_LOG(LogTemp, Warning, TEXT("Health: %f, Name: %s"), Health, *Name);

// Safe casting
if (AMyCharacter* Char = Cast<AMyCharacter>(SomeActor)) { /* use Char */ }

// Line trace
FHitResult Hit;
FCollisionQueryParams Params;
Params.AddIgnoredActor(this);
bool bHit = GetWorld()->LineTraceSingleByChannel(
    Hit, Start, End, ECC_Visibility, Params);

// Timer
GetWorldTimerManager().SetTimer(
    Handle, this, &AMyActor::OnTimerFired, 2.0f, false);

// Spawning
GetWorld()->SpawnActor<AProjectile>(ProjectileClass, Location, Rotation);
```

---

## Build System

| File | Purpose |
|---|---|
| `*.Build.cs` | Module config — dependencies, plugins, linked libraries |
| `*.Target.cs` | Build target config (editor vs game vs server) |
| `*.generated.h` | Auto-generated by UHT. Include it last. Never edit. |
| **UHT** (Header Tool) | Parses your headers, generates reflection code |
| **UBT** (Build Tool) | Manages compilation, replaces CMake/MSBuild |

---

*UE 5.x · Updated Feb 2026*
