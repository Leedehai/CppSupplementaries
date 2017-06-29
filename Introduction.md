# Introduction

Chapter 1: Getting Started with C++

Chapter 2: Setting Out to C++

Chapter 3: Dealing with Data

Chapter 4: Compound Types

Chapter 5: Loops and Relational Expressions

Chapter 6: Branching Statements and Logical Operators

Chapter 7: Functions: C++ 's Programming Modules

Chapter 8: Adventures in Functions

Chapter 9: Memory Models and Namespaces

Chapter 10: Objects and Classes

Chapter 11: Working with Classes

Chapter 12: Classes and Dynamic Memory Allocation

Chapter 13: Class Inheritance

Chapter 14: Reusing Code in C++

Chapter 15: Friends, Exceptions, and More

Chapter 16: The `stirng` Class and the Standard Template Library

Chapter 17: Input, Output, and Files

Chapter 18: Visiting with the New C++ Standard



## Chapter 1 Getting Started with C++

- History and Philosophy of C and C++

  1. Unix, portable, low-level efficiency, concise & fast $$\to$$ C

  2. Data + algorithm = program

     data: what C++ focuses on. **object-oriented (OO)**

     algorithm: what C focuses on. **procedural**

     > C: Brian Kernighan & Dennis Ritichie, 20c70s
     >
     > C++ Bjnarne Stroustup, 20c80s

  3. C: top-down *big tasks $$\to$$ small tasks*  
     C++: top-down + bottom-up *classes $$\to$$ model*

  4. *TWO* aspects of C++  
     **OOP**: realized by classes  
     **Generic Programming**: realized by templates

     > Generic programming: provides tools for performing common tasks, independent from a particular data type.


- Portability and Standards

  ANSI $$ \to $$ ISO 

  ​	C++98: ISO/IEC 14882:1998, 

  ​	C++11: ISO/IEC 14882: 2011

- The mechanism of creating a program

  *source code (text)*$$\to $$**preprocessor**$$\to$$*modified source code (text)*$$\to$$**compiler**$$\to$$*assembly code (text)*$$\to$$**assembler**$$\to$$*object code (binary)*$$\to$$**linker**$$\to$$*executable (binary)*

  > **preprocessor**: execute **preprocessor directive**s, a.k.a., statements starting with `#`.
  >
  > **compiler**: analyze and optimize the source code, generating low-level assembly code files thereafter. Different object platforms (say, Windows, OS X, or ARM) may require different assembly code.
  >
  > **linker**: link your object code with **startup code** and **library code**.

- Source code extensions

  | compilation system   | valid C++ extensions |
  | -------------------- | -------------------- |
  | UNIX                 | C, cc, cxx, c        |
  | GNU C++              | C, cc, cxx, cpp, c++ |
  | Digital Mars         | cpp, cxx             |
  | Borland C++          | cpp                  |
  | Microsoft Visual C++ | cpp, cxx, cc         |



## Chapter 2: Setting Out to C++

- Creating a C++ program
- The general format for a C++ program
- The `#include` directive
- The `main()` function
- Using the `cout` object for output and the `cin` object for input
- Placing comments in a C++ program
- How and when to use `endl`
- Declaring and using variables
- Defining and using simple functions


##Chapter 3: Dealing with Data

- Rules for naming C++ variables
- C++'s built-in integer types: `unsigned long`, `long`, `unsigned int`, `int`, `unsigned short`, `short`, `char`, `unsigned char`, `signed char`, `bool`
- C++11's additions: `unsigned long long`, `long long`
- The `<climits>` and `<cfloats>` file
- C++'s built-in floating-point types:`float`, `double`, `long double`
- Numeric literals (constants) of various integer types
- Numeric literals (constants) of various floating-point types
- Using the `const` qualifier to create symbolic constants
- C++'s arithmetic operators
- Automatic type conversions
- Forced type conversions (type casts)
- Initialization (with C++11)




## Chapter 4: Compound Types

- Creating and using arrays, C-style strings, string-class strings
- Using the `getline()` and `get()` methods for reading strings
- Mixing string and numeric input
- Creating and using structures, unions, and enumerations
- Creating and using pointers
- Managing dynamic memory with `new` and `delete`
- Creating dynamic arrays
- Creating dynamic structures
- Automatic, static, and dynamic storage
- The `vector` and `array` classes (an introduction)
- Pointer Golden Rule: Always initialize
- Summarizing pointer points



## Chapter 5: Loops and Relational Expressions

- The `for` loop
- Expressions and statements
- The increment and decrement operators: `++` and `--`
- Combination assignment operators:  
  `+=`, `-=`, `*=`, `/=`, `%=`, `^=`, `&=`,`|=`, `>>=`,`<<=`
- Compound statements (blocks)
- The comma operator
- Relational operators: `>`, `>=`, `==`, `<=`,`<`, `!`
- The `while` and `do-while` loop
- The `typedef` facility
- The end-of-file condition
- The `get()` character input method
- Nested loops and two-dimensional arrays
- The range-based `for` loop (C++11)



## Chapter 6: Branching Statements and Logical Operators

- The `if` statement and the `if-else` statement
- Logical operators: `&&`, `||`, `!`
- The `<cctype>` library of character functions
- The conditional operator `?:`
- The `switch-case` statement
- The `continue` and `break` statements
- Number-reading loops
- Basic file input/output



## Chapter 7: Functions: C++'s Programming Modules

- Function basics
- Function prototypes
- Passing function arguments by value
- Designing functions to process arrays (including multiple dimensional)
- Using `const` pointer parameters
- Designing functions to process text strings
- Designing functions to process structures
- Designing functions to process objects of the `string` class
- Functions that call themselves (recursion)
- Pointers to functions



## Chapter 8: Adventures in Functions

- Inline functions
- Reference variables
- How to pass function arguments by reference
- Default arguments
- Function overloading
- Function templates
- Function template specializations



## Chapter 9: Memory Models and Namespaces

- Separate compilation of programs
- Storage duration, scope, and linkage
- Placement `new`
- Namespaces



## Chapter 10 Objects and Classes

- Procedural and object-oriented prograaming
- The concept of classes
- How to define and implement a class
- Public and private class access
- Class data members
- Class methods (function members)
- Creating and using class objects
- Class constructors and destructors
- `const` member functions
- The `this` pointer
- Creating arrays of objects
- Class scope
- Abstract data types



## Chapter 11 Working with Classes

- Operator overloading
- Friend functions
- Overloading the `<<` operator for output
- State members
- Using `rand()` to generate random values
- Automatic conversions and type casts for classes
- Class conversion functions



## Chapter 12 Classes and Dynamic Memory Allocations

- Using dynamic memory allocation for class members
- Implicit and explicit copy constructors
- Implicit and explicit overloaded assignment operators
- What you must do of you use `new` in a constructor
- Using static class members
- Using placement `new` with objects
- Using pointers to objects
- Implementing a `queue` abstract data type (ADT)



## Chapter 13: Class Inferitance

- Inheritance as an *is-a* relationship
- How to publicly derive one class from another
- Protected class access
- Constructor member initializer lists
- Upcasting and downcasting
- Virtual member functions
- Early (static) binding and late (dynamic) binding
- Abstract base classes
- Pure virtual functions
- When and how to use public Inheritance



## Chapter 14: Reusing Code in C++

- *has-a* relationships
- Classes with member objects (containment)
- The `valarray` template class
- Private and protected inheritance
- Multiple inheritance
- Virtual base classes
- Creating class templates
- Using class templates
- Template specializations



## Chapter 15: Friends, Exceptions, and More

- Friend classes
- Friend class methods
- Nested classes
- Throwing exceptions, `try` blocks, and `catach` blocks
- Exception classes
- Runtime type identification (RTTI)
- `dynamic_cast` and `typeid`
- `static_cast`, `const_cast`, and `reinterpret_cast`



## Chapter 16 The `string` Class and the Standard Template Library

- The standard C++ string class
- The `auto_ptr`, `unique_ptr`, and `shared_ptr` templates
- The Standard Template Library (STL)
- Container classes
- Iterators
- Function objects (functors)
- STL algorithms
- The `initializer_list` template



## Chapter 17: Input, Output, and Files

- The C++ view of input and output
- The `<iostream>` family of classes
- Redirection
- `ostream` class methods
- Formatting output
- `istream` class methods
- Stream states
- File I/O
- Using the `ifstream` class for input from files
- Using the `ofstream` class for output to files
- Using the `fstream` class for input and output
- Command-line processing
- Binary files
- Random file access
- Incore formatting



## Chapter 18: Visiting with New C++ Standard

> C++11: ISO/IEC 14882: 2011

- Move semantics and rvalue references
- Lambda function
- The function wrapper template
- Variadic templates

