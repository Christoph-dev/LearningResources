# Unreal C++ Project

## Creating the project
***Two ways to start using C++ in Unreal***

*Choose one of*
1. **Start a C++ Project**
   - Generates C++ Source Files
   - Creates C++ version of GameMode, Character etc..
   - Blueprints are still available to make

2. **Start a Blueprint Project**
   - Generates Blueprint version of Gamemode, Character etc..
   - Can Create C++ Class which:
     - Generates C++ Source Files
     - Project becomes hybrid


## Creating a C++ Actor in a Blueprint Project
*An actor is a base class for any object that can be placed into a level*
  - *Actors have a transform, component support and lifecycle events (Tick, BeginPlay)*

1. Create a C++ Class
    - `Tools` &rarr; `New C++ Class` &rarr; `Actor`
      -   *Remember actors give us the properties above*

2. Name it "MyFirstCPPClass" or whatever you like and click `Create`
   - Unreal generates Unreal C++ Source files and the project is now hybrid Blueprints/C++

3. A prompt should show `Would you like to edit the code now?`, Click `yes`.
      - *if the prompt doesnt show, simply goto `Tools` &rarr; `Open Visual Studio`*


## Writing C++ Using Visual Studio
Visual Studio is an Integrated Development Environment (IDE)  
- Which means it is an **Editor**, **Compiler**, **Debugger** and **Project Manager**. 
### Project Management
Visual Studio's outliner on the left organises the files in this format:  
```
Solution 
  └── Project(s)
        └── Files (.h, .cpp, etc.)
  ```

### Editor
Where you write code, highlights syntax to make it easier to read  
C++ has header files which contain the declarations, and source files which has the definitions.

Look at the auto-generated header and see its class and its member functions (or methods)  
```cpp
// <return type> function(<parameter\s>){<body>} 
virtual void BeginPlay() override;
```
- `virtual` Child classes can modify this function
- `void` The return type
- `override` Overrides a parents virtual function
### Compiler
Converting human code into machine code
### Debugger
A set of tools for fixing bugs, such as pausing a program and running it line by line.

## What is UE_LOG()?
Logging sends text to the output log in Unreal Engine, which can be useful in debugging and sanity checks.  
`UE_LOG(<Category>, <Verbosity ie Fatal, Error...>, <Format>, <Arguments>)`

It is a macro, which mean its shorthand for something else. During building it will be converted into a bigger code block, and UE_LOG specifically is an unreal macro, not a standard C++ one.
1. Add this code to the member function `BeginPlay()`, after `Super::...`  
        - `Super` calls the parents version of the function
```cpp
UE_LOG(LogTemp, Warning, TEXT("Hello World!"));
```

2. Close down your Unreal Editor (Not Visual Studio)
3. Right Click on your project and hit `Rebuild`
   - This performs a complete compilation, required for **structural** changes. It can be slow the first time.
   - If there are any issues, they will be displayed below in Visual Studios output.
4. Reopen your Unreal Engine Project
5. Drag your C++ Actor into the level and hit `Play`. *its best you do this on a new map*
6. Check the log to see if the logging works.

## Building vs Livecoding
It can be quicker to use Unreal's Livecode functionality, but it can be temperamental. If you have made a small change, you can use it, otherwise it is better to close down the Unreal Editor and use Build.  

Change the UE_LOG text to something else, and use the livecode function with the hotkey `CTRL+SHIFT+F11`

Then test the game again, without needing to close anything.

Always build in the end.
