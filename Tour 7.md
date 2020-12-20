## Tour 7

### Function

A function (also known as a procedure or a subroutine) is an independent section of code that maps zero or more input parameters to zero or more output parameters. 

```go
func main() {
	xs := []float64{98,93,77,82,83}
	total := 0.0 
  for_,v := range xs{
		total += v }
    fmt.Println(total / float64(len(xs)))
  }
}
```

This program computes the average of a series of numbers. Finding the average like this is a very general problem, so it’s an ideal candidate for definition as a function.

The average function will need to take in a slice of float64s and return one float64. Insert this before the main function:

```go
func average(xs []float64) float64 { 
  panic("Not Implemented")
}
```

Functions start with the keyword func, followed by the function’s name. The parame‐ ters (inputs) of the function are defined like this: name type, name type, .... Our function has one parameter (the list of scores) that we named xs. After the parame‐ ters, we put the return type. Collectively, the parameters and the return type are known as the function’s **signature**.

Finally, we have the function body, which is a series of statements between curly braces. In this body, we invoke a built-in function called panic that causes a runtime error.

Writing functions can be difficult, so **it’s a good idea to break the process into manageable chunks, rather than try‐ ing to implement the entire thing in one large step.**

> Functions are an important element of every programming language because they allow you to break big programs into smaller and more manageable parts. Functions must be as independent from each other as possible and must do one job and only one job well. So, if you find yourself writing functions that do multiple things, you might consider replacing them with multiple functions instead.

```go
func average(xs []float64) float64 { 
  total := 0.0
	for_,v := range xs{ 
    total += v
	}
	return total / float64(len(xs)) 
}
```

Notice that we changed the fmt.Println to be a return instead. The return state‐ ment causes the function to immediately stop and return the value after it to the function that called this one. 

```go
func main() {
	xs := []float64{98,93,77,82,83} 
  fmt.Println(average(xs))
}
```

A few things to keep in mind:

- *Parameter names can be* *different*

The calling and callee functions are allowed to use different names for the parameters. For example, we could have done this:

```go
func main() {
	someOtherName := []float64{98,93,77,82,83}
  fmt.Println(average(someOtherName))
}
```

- *Variables must be passed to functions*

Functions don’t have access to anything in the calling function unless it’s passed in explicitly. This won’t work:

```go
func f() { 
  fmt.Println(x)
}
func main() { 
  x:=5
  f() 
}
```

We need to either do this:

```go
func f(x int) { 
  fmt.Println(x)
}
func main() { 
  x:=5
	f(x) 
}
```

Or this:

```go
var x int=5 
func f() {
   fmt.Println(x)
}
func main() { 
  f()
}
```

- *Functions form call stacks*

Functions are built up in a call *stack*. Suppose we had this program:

```go
func main() { 
  fmt.Println(f1())
}
func f1() int { 
  return f2()
}
func f2() int { 
  return 1
}
```

Each time we call a function, we push it onto the call stack, and each time we return from a function, we pop the last function off of the stack.

- *Return types can have names*

We can name the return type like this:

```go
func f2() (r int) { 
  r=1
	return
}

```

- *Multiple values can be returned*

Go is also capable of returning multiple values from a function. Here is an example function that returns two integers:

```go
func f() (int, int) { 
  return 5, 6
}
func main() { 
  x,y := f()
}
```

Three changes are necessary: change the return type to contain multiple types separated by a comma, change the expression after the return so that it contains multiple comma-separated expressions, and finally, change the assignment statement so that multiple values are on the left side of the := or =.

**Multiple values are often used to return an error value along with the result `(x, err := f())`, or a boolean to indicate success `(x, ok := f())`.**

这个设计挺好的

### Variadic Functions

There is a special form available for the last parameter in a Go function:

```go
func add(args ...int) int {  // args ...int 变长参数 类似Kotlin中的vararg
  total := 0
	for_,v:= range args { 
    total += v
	}
  return total 
}
func main() { 
  fmt.Println(add(1,2,3))
}
```

In this example, add is allowed to be called with multiple integers. This is known as a *variadic parameter*.

By using an ellipsis (...) before the type name of the last parameter, you can indicate that it takes zero or more of those parameters. In this case, we take zero or more ints. We invoke the function like any other function except we can pass as many ints as we want.

This is precisely how the fmt.Println function is implemented:

```kotlin
func Println(a ...interface{}) (n int, err error)
```

The Println function takes any number of values of any type.

We can also pass a slice of ints by following the slice with an ellipsis:

```go
func main() {
	xs := []int{1,2,3} 
  fmt.Println(add(xs...))
}
```

### Closoure

**Anonymous functions** can be defined inline without the need for a name and they are usually used for implementing things that require a small amount of code. In Go, a function can return an anonymous function or take an anonymous function as one of its arguments. Additionally, anonymous functions can be attached to Go variables. Note that anonymous functions are also called **closures**, especially in functional programming terminology.

It is possible to create functions inside of functions. Let’s move the add function we saw before inside of main:

```go
func main() {
	add := func(x, y int) int {
		returnx+y 
  }
  fmt.Println(add(1,1))
}
```

add is a local variable that has the type func(int, int) int (a function that takes two ints and returns an int). When you create a local function like this, it also has access to other local variables.

函数就是Type。这种写法就像可计算的变量似的。

```go
func main() { 
  x:=0
	increment := func() int { 
    x++
		return x 
  }
  fmt.Println(increment()) //1
  fmt.Println(increment()) //2
}
```

increment adds 1 to the variable x, which is defined in the main function’s scope. This x variable can be accessed and modified by the increment function. This is why the first time we call increment we see 1 displayed, but the second time we call it we see 2 displayed.

A function like this together with the nonlocal variables it references is known as a closure. In this case, increment and the variable x form the closure.

One way to use closure is by writing a function that returns another function, which when called, can generate a sequence of numbers. For example, here’s how we might generate all the even numbers:

```go
func makeEvenGenerator() func() uint { 
  i := uint(0)
	return func() (ret uint) {
    ret=i
		i+=2
		return
	} 
}

func main() {
	nextEven := makeEvenGenerator() 
  fmt.Println(nextEven()) // 0 
  fmt.Println(nextEven()) // 2 
  fmt.Println(nextEven()) // 4
}
```

makeEvenGenerator returns a function that generates even numbers. Each time it’s called, it adds 2 to the local i variable, which—unlike normal local variables—persists between calls.

### Recursion

Finally, a function is able to call itself. Here is one way to compute the factorial of a number:

```go
func factorial(x uint) uint { 
  if x==0 {
		return 1 
  }
	return x * factorial(x-1) 
}
```

factorial calls itself, which is what makes this function recursive.

Closure and recursion are powerful programming techniques that form the basis of a paradigm known as *functional programming*. Most people will find functional pro-gramming more difficult to understand than an approach based on for loops, if statements, variables, and simple functions.

### defer, panic, and recover

#### defer

Go has a special statement called defer that schedules a function call to be run after the function completes. Consider the following example:

```go
package main
import "fmt"
func first() { 
  fmt.Println("1st")
}
func second() { 
  fmt.Println("2nd")
}
func main() { 
  defer second()
  first() 
}
```

This program prints 1st followed by 2nd. Basically, defer moves the call to second to the end of the function:

```go
func main() { 
  first()
	second() 
}
```

defer is often used **when resources need to be freed in some way**. For example, **when we open a file, we need to make sure to close it later**. With defer:

```go
f, _ := os.Open(filename) 
defer f.Close()
```

This has three advantages:

- It keeps our Close call near our Open call so it’s easier to understand.

- If our function had multiple return statements (perhaps one in an if and one in

  an else), Close will happen before both of them.

- Deferred functions are run even if a runtime panic occurs.

#### panic and recover

Earlier, we created a function that called the panic function to cause a runtime error. We can handle a runtime panic with the built-in recover function. recover stops the panic and returns the value that was passed to the call to panic. We might be tempted to recover from a panic like this:

```go
package main
import "fmt"
func main() { 
  panic("PANIC")
	str := recover() // this will never happen
  fmt.Println(str)
}
```

But the call to recover will never happen in this case, because the call to panic imme‐ diately stops execution of the function. Instead, we have to pair it with defer:

```go
package main
import "fmt"
func main() { 
  defer func() {
    str := recover()
    fmt.Println(str)
   }()
   panic("PANIC")
}
```

A panic generally indicates a programmer error (e.g., attempting to access an index of an array that’s out of bounds, forgetting to initialize a map, etc.) or an exceptional condition that there’s no easy way to recover from (hence the name *panic*).

recover执行后会返回panic里面的参数

### Pointers

When we call a function that takes an argument, that argument is copied to the func‐ tion:

**参数被复制进入了函数**

```go
func zero(x int) { 
  x=0
}
func main() { 
  x:=5
	zero(x)
	fmt.Println(x) // x is still 5 
}
```

In this program, the zero function will not modify the original x variable in the main function. But what if we wanted to? One way to do this is to use a special data type known as a *pointer*:

```go
unc zero(xPtr *int) { 
  *xPtr = 0
}
func main() {
	x:=5
	zero(&x)
	fmt.Println(x) // x is 0
}
```

可以结合C++的指针一起理解

Pointers reference a location in memory where a value is stored rather than the value itself. By using a pointer (*int), the zero function is able to modify the original variable.

#### The * and & operators

In Go, a pointer is represented using an asterisk (*) followed by the type of the stored value. In the zero function, xPtr is a pointer to an int.

An asterisk is also used to *dereference* pointer variables. Dereferencing a pointer gives us access to the value the pointer points to. When we write *xPtr = 0, we are saying “store the int 0 in the memory location xPtr refers to.” If we try xPtr = 0 instead, we will get a compile-time error because xPtr is not an int; it’s a *int, which can only be given another *int

Finally, we use the & operator to find the address of a variable. &x returns a *int (pointer to an int) because x is an int. This is what allows us to modify the original variable. &x in main and xPtr in zero refer to the same memory location.

#### new

Another way to get a pointer is to use the built-in new function:

```go
func one(xPtr *int) { 
  *xPtr = 1
}
func main() {
	xPtr := new(int)
	one(xPtr) 
  fmt.Println(*xPtr) // x is 1
}
```

**new takes a type as an argument, allocates enough memory to fit a value of that type, and returns a *pointer* to it.**

In some programming languages, there is a significant difference between using new and &, with great care being needed to eventually delete anything created with new. You don’t have to worry about this with Go—it’s a garbage-collected programming language, which means memory is cleaned up automatically when nothing refers to it anymore.

Pointers are rarely used with Go’s built-in types but they are extremely useful when paired with structs.

## Reference

Mastering Go Create Golang production applications using network libraries, concurrency, machine learning, and advanced data structures, Mihalis Tsoukalos, 2019

Introducing Go Build Reliable, Scalable Programs, Caleb Doxsey, 2016