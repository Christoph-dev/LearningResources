# Bouncy Ball Arena

A beginner project integrating C++ functions into a Blueprint workflow.

---

## Project Setup

### Create a Blueprint Project

<img width="405" height="518" alt="image" src="https://github.com/user-attachments/assets/12161709-74f4-411a-9dad-aa572b16f571" />

### Folder Structure

<img width="311" height="142" alt="image" src="https://github.com/user-attachments/assets/9d6e47e4-f699-4d20-b059-2de8efe02f44" />

### Create and Open a New Map

<img width="101" height="174" alt="image" src="https://github.com/user-attachments/assets/e60b4c12-a9d3-4e65-bb2e-fe90df80c0e0" />

---

## Level Blockout

### Floor

Add a Cube from Place Actors:

<img width="305" height="418" alt="image" src="https://github.com/user-attachments/assets/67a78f6b-8eb4-4332-a068-4eeb2c4f3c88" />

Set transforms:

<img width="380" height="111" alt="image" src="https://github.com/user-attachments/assets/7bfbb8e2-b9fd-47ec-b96a-6a2f5a5a3d62" />

### Floor Material

Create a material in Art folder:

<img width="847" height="559" alt="image" src="https://github.com/user-attachments/assets/4f4a007e-28ea-4743-b4df-e00cbea593f6" />

Assign to the cube.

### Player Start

<img width="341" height="484" alt="image" src="https://github.com/user-attachments/assets/7868302c-0214-42b5-b332-2e1b93518586" />

### Blocking Volumes (Walls + Ceiling)

<img width="208" height="185" alt="image" src="https://github.com/user-attachments/assets/7de8efb4-77d3-4284-8bef-f16e43171ae5" />

<img width="394" height="388" alt="image" src="https://github.com/user-attachments/assets/c1b37df7-2b8e-4a96-8cde-9c69f6007198" />

---

## Ball

### Create BP_Ball

Create an Actor Blueprint:

<img width="112" height="167" alt="image" src="https://github.com/user-attachments/assets/3ac1a8ba-48be-4e1f-8d72-290803a05f50" />

Add a Sphere component:

<img width="159" height="78" alt="image" src="https://github.com/user-attachments/assets/20f405cd-b950-4d5c-8709-21e8154f2a19" />

### Ball Material

Create in Art folder:

<img width="654" height="288" alt="image" src="https://github.com/user-attachments/assets/91076bef-b9a8-4953-8211-7402a736914d" />

Assign to ball's sphere component.

### Ball Physics

- Enable **Simulate Physics**
- Set **Mass** to 3 kg
- Drag into level and test

---

## Goal

### Goal Material

Create M_Goal in Art folder:

<img width="516" height="248" alt="image" src="https://github.com/user-attachments/assets/f75879ca-5c7f-4703-8bec-5ffdefd3c71a" />

### Create BP_Goal

Create an Actor Blueprint. Add three cubes to form a goal shape:

<img width="102" height="173" alt="image" src="https://github.com/user-attachments/assets/eceeaf19-d832-48d6-839b-486a40b3f2d4" />

**Cube1:**
<img width="368" height="103" alt="image" src="https://github.com/user-attachments/assets/4dadaab3-bab6-406a-9e9b-7f860cdacae1" />

**Cube2:**
<img width="367" height="113" alt="image" src="https://github.com/user-attachments/assets/65295351-c346-4455-8458-630d48d67922" />

**Cube3:**
<img width="369" height="110" alt="image" src="https://github.com/user-attachments/assets/d03864fa-34e2-4431-81e2-82ba907ae8c8" />

Apply material to each cube.

### Trigger Box

Add a Box Collision to detect ball overlap:

<img width="310" height="54" alt="image" src="https://github.com/user-attachments/assets/e064ab57-88a1-4285-b9c0-c3d81341f15b" />

<img width="314" height="110" alt="image" src="https://github.com/user-attachments/assets/aa14f477-d2de-4463-a569-07fc60da4240" />

---

## Manager

### Create BP_Manager

<img width="106" height="179" alt="image" src="https://github.com/user-attachments/assets/4021b281-2b2d-4317-9e4f-a99b0454a66c" />

Create the SpawnGoal Function

<img width="333" height="83" alt="image" src="https://github.com/user-attachments/assets/6393274e-2b84-4753-850c-0aed268ee51a" />

Also, goto the Event Graph of BP_Manager and add a new custom event called Goal.  
<img width="260" height="227" alt="image" src="https://github.com/user-attachments/assets/60155ba9-9b2e-4e53-92a0-4f7efdced8ab" />


### BP_Goal Setup

Before implementing the manager functions, add a Manager reference variable to BP_Goal:

<img width="305" height="70" alt="image" src="https://github.com/user-attachments/assets/0a0b5cd6-bf69-47b8-b920-5bf907ebd253" />

And then tick Instance Editable and Expose on Spawn  
<img width="205" height="207" alt="image" src="https://github.com/user-attachments/assets/a2910f55-af28-4877-8f62-bc09e8b1fb76" />

Add this to the Event Graph:

<img width="880" height="265" alt="image" src="https://github.com/user-attachments/assets/66657a28-c6bf-4bf1-9088-631a91944cbf" />


---

## C++ Function Library

We'll handle the spawn math in C++. Create a BlueprintFunctionLibrary class:

**Tools > New C++ Class**

<img width="617" height="263" alt="image" src="https://github.com/user-attachments/assets/c9fb161d-beb5-43f8-9c62-09a0bdde8984" />

Leave name as default, click **Create Class**, then **Yes** to edit.

---

### Header File

`MyBlueprintFunctionLibrary.h`:

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Kismet/BlueprintFunctionLibrary.h"
#include "MyBlueprintFunctionLibrary.generated.h"

class AActor;

UCLASS()
class MOONBALL_API UMyBlueprintFunctionLibrary : public UBlueprintFunctionLibrary
{
    GENERATED_BODY()

public:

    UFUNCTION(BlueprintCallable, Category = "My Functions")
    static void GetRandomPerimeterSpawn(
        float FloorHalfWidth,
        float GoalHalfWidth,
        FVector Center,
        FVector& OutLocation,
        FRotator& OutRotation
    );
};
```

---

### Source File

`MyBlueprintFunctionLibrary.cpp`:

```cpp
// Fill out your copyright notice in the Description page of Project Settings.


#include "MyBlueprintFunctionLibrary.h"

void UMyBlueprintFunctionLibrary::GetRandomPerimeterSpawn(
	float FloorHalfWidth, 
	float GoalHalfWidth, 
	FVector Center, 
	FVector& OutLocation, 
	FRotator& OutRotation)
{
	int32 Edge = FMath::RandRange(0, 3);
	float EdgePos = FMath::FRandRange(-1.0f, 1.0);
	float SafeRange = FloorHalfWidth - GoalHalfWidth;

	OutLocation = Center;

	if (Edge == 0)
	{
		OutLocation.X += FloorHalfWidth;
		OutLocation.Y += EdgePos * SafeRange;
		OutRotation = { 0, 90, 0 };
	}
	else if (Edge == 1)
	{
		OutLocation.X += EdgePos * SafeRange;
		OutLocation.Y += FloorHalfWidth;
		OutRotation = { 0, 0, 0 };
	}
	else if (Edge == 2)
	{
		OutLocation.X += FloorHalfWidth;
		OutLocation.Y += EdgePos * SafeRange;
		OutRotation = { 0, 90, 0 };
	}
	else
	{
		OutLocation.X += EdgePos * SafeRange;
		OutLocation.Y += FloorHalfWidth;
		OutRotation = { 0, 0, 0 };
	}
}
```

---

### Compile

Save both files, close the Unreal Editor and Rebuild the solution.

---

### Usage

Right-click in any Blueprint graph, search **"My Functions"** category.

- **GetRandomPerimeterSpawn** - outputs Location + Rotation, plug into SpawnActor
- **HandleGoalScored** - destroys the goal

---

## Quick Reference

| Macro | Purpose |
|-------|---------|
| `UFUNCTION(BlueprintCallable)` | Exposes function to BP |
| `Category = "..."` | Groups in node menu |
| `FVector&` / `FRotator&` | Output pins (see below) |

### Why `&` becomes an output pin

A reference (`&`) gives the function access to the original variable, not a copy. Blueprint allocates a temp variable, your function writes to it, Blueprint reads the result. Always write to `Out` parameters - they start as garbage.


## SpawnGoal Function

Add the Random Perimeter Spawn Node to the Goal Function, and build it like so:

<img width="880" height="378" alt="image" src="https://github.com/user-attachments/assets/10eb89bb-1f95-439f-af9b-cfa8f9e8c55f" />

Then right click the final return value and promote it to a variable, call it 'Current Goal'.

<img width="581" height="404" alt="image" src="https://github.com/user-attachments/assets/07d30548-e564-402e-94ff-685f59dc023d" />

## Goal Event

Go to the BP_Manager Event Graph and create a Custom Event called SpawnGoal

Then add the rest of these nodes.

<img width="744" height="310" alt="image" src="https://github.com/user-attachments/assets/2986cf0f-18e8-48fb-9d4f-76820c9f9be0" />

## UI

TBD

## Adding Feel

WIP  
- Increase Gravity of Player
- Increase Jump Velocity
- Increase Walk Speed
- Increase Air Controls
- Make Physical Material for Ball
- Increase Restitution for Ball Material
- Apply it to Ball

- Add Input Action - Kick Ball, Add it to IMC
- Add Trigger box to Character, put it infront of Character
- Get Overlapping Actors from trigger box
- Change Class filter to ball
- for each actor in trigger box

<img width="1275" height="349" alt="image" src="https://github.com/user-attachments/assets/f4ce4378-7d12-4d37-b5b4-fc740d4ec64a" />

## Backdrop ARt

TBD

## Menu

TBD
