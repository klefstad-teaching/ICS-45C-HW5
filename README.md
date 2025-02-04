# ICS 45C Homework 5

## Getting the assignment

1. Accept the assignment by clicking the link in Canvas
2. Once you accept the invite, you will reach a page that says "You're ready to go"
3. Click the URL from that page that looks similar to this: `https://github.com/klefstad-teaching/ics-45c-hw5-<YourGitHubUsername>`. It may take a bit of time for the repo to be ready.
4. Click the green `<> Code` Button on the top right, and then click the middle tab `SSH`
5. Copy that link
6. Go to your hub and go into the ICS45C folder, and open up the terminal and type in the following command:
```bash
git clone git@github.com:klefstad-teaching/ics-45c-hw5-<YourGitHubUserName>.git HW4
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
