## Tour 2

### Types

> Philosophers sometimes make a distinction between types and tokens. For example, suppose you have a dog named Max. *Max* is the token (a particular instance or  ‐ ber) and *dog* is the type (the general concept). *Dog* or *dogness* describes a set of prop‐ erties that all dogs have in common. Although oversimplistic, we might reason like this: all dogs have four legs, Max is a dog, therefore Max has four legs. Types in pro‐ gramming languages work in a similar way: all strings have a length, *x* is a string, therefore *x* has a length.

*Type: the general concept*

> ***Go is a statically typed programming language. This means that variables always have a specific type and that type cannot change.*** Static typing may seem cumbersome at first. You’ll spend a large amount of your time just trying to fix your program so that it finally compiles. But types help us reason about what our program is doing and catch a wide variety of common mistakes.

#### Numbers

1. Integers

> Go’s integer types are **uint8, uint16, uint32, uint64, int8, int16, int32, and int64**. 8, 16, 32, and 64 tell us how many bits each of the types use. uint means “unsigned integer” while int means “signed integer.” Unsigned integers only contain positive numbers (or zero). In addition, there two alias types: byte (which is the same as uint8) and rune (which is the same as int32). Bytes are an extremely common unit of measurement used on computers (1 byte = 8 bits, 1,024 bytes = 1 kilobyte, 1,024 kilobytes = 1 megabyte, etc.) and therefore Go’s byte data type is often used in the definition of other types. There are also three machine dependent integer types: uint, int, and uintptr. They are machine dependent because their size depends on the type of architecture you are using.

2. Floating-number Numbers

> Floating-point numbers are numbers that contain a decimal component (i.e., real numbers). For example, 1.234, 123.4, 0.00001234, and 12340000 are all floating-point numbers. Their actual representation on a computer is fairly complicated and it’s not really necessary to know the particulars in order to use them. 

- Floating-point numbers are **inexact**. Occasionally it is not possible to represent a number. For example, computing 1.01 – 0.99 using floating-point arithmetic results in 0.020000000000000018—a number extremely close to what we would expect, but not exactly the same.

- Like integers, floating-point numbers have a certain size (32 bit or 64 bit). **Using a larger-sized floating-point number increases its precision** (i.e., how many digits it can represent).

- In addition to numbers, there are several other values that can be represented: **“not a number” (NaN, for things like 0/0)** and **positive and negative infinity (+∞ and −∞).**

Go has two floating-point types: float32 and float64 (also often referred to as single precision and double precision, respectively). It also has two additional types for rep‐ resenting complex numbers (numbers with imaginary parts): complex64 and complex128. **Generally, we should stick with float64 when working with floating- point numbers.**

Go supports the following standard arithmetic operators:

- Addition +
- Substraction -
- Multiplication *
- Division /
- Remainder %

#### String

> A string is a sequence of characters with a definite length used to represent text. Go strings are made up of individual bytes, usually one for each character (characters from some languages, such as Chinese, are represented by more than one byte).
>
> String literals can be created using double quotes "Hello, World" or backticks `Hello, World`. *The difference between these is that double-quoted strings cannot contain newlines and they allow special escape sequences. For example, \n gets replaced with a newline and \t gets replaced with a tab character.*

*``不会对转义字符进行转义*

The following are some common operations on strings:

- Finds the length of a string

  - ```go
    len("Hello, World")
    ```

- Accesses a particular character in the string (in this case, the second character)

  - ```go
    "Hello, World"[1]
    ```

- Concatenates two strings together

  - ```go
    "Hello, " + "World"
    ```

A few things to notice:

- *A space is also considered a character*, so the string’s length is 12, not 11.
- Strings are *indexed* starting at 0, not 1. [1] gives you the second element, not the first. *Also notice that you see 101 instead of e when you run this program. This is because the character is represented by a byte (remember a byte is an integer).*
- Concatenation uses the same symbol as addition. The Go compiler figures out what to do based on the types of the arguments. Because both sides of the + are strings, the compiler assumes you mean concatenation and not addition (addition is meaningless for strings).

Strings and numbers are both extremely useful. Most programs either represent mathematical formulas or involve the manipulation and transformation of data, which is usually represented as a string (or a byte slice, as we will see later).

#### Booleans

> A boolean value (named after George Boole) is a special 1-bit integer type used to represent true and false (or on and off). Three logical operators are used with boolean values:

```go
&& and
|| or
!  not
```

#### Go pointers

> Go supports **pointers**, which are memory addresses that offer improved speed in exchange for difficult-to-debug code and nasty bugs.
>
> When working with pointers, **you need * to get the value of a pointer**, which is called **dereferencing the pointer**, and **& to get the memory address of a non-pointer variable.**

**：获取指针指向的那个值；&：获取非指针变量的内存地址*

> Tips: Generally speaking, amateur developers should use pointers only when the libraries they use require it because pointers can be the cause of horrible and difficult-to-discover bugs when used carelessly.

**Please note that strings in Go are value types not pointers as is the case in C.**

There are two main reasons for using pointers in your programs:

- Pointers allow you to share data, especially between Go functions.
- Pointers can be extremely useful when you want to differentiate between a zero value and a value that is not set.

### Reference

Mastering Go Create Golang production applications using network libraries, concurrency, machine learning, and advanced data structures, Mihalis Tsoukalos, 2019

Introducing Go Build Reliable, Scalable Programs, Caleb Doxsey, 2016