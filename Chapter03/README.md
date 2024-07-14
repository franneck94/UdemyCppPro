# CMake Tutorial

## Generating a Project

```bash
cmake [<options>] -S <path-to-source> -B <path-to-build>
```

Assuming that a CMakeLists.txt is in the root directory, you can generate a project like the following.

```bash
mkdir build
cd build
cmake -S .. -B . # Option 1
cmake .. # Option 2
```

Assuming that you have already built the CMake project, you can update the generated project.

```bash
cd build
cmake .
```

## Generator for GCC and Clang

```bash
cd build
cmake -S .. -B . -G "Unix Makefiles" # Option 1
cmake .. -G "Unix Makefiles" # Option 2
```

## Generator for MSVC

```bash
cd build
cmake -S .. -B . -G "Visual Studio 16 2019" # Option 1
cmake .. -G "Visual Studio 16 2019" # Option 2
```

## Specify the Build Type

Per default, the standard type is in most cases the debug type.
If you want to generate the project, for example, in release mode you have to set the build type.

```bash
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
```

## Passing Options

If you have set some options in the CMakeLists, you can pass values in the command line.

```bash
cd build
cmake -DMY_OPTION=[ON|OFF] ..
```

## Specify the Build Target (Option 1)

The standard build command would build all created targets within the CMakeLists.
If you want to build a specific target, you can do so.

```bash
cd build
cmake --build . --target ExternalLibraries_Executable
```

The target *ExternalLibraries_Executable* is just an example of a possible target name.
Note: All dependent targets will be built beforehand.

## Specify the Build Target (Option 2)

Besides setting the target within the cmake build command, you could also run the previously generated Makefile (from the generating step).
If you want to build the *ExternalLibraries_Executable*, you could do the following.

```bash
cd build
make ExternalLibraries_Executable
```

## Run the Executable

After generating the project and building a specific target you might want to run the executable.
In the default case, the executable is stored in *build/5_ExternalLibraries/app/ExternalLibraries_Executable*, assuming that you are building the project *5_ExternalLibraries* and the main file of the executable is in the *app* dir.

```bash
cd build
./bin/ExternalLibraries_Executable
```

## Different Linking Types

```cmake
add_library(A ...)
add_library(B ...)
add_library(C ...)
```

### PUBLIC

```cmake
target_link_libraries(A PUBLIC B)
target_link_libraries(C PUBLIC A)
```

When A links in B as *PUBLIC*, it says that A uses B in its implementation, and B is also used in A's public API. Hence, C can use B since it is part of the public API of A.

### PRIVATE

```cmake
target_link_libraries(A PRIVATE B)
target_link_libraries(C PRIVATE A)
```

When A links in B as *PRIVATE*, it is saying that A uses B in its
implementation, but B is not used in any part of A's public API. Any code
that makes calls into A would not need to refer directly to anything from
B.

### INTERFACE

```cmake
add_library(D INTERFACE)
target_include_directories(D INTERFACE {CMAKE_CURRENT_SOURCE_DIR}/include)
```

In general, used for header-only libraries.

## Different Library Types

### Library

A binary file that contains information about code.
A library cannot be executed on its own.
An application utilizes a library.

### Shared

- Linux: \*.so
- MacOS: \*.dylib
- Windows: \*.dll

Shared libraries reduce the amount of code that is duplicated in each program that makes use of the library, keeping the binaries small.
Shared libraries will however have a small additional cost for the execution.
In general the shared library is in the same directory as the executable.

### Static

- Linux/MacOS: *.a
- Windows: *.lib

Static libraries increase the overall size of the binary, but it means that you don't need to carry along a copy of the library that is being used.
As the code is connected at compile time there are not any additional run-time loading costs.

## Important CMake Variables for Paths

- CMAKE_SOURCE_DIR
  - Topmost folder (source directory) that contains a CMakeList.txt file.
- PROJECT_SOURCE_DIR
  - Contains the full path to the root of your project source directory.
- CMAKE_CURRENT_SOURCE_DIR
  - The directory where the currently processed CMakeLists.txt is located in.
- CMAKE_CURRENT_LIST_DIR
  - The directory of the listfile currently being processed. (for example a \*.cmake Module)
- CMAKE_MODULE_PATH
  - Tell CMake to search first in directories listed in CMAKE_MODULE_PATH when you use FIND_PACKAGE() or INCLUDE().
- CMAKE_BINARY_DIR
  - The filepath to the build directory

## Custom Targets and Commands

- When is needed to use add_custom_target?  
Each time we need to run a command to do something in our build system different to build a library or an executable.

- When is a good idea to run a command in add_custom_target?  
When the command must be executed always the target is built.

- When is a good idea to use add_custom_command?  
Always we want to run the command when is needed: if we need to generate a file (or more) or regenerate it if something changed in the source folder.

- When is a good idea to use execute_process?  
Running a command at configure time.

## Macros vs Functions

The only difference between a function and a macro is scope; macros don't have one.  
So, if you set a variable in a function and want it to be visible outside, you'll need PARENT_SCOPE.

## Generator Expressions

Generator expressions are evaluated during build system generation to produce information specific to each build configuration. They have the form $<...>.

Most generator-expressions in the wild will contain a conditional component.  
This is due to the fact that generator-expressions can replace if-else statements in properties context.

A conditional expression boils down to the following form:

```cmake
$<condition:value-if-true>
```

The result of the entire conditional expression is value-if-true if the condition evaluates to 1, and an empty string otherwise.

```cmake
add_compile_options("$<$<AND:$<CXX_COMPILER_ID:MSVC>,$<CONFIG:DEBUG>>:/MDd>")

add_library(Foo ...)
target_link_options(Foo
    PRIVATE
        $<$<CONFIG:Debug>:--coverage>
)
```
