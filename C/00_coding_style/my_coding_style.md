# My C Coding Style

Time: Aug-26-2026

### 1. Qualifier Position

Qualifiers go before the type name.

```c
static const int num;
```

### 2. Brace Style

Braces always occupy a full line (Allman style).

```c
if (condition)
{
    do_something();
}
```

### 3. Space After Commas in Parameter Lists

Put one space after each comma in a parameter list.

```c
func(int a, int b, int c);
```

### 4. Parameter Wrapping

When a parameter list has more than three parameters, wrap the line. After wrapping, align with the first parameter.

```c
void func(int a, int b, int c,
          int d);
```

### 5. Comment Placement

Do not put comments at the end of a code line. Put comments on their own line, aligned with the code.

```c
/* This is the correct comment position */
int value = 0;
```

### 6. Pointer Asterisk Position

Place the asterisk before the variable name.

```c
const char *ptr;
char *const ptr;
const char *const ptr;
```

Function pointers should generally use `typedef`. In special cases:

```c
void (*ptr)(void);
```

### 7. One Variable per Line

Declare only one variable per line.

```c
int a;
int b;
```

### 8. Function Sections and Blank Lines

Inside a function, group code into sections by functionality. Put one blank line between functions.

```c
void func1(void)
{
    /* Section 1 */
    ...

    /* Section 2 */
    ...
}

void func2(void)
{
    ...
}
```

### 9. Space After Keywords

Except for function names and `sizeof`, put one space after keywords such as `if`, `while`, and `for` before the opening parenthesis.

```c
if (condition)
while (condition)
for (i = 0; i < n; i++)
```

### 10. Braces Are Required

Even if an `if` body contains only one statement, use braces.

```c
if (condition)
{
    do_something();
}
```

### 11. Indentation

Use tabs for indentation, with a display width of 4 spaces. Leading indentation must use tabs. If inline column alignment is needed (such as aligning a wrapped parameter list with the first parameter, or aligning backslashes in macro definitions), use spaces to fill the gap. Never use spaces for leading indentation.

### 12. Operator Spacing

Put one space between operators and operands, as in `a + b = c;`.

Exceptions are `->` and grouping by operator precedence. For example, `a*b + c*d;` is better.

### 13. Explicit Type Conversion

Explicit is better than implicit. Write type conversions explicitly and do not rely on implicit promotion. When mixing `char` and `int`, signed and unsigned types, or integer and floating-point types, explicitly cast before performing the operation to avoid problems caused by implicit conversion.

Exception: implicit conversion of `void *` pointers, which is part of C's design philosophy.

### 14. Shift Operations

Use unsigned integer types for shift operations.

### 15. Function Prototypes and `static`

Function prototypes for exposed interfaces go in header files. Static functions (`static`-qualified) are written inside the file in call order, without prototypes, unless multiple static functions call each other.

All internal functions must be qualified with `static`. Do not leave a function without `static` and also not declared in a header file. Eliminate this intermediate state.

### 16. Pointer Initialization and Checks

Initialize pointer variables at declaration:

```c
int *ptr = malloc(sizeof(int));
int *ptr = &num;
int *ptr = NULL;
```

Avoid a bare `int *ptr`. After `free`, immediately set the pointer to `NULL`.

After dynamic memory allocation, check whether allocation succeeded (whether it equals `NULL`). All pointer arrays must be explicitly initialized to `{NULL}` at declaration (the first element is set to `NULL`, and the remaining elements are set to 0). Check for `NULL` before dereferencing any pointer.

### 17. `extern` and `static inline`

Variables shared by multiple files should be declared with `extern` in a header file. `extern` variables may only be used in header files and may only declare global variables.

`static` is only allowed in header files when combined with `inline`, that is, `static inline`. This is because if `static` qualifies a function prototype in a header file, and the actual definition in the `.c` file is not `static` (that is, it is intended to have external linkage), the compiler may not report a type mismatch and may instead treat the function as `static`, causing hidden problems.

### 18. Header Guards

Header files must contain include guards:

```c
#ifndef XX_H
#define XX_H

...

#endif
```

### 19. Include Order and Dependencies

The `#include` order is fixed. First include the header corresponding to the current file. Then include standard libraries, third-party libraries, and personal libraries in that order. Separate different library groups with blank lines.

If a function declaration, struct member, or macro definition in your own `.h` file uses a type from another header, include that header in the `.h` file, and include it again in the corresponding `.c` file when it is also used there.

In other cases, generally put the `#include` in the `.c` file.

Even for standard libraries, include the header that originally defines the symbol. Do not rely on transitive includes. For example, if a definition in a header uses `NULL`, include `<stddef.h>` in the header instead of `<stdlib.h>`. If the corresponding `.c` file also uses it, include `<stddef.h>` there as well.

### 20. No `switch` Fallthrough

Do not use `switch` fallthrough unless it is mentioned in a comment. Every `case` must end with `break` or `return`.

### 21. Naming Rules

Macro names are all uppercase. Struct, enum, and union names are capitalized. Ordinary variables are all lowercase. File names are all lowercase.

### 22. Nested Struct Initialization

Use nested braces when initializing nested structs to avoid ambiguity.

```c
struct outer o = {
    .inner = {
        .a = 1,
        .b = 2
    }
};
```

### 23. Function Parameter Passing

If a function needs to modify already allocated memory (structs, arrays), pass a pointer directly.

If a function needs to output an address that does not exist before the call, prefer using the return value.

If the return value cannot be used, pass a double pointer to retrieve it. The formal parameter inside the function is newly allocated and receives a copy of the passed argument by value. To obtain an address generated inside the function through a pointer, you need to pass a double pointer to modify the address of the pointer variable itself.