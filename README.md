# ICS 45C Homework 5

A singly-linked list implementation of class String

## Getting the assignment

1. Accept the assignment by clicking the link in Canvas
2. Once you accept the invite, you will reach a page that says "You're ready to go"
3. Click the URL from that page that looks similar to this: `https://github.com/klefstad-teaching/ics-45c-hw5-<YourGitHubUsername>`. It may take a bit of time for the repo to be ready.
4. Click the green `<> Code` Button on the top right, and then click the middle tab `SSH`
5. Copy that link
6. Go to your hub and go into the ICS45C folder, and open up the terminal and type in the following command:
```bash
git clone git@github.com:klefstad-teaching/ics-45c-hw5-<YourGitHubUserName>.git HW5
```
7. Go to VSCode and open up the `HW5` Folder


## Directory Structure

```bash
.
├── CMakeLists.txt
├── CMakePresets.json
├── gtest
│   ├── gtestmain.cpp
│   ├── string_gtests.cpp
│   └── student_gtests.cpp
└── src
    ├── alloc.cpp
    ├── alloc.hpp
    ├── list.cpp
    ├── list.hpp
    ├── standard_main.cpp
    ├── string.cpp
    └── string.hpp
```

## Overview and Objectives

For Homework 5, class `String` is implemented as a ***singly-linked list*** of characters, rather than the dynamically allocated arrays in Homework 4. Class `String` is now supported by a **namespace** called `list` which contains class `Node` and singly-linked list helper functions.

Singly-linked lists use memory storage proportional to the number of characters in the string—no more and no less. Strings have varying lengths, and the singly-linked list which stores them is the same length as the number of characters in this `String` object. (Note that there is no null terminating character needed in linked lists! Use the `nullptr` to indicate the end of the linked list.)

> Note: For the remainder of these instructions, the term "linked list" will refer to the singly-linked list data structure (as opposed to doubly-linked list, or other more complex linked lists).

The length of the linked list must change with operations such as concatenation, assignment, or `read()`. The static helper functions from Homework 3 or Homework 4 will no longer work with linked lists, so a new set of helper methods are implemented for Homework 5, with similar names and behaviors.

We create a new **namespace**, called `list`, to hold our definition of a helper class named `Node`, as all the helper functions needed to implement the methods of class `String`. A namespace is a convenient way of grouping classes, functions, and variables into a common scope without using a class full of static members. From outside the namespace, you must use `list::Node`, `list::reverse`, etc. to refer to the members of the namespace. From inside the namespace, you can leave out the `list::` prefix.

## Relation to Course Objectives

**Proficiency in implementing linked lists efficiently—a core prerequisite skill for ICS46 and above.** You learn how to implement (and debug) linked lists without memory leaks or other memory errors, and become more skilled at test-driven development.

Like arrays, the linked list is a fundamental data structure in C++, and implementing it correctly and efficiently is tricky. You will implement linked lists three times in this course, to help you gain proficiency and fluency, essential to build upon in Data Structures (ICS 46) and many other upper-division classes. Linked list processing is also (in?)famously part of many technical job interviews.

Through the progression of Homeworks 3, 4, and 5, you also see an example of how a class’s interface can be separated from its implementation, and understand how a class’s underlying implementation can change while its interface remains the same.

Lastly, you will learn about more features of C++ such as namespaces, and the C++20 way to implement comparisons with less code duplication, the **spaceship operator**.

## Organization of Classes and Files

> Be sure to use **exactly** the names given in these instructions and in this repo for **files**, **functions**, and **classes**, because the autograder will be expecting exactly those names.

> ⚠️ **Make sure your `#includes` and `using` match exactly what is given in the screenshots**, for the autograder to run your submission with our own test programs. *Note that if there are errors in these files (typos, omissions, misspellings, or #include problems), your program may not compile and link.*

## 1 class String

None of the static C-string helper methods from Homework 4’s class `String` can be reused to implement linked lists. **Instead, all helper functions are now defined in a new namespace named `list` defined in the two files `list.hpp` and `list.cpp`.**

The methods of class `String` **must never** call `new` or `delete` directly. They must call `list` helper functions instead. (`list` helper functions ***will*** call `new` and `delete` directly.)

Class `String` will now have only one data member named `head`, of type pointer to a `list::Node`. A `String` object whose value is the empty String has a `head` that contains the value `nullptr`.

Other than that, three other small changes have been made to the interface:

1. There is only the const-version of `operator[]` now, and it returns `char` instead of `const char&`. Otherwise, there is no reasonable thing to return in the case of an empty `String`. **Also, indexing into a list is a slow operation - don't use it in a loop!**
2. The private constructor takes a `list::Node*` argument instead of an `int`. You will once again find this useful for `operator+` and `reverse`. This constructor should simply set `head` to the given `list::Node*`. **It does not allocate anything.**
3. The six comparison operators (`==`, `!=`, `<=`, `<`, `>`, `>=`) have been replaced by just two (`==`, `<=>`). This change is explained further below.

> **No other data members may be added to class `String`.**

## 1.1 Implementing comparisons with the spaceship operator

In Homeworks 3 and 4 you implemented six different comparison operators (`==`, `!=`, `<=`, `<`, `>`, `>=`) for class `String`, all of which used almost the same code. In this homework, you will see a more modern and convenient way of defining comparisons that was introduced in C++20.

First, you will implement `String::operator==` as before using `list::compare` instead of `String::strcmp`. The compiler will then effectively define `String::operator!=` for you by just negating your `String::operator==`.

Next, you will implement four ordered comparisons (`<=`, `<`, `>`, `>=`) by defining `String::operator<=>`, which is also known as the spaceship operator. The spaceship operator does not return a `bool`; instead it returns a special value that directly describes the ordering of its arguments. For example:

- `3 <=> 5` returns the value `strong_ordering::less` meaning "3 is less than 5",
- `10 <=> -5` returns `strong_ordering::greater`, and
- `0 <=> 0` returns `strong_ordering::equal`.

`strong_ordering` is defined in the `<compare>` header of the standard library.

A possible implementation is:
![spaceship operator implementation](/assets/1-1-1.png)

But a much nicer implementation is:
![much nicer spaceship operator implementation](/assets/1-1-2.png)

String.hpp screenshot
![String.hpp screenshot](/assets/1-1-3.png)

## 2 list.hpp and class list::Node

Class `list::Node` defines a single link, or node, in a linked list of nodes containing characters. namespace `list` holds helper functions that are used by the public methods of class `String` to implement class `String` as a linked list of characters. The algorithms from Homework 3 & 4 helper functions may be reused, but the details of traversing must change to traverse a linked list.

`list::Nodes` are dynamically allocated using `new` and deallocated using `delete`. new allocates a single object, and `delete` deletes a single object. *(Note that there are **no brackets!** Brackets indicate an array of objects in contiguous storage. Never confuse them!)*

    `Node *p = new Node{'A', nullptr};` allocates `new` storage for a single Node initializing data to 'A' and `next` to `nullptr`, and returns the address which is saved in the pointer variable P. Use the curly braces around the initial values for `data` and `next` whenever you create a new `list::Node`. Since C++ 20 you can also use the more explicit syntax `Node *p = new Node {.data = 'A', .next = nullptr};` to make it more clear which field is getting what value.

    `delete p;` frees the storage, where p is a pointer that holds the address of a `list::Node` to deallocate

Only the helper functions in namespace `list` may call `new` or `delete`. We continue to use `alloc.hpp` and `alloc.cpp` from Homework 4 to track allocations for us, described below.

Linked lists are an opportunity to experiment with recursion. Some list processing functions  are easier and cleaner to write recursively. If you implemented them iteratively, try implementing them recursively after your program is working.

## 2.1 Preconditions:

Most of the functions in namespace `list` should work also when given `nullptr` as arguments for any `list::Node*` (in the same way that the string functions supported empty strings). However, there are three exceptions:

- `list::nth` is only required to work if `head != nullptr`.
- `list::last` is only required to work if `head != nullptr`.
- `list::index` is only required to work if `head != nullptr` and `node` points to a node that is actually in the linked list starting at head. In particular, `node != nullptr` as well.

In those cases, your code can do anything, and your tests should not assume a particular behavior.

namespace list defined in list.hpp screenshot

![list.hpp screenshot](/assets/2-1-1.png)

## 3 Class AllocationTracker

As with Homework 4, use class `AllocationTracker`, in the provided files `alloc.hpp` and `alloc.cpp`, to track and record information about all heap allocations and deallocations with its custom implementations of `new` and `delete`.

>❗Do not modify anything in `alloc.hpp` or `alloc.cpp`.

An `AllocationTracker` object will be created and called in `standard_main.cpp`.

## 4 standard_main and *_gtests.cpp:  Test each function in class String and class list::Node

![comic](/assets/4-1.png)

In `student_gtests.cpp`, **fully test and debug** your implementation of the **namespace `list` helper functions (and only those functions). As in Homework 4, your GTests will be tested by the autograder.**

Use `standard_main.cpp` and `string_gtests.cpp` to test and debug the implementation of the **public methods of class** `String`. By now, you should have a good collection of tests.

If you get a segfault, the only way to debug it is by running your `standard_main`, `string_gtests`, or `student_gtests` with gdb to reveal the bug.

If your program runs fine under your tests, but fails with the autograder, that is because the autograder’s test program revealed bugs that your tests did not reveal to you. For example, if your operator + fails on the autograder, but not under your tests, you must add test cases to reveal the cause of the segfault. Then you can use gdb to show you exactly what instruction causes the segfault.

At the end of `standard_main.cpp`, in `main()`, you must use an `AllocationTracker` object to track and report your allocations.

## Steps of Development

Follow the general approach below to make debugging more successful and minimize your memory allocations.

### Write and test the helper functions defined in namespace list.

1. Write the functions in namespace list and the functions to test them in student_gtests.cpp.  Follow the method of test-driven development detailed in previous homework assignments.

### Convert class String to use list::Node (linked lists of char)

2. Write most* of the methods of class String to change its representation to use a list::Node. *Do not yet define destructor, move constructor, or move assignment—to make it much easier to debug the others. This means fully comment out these methods so they do not get called with empty definitions which will likely result in fatal errors.
3. Add test functions in string_gtests.cpp for each feature. Add only one test at a time, starting with creating a single String object from a C-string literal, then print out the String object and/or check its size. Debug that first test until you are convinced it is correct, and then proceed to the next test. For now, have the destructor doing nothing, which will result in many memory leaks, but will allow you to get your program otherwise functioning correctly. Once you have all of those test functions in student_gtests.cpp working, then define your destructor for class String to call list::free(head).
4. Cross your fingers and pray. 🙏
5. Run student_gtests with sanitizers or valgrind. Any time you allocate heap storage with new, always run with sanitizers or valgrind to help you catch memory- and pointer-related errors, which the C++ compiler will not catch for you. The default preset uses the sanitizers. To run with valgrind, you can use
cmake --preset valgrind

to create a separate build_valgrind folder. You can compile into that folder with

cmake --build build_valgrind

and run programs with

valgrind build_valgrind/standard_main.

Once the rest of your program is functioning correctly, define the move constructor, compile and run, test until it works.
Define the move assignment operator, compile and run, test until it works.

## How to Submit and Grade the programs

In GradeScope for Homework 5, submit the files from GitHub hw5:

- `string.hpp`
- `string.cpp`
- `list.hpp`
- `list.cpp`
- `alloc.hpp`
- `alloc.cpp`
- `standard_main.cpp`
- `student_gtests.cpp`

## Grading criteria

Points are allotted for

The quality of your tests of the list:: functions in student_gtests.cpp.
Preventing memory leaks. Major points are deducted for mismatched new and delete and other memory errors detected by sanitizer.
Passing tests of functional correctness of list::Node functions.
Passing tests of functional correctness of String public methods.
Reducing excessive memory allocations by thoughtful, correct allocation-reducing techniques.

The autograder must compile and run your code, so if your code does not compile on Linux, the autograder cannot run your program to award you any points. Your score will be 0. Failure to compile can also result from errors in your #includes or errors/typos/mismatches in your .hpp and .cpp class files. You must fix these errors in order to receive a score.

Error message such as

standard_main failed to build/run properly
This message only appears if the script broke, please contact the staff
Segmentation fault
usually indicate that there are one or more fatal error(s) somewhere in your class String or namespace list functions that you must test and debug before submitting. “Script broke” means the memory error (segfault) crashed the program, so the autograder could not even continue to run.

We may adjust grades manually when warranted. For example, a submission that attempts to defraud the autograder points by not implementing the requirements may be given a 0.

## Build Instructions

```bash
# Produce the `build` folder with the presets provided for the homework:
cmake --preset default

# Build all targets at once:
cmake --build build

# Build only standard_main.cpp:
cmake --build build --target standard_main

# Build string gtests:
cmake --build build --target string_gtests

# Build student gtests:
cmake --build build --target student_gtests
```

To run the above targets after compiling them:

```bash
./build/standard_main        # Runs the 'main' function from src/standard_main.cpp
./build/string_gtests        # Runs the 'string' gtests
./build/student_gtests       # Runs the 'student' gtests
```

NOTE : If you want to also use valgrind to check your code, use these instructions:

```bash
# Produce the `build_valgrind` folder with the presets provided for the homework:
cmake --preset valgrind

# Build all targets at once:
cmake --build build_valgrind

# Build only standard_main.cpp:
cmake --build build_valgrind --target standard_main

# Build string gtests:
cmake --build build_valgrind --target string_gtests

# Build student gtests:
cmake --build build_valgrind --target student_gtests
```

Then run the above targets with:

```bash
./build_valgrind/standard_main        # Runs the 'main' function from src/standard_main.cpp
./build_valgrind/string_gtests        # Runs the 'string' gtests
./build_valgrind/student_gtests       # Runs the 'student' gtests
```

Once you have run the code above and it either produces the output you expected or passes
all provided tests, congratulations! You are now ready to [submit](#submission) your homework!

## Submission

As with previous submissions, submit via `GitHub` by the following steps:

1. `git add .`
2. `git commit -a -m "Commit Message Here"`
3. `git push --set-upstream origin main`

to push your changes to your private GitHub repository, and then submit `hw5` to `Gradescope`.

## Credit

All code moved from [ICS45c](https://github.com/RayKlefstad/ICS45c/tree/hw5)
