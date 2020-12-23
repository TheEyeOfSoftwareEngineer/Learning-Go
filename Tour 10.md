## Tour 10

### **Error handling**

Errors and error handling are two very important Go topics. Go likes error messages so much that it has a separate data type for errors, named error. This also means that you can easily create your own **error messages** if you find that what Go gives you is not adequate.

You will most likely need to create and handle your own errors when you are developing your own Go packages.

Please note that having an error condition is one thing, but deciding how to react to an error condition is a totally different thing. Putting it simply, not all error conditions are created equal, which means that s**ome error conditions might require that you immediately stop the execution of a program, whereas other error situations might require printing a warning message for the user to see while continuing the execution of the program**. It is up to the developer to use common sense and decide what to do with each error value the program might get.

**Errors in Go are not like exceptions or errors in other programming languages**; they are normal Go objects that get returned from functions or methods just like any other value.

#### **The error data type**

There are many scenarios where you might end up having to deal with a new error case while you are developing your own Go application. The error data type is here to help you to define your own errors.

This subsection will teach you how to create your own error variables. As you will see, in order to create a new error variable, you will need to call the New() function of the errors standard Go package.

The example Go code to illustrate this process can be found in newError.go and will be presented in two parts. The first part of the program is as follows:

```go
package main
import (
  "errors"
	"fmt" 
)

func returnError(a, b int) error {
  if a == b {
    err := errors.New("Error in returnError() function!")
    return err
  } else {
    return nil
	} 
}
```

you can see the errors.New() function in action, which takes a string value as its parameter. If a function should return an error variable but there is not an error to report, it returns nil instead.

The second part of newError.go is the following:

```go
func main() {
  err := returnError(1, 2)
  if err == nil {
    fmt.Println("returnError() ended normally!")
  } else {
    fmt.Println(err)
  }
  err = returnError(10, 10)
  if err == nil {
    fmt.Println("returnError() ended normally!")
  } else {
    fmt.Println(err)
  }
  if err.Error() == "Error in returnError() function!" {
    fmt.Println("!!")
  }
}
```

As the code illustrates, most of the time, you need to check whether an error variable is equal to nil and then act accordingly. What is also presented here is the use of the **errors.Error() function, which allows you to convert an error variable into a string variable.** **This function lets you compare an error variable with a string variable.**

It is considered good practice to send your error messages to the logging service of your UNIX machine, especially when a Go program is a server or some other critical application. 

Executing newError.go will produce the following output:

```go
$ go run newError.go
returnError() ended normally!
Error in returnError() function!
!!
```

If you try to compare an error variable with a string variable without first converting the error variable to a string variable, the Go compiler will create the following error message:

```go
# command-line-arguments
./newError.go:33:9: invalid operation: err == "Error in returnError()
function!" (mismatched types error and string)
```

#### **Error handling**

**Error handling** in a very important feature of Go because almost all Go functions return an error message or nil, which is the Go way of saying whether there was an error condition while executing a function. You will most likely get tired of seeing the following Go code not only in this book but also in every other Go program you can find on the internet:

这么设计估计也是对于nil的忌惮

```go
if err != nil {
  fmt.Println(err)
  os.Exit(10)
}
```

Please do not confuse error handling with printing to error output because they are two totally different things. The former has to do with Go code that handles error conditions, whereas the latter has to do with writing something to the standard error file descriptor.

The preceding code prints the generated error message on the screen and exits your program using os.Exit(). **Please note that you can also exit your program by calling the return keyword inside the main() function. Generally speaking, calling os.Exit() from a function other than main() is considered a bad practice**. Functions other than main() tend to return the error message before exiting, which is handled by the calling function.

Should you wish to send the error message to the logging service instead of the screen, you should use the following variation of the preceding Go code:

```go
if err != nil {
  log.Println(err)
  os.Exit(10)
}
```

Lastly, there is another variation of the preceding code that is used when something really bad has happened and you want to terminate the program:

```go
if err != nil {
  panic(err)
  os.Exit(10)
}
```

**Panic** is a built-in Go function that stops the execution of a program and starts panicking! **If you find yourself using panic too often, you might want to reconsider your Go implementation**. People tend to avoid panic situations in favor of errors wherever possible.

Panic终止程序的运行

Go also offers the **recover** function, which might be able to save you from some bad situations. 

It is now time to see a Go program that **not only handles error messages generated by standard Go functions, but also defines its own error message**. The name of the program is errors.go and it will be presented to you in five parts. As you will see, the errors.go utility tries to improve the functionality of the cla.go program you saw earlier in this chapter by examining whether its command-line arguments are acceptable floats.

The first part of the program is as follows:

```go
package main
import (
  "errors"
  "fmt"
  "os"
  "strconv"
)
```

This part of errors.go contains the expected import statements.

The second portion of errors.go comes with the following Go code:

```go
func main() {
  if len(os.Args) == 1 {
    fmt.Println("Please give one or more floats.")
    os.Exit(1) 
  }
  arguments := os.Args
  var err error = errors.New("An error")
  k := 1
  var n float64
```

Here, you create a new error variable named err in order to initialize it with your own value.

The third part of the program is as follows:

```go
for err != nil {
  if k >= len(arguments) {
    fmt.Println("None of the arguments is a float!")
    return
  }
  n, err = strconv.ParseFloat(arguments[k], 64) // string转换成float64
  k++
}

min, max := n, n
```

This is the trickiest part of the program because if the first command-line argument is not a proper float, you will need to check the next one and keep checking until you find a suitable command-line argument. If none of the command-line arguments are in the correct format, errors.go will terminate and print a message on the screen. All this checking happens by examining the error value that is returned by strconv.ParseFloat(). All this code is just for the accurate initialization of the min and max variables.

The fourth portion of the program comes with the following Go code:

```go
for i := 2; i < len(arguments); i++ {
  n, err := strconv.ParseFloat(arguments[i], 64)
  if err == nil {
    if n < min {
      min = n 
    }
    if n > max {
      max = n
    }
  }
}
```

Here, you just process all the right command-line arguments in order to find the minimum and maximum floats among them.

Finally, the last code portion of the program deals with just printing out the current values of the min and max variables:

```go
	fmt.Println("Min:", min)
	fmt.Println("Max:", max)
}
```

As you can see from the Go code of errors.go, most of its code is about error handling rather than about the actual functionality of the program. Unfortunately, this is the case with most modern software developed in Go, as well as most other programming languages.

If you execute errors.go, you will get the following kind of output:

```go
$ go run errors.go a b c
None of the arguments is a float!
$ go run errors.go b c 1 2 3 c -1 100 -200 a
Min: -200
Max: 100
```

## Reference

Mastering Go Create Golang production applications using network libraries, concurrency, machine learning, and advanced data structures, Mihalis Tsoukalos, 2019

