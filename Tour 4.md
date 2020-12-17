## Tour 4

### The for Statement

> The for statement allows us to repeat a list of statements (a block) multiple times.

```go
package main

import "fmt"

func main() {
  i := 1
  for i <= 10 {
    fmt.Println(i)
    i = i + 1
  }
}
```

1. We evaluate (run) the expression i <= 10 (“i less than or equal to 10”). If this evaluates to true, then we run the statements inside of the block. Otherwise, we jump to the next line of our program after the block (in this case, there is nothing after the for loop, so we exit the program).
2. After we run the statements inside of the block, we loop back to the beginning of the for statement and repeat step 1.

> Other programming languages have a lot of different types of loops (while, do, until, foreach, ...) but Go only has one that can be used in a variety of different ways.

The previous program could also have been written like this:

```go
package main

import "fmt"

func main() {
  for i:=1; i <= 10; i++ {
    fmt.Println(i)
  }
}
```

> Now the conditional expression also contains two other statements with semicolons between them. First, we have the variable initialization, then we have the condition to check each time, and finally, we *increment* the variable. Adding 1 to a variable is so common that we have a special operator (++); similarly, subtracting 1 can be done with --.

### The if statement

- If-else

```go
if i%2 == 0 {
  //even
} else {
  //odd
}
```

- if-else if

```go
if i%2 == 0 {
  //divided by 2
} else if  i%3 == 0 {
  //divided by 3
} else if i%4 == 0 {
  //divided by 4
}
```

```go
func main() {
  for i:=1; i <= 10; i++ {
    if i%2 == 0 {
    	fmt.Println(i, "even")  
    } else {
      fmt.Println(i, "odd")  
    }
    
  }
}
```

### The switch statement

```go
switch i {
	case 0: fmt.Println("Zero")
	case 1: fmt.Println("One")
	case 2: fmt.Println("Two")
	case 3: fmt.Println("Three")
	case 4: fmt.Println("Four")
	case 5: fmt.Println("Five")
	default: fmt.Println("Unknown Number") 
}

```

> Like an if statement, each case is checked top down and the first one to succeed is chosen. A switch also supports a default case that will happen if none of the cases matches the value (similar to how the else works in an if statement).

for, if, and switch are the main control flow statements. 

## Reference

Introducing Go Build Reliable, Scalable Programs, Caleb Doxsey, 2016