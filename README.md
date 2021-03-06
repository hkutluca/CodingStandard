# Coding Standard for the Project

##  C++ Style Guide:

Style guide defines rules such as variable and class naming, braces and tab rules etc. Style guide is derived similar to WebKit style. But rules are changed as seen below. Install llvm-vs-code-extensions.vscode-clangd then use .clang-format  and .clang-tidys files to highlight and correct style problems.
if you cannot see all warnings in your editor pleas euse command line. 

Use:

$clang-tidy test.cpp -- -Imy_project/include -DMY_DEFINES ...

examples

$clang-tidy test.cpp 

$clang-tidy *


It is highly recomended that Software Engineers reads  Clean Code book from Robert C. Martin or watch videos of Clean Code from youtube (https://www.youtube.com/watch?v=7EmboKQH8lM&t=3325s) or from many available sources from internet.
- [Clean Code book from Robert C. Martin] https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882

##  Developing Robust C++ Code:

Roughly 80% of software defects when using the C or C++ language, are attributable to the incorrect usage of 20% of the language constructs. If the usage of the language can be restricted to avoid this subset that is known to be problematic, then the quality of the ensuing software is going to greatly increase. If you don’t want defects in your code, then don’t put them there!

To avoid undefined behaviour
-e.g. for C and C++, dividing by zero or dereferencing a NULL pointer
the language reference provides no definition of what behaviour to expect
To avoid implementation defined behaviour
-e.g. the size of an integer
-The compiler developer must document the decision taken
-Leads to un-portable code
- May mislead a programmer moving to a different programming environment
To improve clarity for review and maintenance.
-e.g not allowing 'count1' and 'countl' as variable names in the same program objective issue and solution
To provide a consistent style across a program or set of programs
-e.g. name format (Hungarian notation etc) or code layout similar to improving clarity - but subjective

There are several coding guidelines that propose using subsets of th language. MISRA C++ 2008 standard is most common such guideline. However it is based on C++98 rules. Later AUTOSAR C++2014 guideline is published to include C++11 and C++14 rules. Both guidelines are provided in github.
We strongly recomend engineers have a look to these guidelines and obey the rules as much as possible. We will setup tool support for these rules ın near feuture. 
Selected important rules are given below. 

------

#### Namespaces

1. *MISRA C++: 2008 Rule 7-3-1*
   Global namespace shall only contain main, namespace declarations and extern "C" declarations. Use explicit or anon namespaces for everything else.
2. Closing braces of namespaces should have a comment saying the namespace it closes:
```cpp
namespace foo
{
...
} // namespace foo
```

#### Constants
1. Prefer `const` or `constexpr` variables over `#defines` whenever possible, as the latter are not visible to the compiler.
2. *MISRA C++: 2008 Rule 7-1-1 and 7-1-2*
   A variable that is not modified after its initialization should be declared as `const`.
3. For naming of constants, see the Naming section of this document.



#### Literals
1. Except `0`  (only used in comparison for checking signness/existence/emptiness) and `nullptr`, `true`, `false`, all other literals should only be used for variable initialization.
   Example:
```cpp
if (nbInputs == 2U){/*...*/}
```
   Should be changed to:
```cpp
constexpr size_t kNbInputsWBias = 2U;
if (nbInputs == kNbInputsWBias) {/*...*/}
```


#### Brace Notation
1. Use the [Allman indentation](https://en.wikipedia.org/wiki/Indent_style#Allman_style) style.
2. Put the semicolon for an empty `for` or `while` loop in a new line.
3. *AUTOSAR C++14 Rule 6.6.3*, *MISRA C++: 2008 6-3-1*
   The statement forming the body of a `switch`, `while`, `do .. while` or `for` statement shall be a compound statement. (use brace-delimited statements)
4. *AUTOSAR C++14 Rule 6.6.4*, *MISRA C++: 2008 Rule 6-4-1*
   `If` and `else` should always be followed by brace-delimited statements, even if empty or a single statement.


#### Naming
1. Filenames, 
   * All first letters are uppercase: `ThisIsASubDir` and `ThisIsAFilename.cpp`
   * *NOTE*: All files involved in the compilation of a compilation target (.exe/.so) must have filenames that are case-insensitive unique.

2. Types  and  methods
   * All types (including, but not limited to, class names) are [camel case](https://en.wikipedia.org/wiki/Camel_case) with uppercase first letter. Example: `FooBarClass`

3. Local variables,  namespaces
   * Camel case with first letter lowercase. Example: `localFooBar`

4. Non-magic-number global variables that are non-static and not defined in anonymous namespace
   * Camel case prefixed by a lower case 'g'. Example: `gDontUseGlobalFoos`

5. Non-magic-number global variables that are static or defined in an anonymous namespace
   * Camel case prefixed by a lower case  's'. Example: `sMutableStaticGlobal`

6. Locally visible static variable
   * Camel case with lowercase prefix ''s" as the first letter of the name. Example: `static std::once_flag sCaskInitOnce;`

7. Public, private and protected class member variables
   * Camel case with first letter lowercase prefixed with an 'm_': `m_nbFooValues`.
   * Public member variables do not require the 'm' prefix but it is highly encouraged to use the prefix when needed to improve code clarity, especially in cases where the class is a base class in an inheritance chain.

8. Constants
   * Enumerations, global constants, static constants at class-scope and function-scope magic-number/literal constants are uppercase snakecase with prefix 'k':
```cpp
const int kDIGIT_NUM = 10;
```
> *NOTE*: Function-scope constants that are not magic numbers or literals are named like non-constant variables:
```cpp
const bool pass = a && b;
```

9. Macros
   * See [Constants](CODING-GUIDELINES.md#constants), which are preferred over `#define`.
   * If you must use macros, however, follow uppercase snakecase: `FOO_VERSION`

Notes:
* In general we don't use [hungarian notation](https://en.wikipedia.org/wiki/Hungarian_notation), except for 'apps hungarian' in some cases such as 'nb' in a variable name to indicate count: `mNbCallDescriptors`
* If a constructor's parameter name `foo` conflicts with a public member name `foo`, add a trailing underscore to the parameter name: `foo_`.
* *MISRA C++: 2008 Rule 2-13-4*
  Literal suffixes should be upper case. For example, use `1234L` instead of `1234l`.


#### Tabs vs Spaces
1. Use only spaces. Do not use tabs.
2. Indent 4 spaces at a time. This is enforced automatically if you format your code using our clang-format config.


#### Formatting
1. Use the [LLVM clang-format](https://clang.llvm.org/docs/ClangFormat.html) tool for formatting your changes prior to submitting the PR.
2. Use a maximum of 120 characters per line. The auto formatting tool will wrap longer lines.
3. Exceptions to formatting violations must be justified on a per-case basis. Bypassing the formatting rules is discouraged, but can be achieved for exceptions as follows:
```cpp
// clang-format off
// .. Unformatted code ..
// clang-format on
```


#### Pointers and Memory Allocation
1. *AUTOSAR C++ 2014: 18-5-2/3*
   Use smart pointers for allocating objects on the heap.
2. When picking a smart pointer, prefer `unique_ptr` for single resource ownership and `shared_ptr` for shared resource ownership. Use `weak_ptr` only in exceptional cases.
3. Do not use smart pointers that have been deprecated in C++11.


#### Comments
1. C++ comments are required. C comments are not allowed except for special cases (inline).
2. C++ style for single-line comments. `// This is a single line comment`
3. In function calls where parameters are not obvious from inspection, it can be helpful to use an inline C comment to document the parameter for readers:
```cpp
doSomeOperation(/* checkForErrors = */ false);
```
4. If the comment is a full sentence, it should be capitalized i.e. start with capital letter and punctuated properly.
5. Follow [Doxygen rules](http://www.doxygen.nl/manual/docblocks.html) for documenting new class interfaces and function prototypes.
* For C++-style single-line comments use `//!`.
* For class members, use `//!<`.
```cpp
//! This is a Doxygen comment
//! in C++ style

struct Foo
{
    int x; //!< This is a Doxygen comment for members
}
```


#### Disabling Code
1. Use `#if` / `#endif` to disable code, preferably with a mnemonic condition like this:
```cpp
#if DEBUG_CONVOLUTION_INSTRUMENTATION
// ...code to be disabled...
#endif
```

```cpp
// Alternative: use a macro which evaluates to a noop in release code.
#if DEBUG_CONVOLUTION_INSTRUMENTATION
# define DEBUG_CONV_CODE(x) x
#else
# define DEBUG_CONV_CODE(x)
#endif
```

2. *MISRA C++: 2008 Rule 0-1-9*, *AutoSAR C++ 2014: 6-0-1*
   Dead code is forbidden in safety-critical software - you may not use compile-time expressions and DCE to disable code. However, this technique can be useful elsewhere (e.g. tools, tests) to help prevent bitrot.

```cpp
// Not allowed in safety-critical code.
const bool gDisabledFeature = false;

void foo()
{
   if (gDisabledFeature)
   {
       doSomething();
   }
}
```

3. *MISRA C++: 2008 Rule 2-7-2 and 2-7-3*
   Do NOT use comments to disable code. Use comments to explain code, not hide it.


#### Exceptions
1.  Exceptions must not be thrown across library boundaries.


#### Casts
1. Use the least forceful cast necessary, or no cast if possible, to help the compiler diagnose unintended consequences.
2. Casting a pointer to a `void*` should be implicit (except if removing `const`).
3. *MISRA C++: 2008 Rule 5-2-5*
   Casting should not remove any `const` or `volatile` qualification from the type of a pointer or reference.
4. *MISRA C++: 2008 Rule 5-2-4*
   Do not use C-style casts (other than void casts) and functional notation casts (other than explicit constructor calls).
6. Casting from a `void*` to a `T*` should be done with `static_cast`, not `reinterpret_cast`, since the latter is more forceful.
7. Use `reinterpret_cast` as a last resort, where `const_cast` and `static_cast` won't work.
8. Avoid `dynamic_cast`.


#### Expressions
1. *MISRA C++: 2008 Rule 6-2-1*
   Do not use assignment operator in subexpressions.
```cpp
// Not compliant
x = y = z;

// Not compliant
if (x = y)
{
    // ...
}
```


#### Ternary operator
1. *AUTOSAR C++ 2014: 7-1-1*
   Ternary operator should not be used as a sub-expression. Ternary operator expressions should be encapsulated with braces. Example:
```cpp
const auto var = (condition0 ? a : (condition1 ? b : c));
```
   should be changed to:
```cpp
const auto d = (condition1 ? b : c);
const auto var = (condition0 ? a : d);
```


#### Statements
1. When practical, a `switch` statement controlled by an `enum` should have a case for each enum value and not have a default clause so that we get a compile-time error if a new enum value is added.
2. *MISRA C++:2008 Rules 6-4-3, 6-4-4, and 6-4-5*
   Switch statements should be well structured.  An informal guideline is to treat switch statements as structured multi-way branches and not "glorified gotos" such as:
```cpp
// Not compliant
switch (x) case 4: if (y) case 5: return 0; else default: return 1;
```
3. The "well structured" requirement prohibits fall-though except from one case label to another.   Each case clause must be terminated in a break or throw.  If a case clause has multiple statements, the braces are optional.  The following example illustrates these requirements:
```cpp
switch (x)
{
case 0:         // Fall-through allowed from case 0: to case 1: since case 0 is empty.
case 1:
    a();
    b();
    break;
case 2:
case 4:
{              // With optional braces
    c();
    d();
    break;
}
case 5:
    c();
    throw 42;  // Terminating with throw is okay
default:
    throw 42;
}
```

4. *MISRA C++:2008 Rule 6-4-3*
   Ending a case clause with return is not allowed.
5. If a switch clause is a compound statement, put the break inside the braces.
```cpp
switch (x)
{
case 0:
case 1:
{
    y();
    z();
    break;
}
...other cases...
}
```

#### Functions
1. Avoid declaring large functions as `inline`, absent a quantifiable benefit.  Remember that functions defined in class declarations are implicitly inline.
2. Rather than using the `static` keyword to mark a function as having internal linkage, prefer to use anonymous namespaces instead.
3. *MISRA C++:2008 Rule 0-1-10*
   Every defined function must be called at least once. That is, do not have unused methods.
4. *MISRA C++:2008 Rule 8-4-2*
   Parameter names should be consistent across function definition and corresponding function declarations.


#### Forward declarations and extern variables

1. *MISRA C++: 2008 Rule 3-2-3*
   For safety critical code, a type, object or function that is used in multiple translation units shall be declared in one and only one file.
   * This means we cannot forward declare incomplete types in files where they are needed. Instead, we should put forward declarations in header files, and include these header files as needed.


#### Structures and Classes
1. *MISRA C++: 2008 Rule 14-7-1*
   All class templates, function templates, class template member functions and class template static members shall be instantiated at least once. This prevents use of uninitialized variables.
2. *MISRA C++: 2008 Rule 11-01*
   If class is not a *Plain Old Data Structure*, then its data members should be private.


#### Preprocessor Directives
1. *MISRA C++: 2008 Rule 16-0-2*
   `#define` and `#undef` of macros should be done only at global namespace.
2. Avoid the use of `#ifdef` and `#ifndef` directives (except in the case of header include guards). Prefer to use `#if defined(...)` or `#if !defined(...)` instead. The latter syntax is more consistent with C syntax, and allows you to use more complicated preprocessor conditionals, e.g.:
```cpp
#if defined(FOO) || defined(BAR)
void foo();
#endif // defined(FOO) || defined(BAR)
```

3. When nesting preprocessor directives, use indentation after the hash mark (#). For example:
```cpp
#if defined(FOO)
# if FOO == 0
#  define BAR 0
# elif FOO == 1
#  define BAR 5
# else
#  error "invalid FOO value"
# endif
#endif
```

4. Do not use `#pragma` once as include guard.
5. Use a preprocessor guard. It's standard-conforming and modern compilers are smart enough to open the file only once.
   * The guard name must have prefix `TRT_` followed by the filename, all in caps. For a header file named `FooBarHello.h`, name the symbol as `FOO_BAR_HELLO_H`.
   * Only use the file name to create the symbol. Unlike the Google C++ guideline, we do not include the directory names in the symbol. This is because we ensure all filenames are unique in the compilation unit.
   * Do not use prefix with underscore. Such symbols are reserved in C++ standard for compilers or implementation.
   * Do not use trailing underscore for the symbol. We differ in this from Google C++ guideline, which uses trailing underscore: `FOO_BAR_HELLO_H_`
```cpp
#ifndef FOO_BAR_HELLO_H
#define FOO_BAR_HELLO_H
// ...
#endif // FOO_BAR_HELLO_H
```

6. *AUTOSAR C++ 2014: 7-1-6*
   Use `using` instead of `typedef`.


#### Signed vs Unsigned Integers
1. Use signed integers instead of unsigned, except for  the cases below.
* The integer is a bitmap - use an unsigned type, since sign extension could lead to surprises.
* The integer is being used with an external library that expects an unsigned integer.  A common example is a loop that compares against `std::vector::size()`, such as:
```cpp
for (size_t i = 0; i < mActiveCalls.size(); ++i) // preferred style
```
* Using only signed integers for the above would lead to prolixity and perhaps unsafe narrowing:
```cpp
for (int i = 0; i < static_cast<int>(mActiveCalls.size()); ++i)
```


#### Common Pitfalls

1. C headers should not be used directly.
   - Example: Use `<cstdint>` instead of  `<stdint.h>`
2. Do not use C library functions, whenever possible.
   * Use brace initialization or `std::fill_n()` instead of `memset()`. This is especially important when dealing with non-[POD types](http://en.cppreference.com/w/cpp/concept/PODType). In the example below, using `memset()` will corrupt the vtable of `Foo:`
```cpp
struct Foo {
    virtual int GetX() { return x; }
    int x;
};
...

// Bad: use memset() to initialize Foo
{
    Foo foo;
    memset(&foo, 0, sizeof(foo)); // Destroys hiddien virtual-function-table pointer!
}
// Good: use brace initialization to initialize Foo
{
    Foo foo = {};
}
```

2. When specifying pointers to `const` data, the pointer itself may be `const`, in some usecases.
```cpp
char const * const errStr = getErrorStr(status);
```

----


