## Tour 3

### Variables

> A variable is a storage location, with a specific type and an associated name.

```go
package main

import "fmt"

func main() {
  var x string = "Hello, World"
  fmt.Println(x)
}
```

> Variables in Go are created by first using the var keyword, then specifying the variable name (x) and the type (string), and finally, assigning a value to the vari‐ able (Hello, World). Assigning a value is optional, so we could use two statements, like this:

```go
package main

import "fmt"

func main() {
  var x string
  x = "Hello GO"
  fmt.Println(x)
}
```

> Variables in Go are similar to variables in algebra, but there are some subtle differ‐ ences. First, when we see the = symbol, we have a tendency to read that as “x equals the string Hello, World.” There’s nothing wrong with reading our program that way, but it’s better to read it as “x takes the string Hello, World” or “x is assigned the string Hello, World.” This distinction is important **because (as their name would suggest) variables can change their value throughout the lifetime of a program**.

> Another difference between Go and algebra is that we use a different symbol for equality: == (two equals signs next to each other). == is an operator like + and it returns a boolean. 

#### Name a variable

> Naming a variable properly is an important part of software development. Names must start with a letter and may contain letters, numbers, or the underscore symbol (_). The Go compiler doesn’t care what you name a variable, but you should choose names that clearly describe the variable’s purpose. 

#### Scope

```go
package main

import "fmt"

func main() {
  var x string = "Hello, World"
  fmt.Println(x)
}
```

Another way of writing this program would be like this:

```go
package main

import "fmt"

var x string = "Hello, World"

func main() {
  fmt.Println(x)
}
```

> Notice that we moved the variable outside of the main function. This means that other functions can access this variable:

```go
func main() {
	var x string = "Hello, World" 
  fmt.Println(x)
}

func f() { 
  fmt.Println(x)
}
```

> The compiler is telling you that the x variable inside of the f function doesn’t exist. It only exists inside of the main function. The range of places where you are allowed to use x is called the scope of the variable. According to the language specification, “Go is lexically scoped using blocks.” Basically, this means that the variable exists within the nearest curly braces ({}), or block, including any nested curly braces (blocks), but not outside of them. 

#### Constants

> Go also has support for constants. Constants are essentially variables whose values cannot be changed later. They are created in the same way you create variables, but instead of using the var keyword we use the const keyword:

```go
package main

import "fmt"

func main() {
  const x string = "Hello, World"
  fmt.Println(x)
}
```

Constants are a good way to reuse common values in a program without writing them out each time. For example, Pi in the math package is defined as a constant.

#### Defining Multiple Variables

Go also has another shorthand when you need to define multiple variables:

```go
var {
  a = 5
  b = 10
  c = 15
}
```

Use the keyword *var (or const)* followed by parentheses with each variable on its own line.

#### An Example Program

```go
package main

import "fmt"

func main() {
  fmt.Println("Enter a number: ")
  var input float64
  fmt.Scanf("%f", &input)
  
  output := input * 2
  
  fmt.Println(output)
}
```

### Reference

Introducing Go Build Reliable, Scalable Programs, Caleb Doxsey, 2016