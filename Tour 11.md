## Tour 11

### Reflection

#### **Type methods**

A Go type method is a function with a special receiver argument. You declare methods as ordinary functions with an additional parameter that appears in front of the function name. This particular parameter connects the function to the type of that extra parameter. As a result, that parameter is called the **receiver of the method**.

```go
func (f *File) Close() error {
  if err := f.checkValid("close"); err != nil {
    return err 
  }
  return f.file.close()
}
```

The Close() function is a type method because there is that (f *File) parameter in front of its name and after the func keyword. The f parameter is called the receiver of the method. In object-oriented programming terminology this process can be described as sending a message to an **object**. In Go, the receiver of a method is defined using a regular variable name without the need to use a dedicated keyword such as this or self.

这还是之间学习过的内容就是给struct添加方法

Now let me offer you a complete example using the Go code of the methods.go file, which will be presented in four parts.

The first part of methods.go comes with the following Go code:

```go
package main
import (
  "fmt"
)
type twoInts struct {
  X int64
  Y int64 
}
```

In the preceding Go code, you can see the definition of a new structure with two fields named twoInts.

The second code segment of methods.go is next:

```go
func regularFunction(a, b twoInts) twoInts {
  temp := twoInts{X: a.X + b.X, Y: a.Y + b.Y}
  return temp
}
```

In this part, you define a new function named regularFunction() that accepts two

parameters of type twoInts and returns just one twoInts value.

The third part of the program contains the next Go code:

```go
func (a twoInts) method(b twoInts) twoInts {
  temp := twoInts{X: a.X + b.X, Y: a.Y + b.Y}
  return temp
}
```

The method() function is equivalent to the regularFunction() function defined in the previous part of methods.go. However, the method() function is a type method and you are going to learn a different way of calling it in a moment.

上面的定义和之前的写法效果是一样的，但是化身为了Type method。但是这种写法从语意上比较难理解。

The really interesting thing here is that the implementation of method() is exactly the same as the implementation of regularFunction()!

The last code segment of methods.go is the following:

```go
func main() {
  i := twoInts{X: 1, Y: 2}
  j := twoInts{X: -5, Y: -2}
  fmt.Println(regularFunction(i, j))
  fmt.Println(i.method(j))
}
```

看到这里这种写法就比较好理解了。

As you can see, the way you call a type method (i.method(j)) is different from the way you call a conventional function (regularFunction(i, j)).

Executing methods.go will create the next output:

```
$ go run methods.go
{-4 0}
{-4 0}
```

Notice that type methods are also associated with interfaces, which will be the subject of the next section. As a result, you will see more type methods later.

#### **Go interfaces**

Strictly speaking, a Go interface type defines the behavior of other types by specifying a set of methods that need to be implemented. For a type to satisfy an interface, it needs to implement all the methods required by that interface, which are usually not too many.

Putting it simply, interfaces are **abstract types** that define a set of functions that need to be implemented so that a type can be considered an instance of the interface. When this happens, we say that the type satisfies this interface. So, an interface is two things: **a set of methods and a type**, and it is used to define the behavior of other types.

The biggest advantage you get from having and using an interface is that you can pass a variable of a type that implements that particular interface to any function that expects a parameter of that specific interface. Without that amazing capability, interfaces would have been only a formality without any practical or real benefit.

Two very common Go interfaces are io.Reader and io.Writer and they are used in file input and output operations. More specifically, io.Reader is used for reading from a file, whereas io.Writer is used for writing to a file of any type.

The definition of io.Reader as found in https://golang.org/src/io/io.go is the following:

```go
type Reader interface {
  Read(p []byte) (n int, err error)
}
```

So, in order for a type to satisfy the io.Reader interface, you will need to implement the Read() method as described in the interface definition.

Similarly, the definition of io.Writer as found in https://golang.org/src/io/io.go is next:

```go
type Writer interface {
  Write(p []byte) (n int, err error)
}
```

To satisfy the io.Writer interface, you will just need to implement a single method named Write().

Each of the io.Reader and io.Writer interfaces requires the implementation of just one method. Yet both interfaces are very powerful – most likely their power comes from their simplicity. Generally speaking, most interfaces are fairly simple.

Putting it simply, interfaces should be utilized when there is a need for making sure that certain conditions will be met and certain behaviors will be anticipated from a Go element

##### **About type assertions**

A type assertion is the x.(T) notation, where x is an interface type and T is a type. Additionally, the actual value stored in x is of type T and T must satisfy the interface type of x. The following paragraphs, as well as the code example, will help you to clarify this relatively eccentric definition of a type assertion.

Type assertions help you to do *two things*. **The first thing is checking whether an interface value keeps a particular type.** When used this way, a type assertion returns two values: the underlying value and a bool value. Although the underlying value is what you might want to use, the Boolean value tells you whether the type assertion was successful or not.

**The second thing that type assertions help with is allowing you to use the concrete value stored in an interface or assign it to a new variable.** This means that if there is an int variable in an interface, you can get that value using a type assertion.

However, if a type assertion is not successful and you do not handle that failure on your own, your program will panic. Look at the Go code of the assertion.go program, which will be presented in two parts. The first part contains the following Go code:

```go
package main
import (
	"fmt"
)
func main() {
  var myInt interface{} = 123
  k, ok := myInt.(int)
  if ok {
    fmt.Println("Success:", k)
  }
  v, ok := myInt.(float64)
  if ok {
    fmt.Println(v)
  } else {
    fmt.Println("Failed without panicking!")
  }
```

判断成功则返回对应的值

First, you declare the myInt variable that has a dynamic type int and value 123. Then, you use a type assertion twice to testing the interface of the myInt variable – once for int and once for float64.

As the myInt variable does not contain a float64 value, the myInt.(float64) type assertion will fail unless handled properly. Fortunately, in this case, the correct use of the ok variable will save your program from panicking.

The second part comes with the next Go code:

```go
	i := myInt.(int)
	fmt.Println("No checking:", i)
	j := myInt.(bool)
	fmt.Println(j)
}
```

There are two type assertions taking place here. The first type assertion is successful, so there will be no problem with that. But let me talk a little bit more about this particular type assertion. The type of variable i will be int and its value will be 123, which is the value stored in myInt. So, as int satisfies the myInt interface, which in this case happens because the myInt interface requires no functions to be implemented, the value of myInt.(int) is an int value.

However, the second type assertion, which is myInt.(bool), will trigger a panic because the underlying value of myInt is not Boolean (bool).

如果有成功与否的判断就不触发panic否则将触发

Therefore, executing assertion.go will generate the following output:

```
$ go run assertion.go
Success: 123
Failed without panicking!
No cheking: 123
panic: interface conversion: interface {} is int, not bool
goroutine 1 [running]:
main.main()
/Users/mtsouk/Desktop/mGo2nd/Mastering-Go-Second-
Edition/ch07/assertion.go:25 +0x1c1
exit status 2
```

Go states pretty clearly the reason for panicking: interface {} is int, not bool.

Generally speaking, when using interfaces, expect to use type assertions as well. You will see more type assertions in the useInterface.go program coming up.

#### **Writing your own interfaces**

In this section, you will learn how to develop your own interfaces, which is a relatively easy process as long as you know what you want to develop.

The technique is going to be illustrated using the Go code of myInterface.go, which will be presented shortly. The interface that is going to be created will help you to work with geometric shapes of the plane.

The Go code of myInterface.go is next:

```go
package myInterface
type Shape interface {
  Area() float64
  Perimeter() float64
}
```

The definition of the shape interface is truly straightforward as it requires that you implement just two functions, named Area() and Perimeter(), that both return a float64 value. The first function will be used to calculate the area of a shape in the plane and the second one to calculate the perimeter of a shape in the plane.

After that, you will need to install the myInterface.go package and make it available to the current user. As you already know, the installation process involves the execution of the following UNIX commands:

```
$ mkdir ~/go/src/myInterface
$ cp myInterface.go ~/go/src/myInterface
$ go install myInterface
```

需要给外部文件编译

##### **Using a Go interface**

This subsection will teach you how to use the interface defined in myInterface.go in a Go program named useInterface.go, which will be presented in five parts.

The first part of useInterface.go comes with the next Go code:

```go
package main
import (
  "fmt"
  "math"
  "myInterface"
)
type square struct {
  X float64
}
type circle struct {
  R float64
}
```

As the desired interface is defined in its own package, it should come as no surprise that you are importing the myInterface package.

The second code portion from useInterface.go contains the following code:

```go
func (s square) Area() float64 {
  return s.X * s.X
}
func (s square) Perimeter() float64 {
  return 4 * s.X
}
```

In this part, you implement the shape interface for the square type.

The third part contains the next Go code:

```go
func (s circle) Area() float64 {
  return s.R * s.R * math.Pi
}
func (s circle) Perimeter() float64 {
  return 2 * s.R * math.Pi
}
```

In this part, you implement the shape interface for the circle type.

The fourth part of useInterface.go comes with the following Go code:

```go
func Calculate(x myInterface.Shape) {
  _, ok := x.(circle)
  if ok {
    fmt.Println("Is a circle!")
  }
  v, ok := x.(square)
  if ok {
    fmt.Println("Is a square:", v)
  }
  fmt.Println(x.Area())
  fmt.Println(x.Perimeter())
}
```

So, in the preceding code, you implement one function that requires a single shape parameter (myInterface.Shape). The magic here should be more obvious once you understand that it requires any shape parameter, which is any parameter whose type implements the shape interface.

The code at the beginning of the function shows how you can differentiate between the various data types that implement the desired interface. In the second block, you can see how you can find out the values stored in a square parameter – you can use this technique for any type that implements the myInterface.Shape interface.

The last code segment includes the next code:

```go
func main() {
  x := square{X: 10}
  fmt.Println("Perimeter:", x.Perimeter())
  Calculate(x)
  y := circle{R: 5}
  Calculate(y)
}
```

n this part, you can see how you can use both circle and square variables as parameters to the Calculate() function you implemented earlier.
If you execute useInterface.go, you will get the next output:

```
$ go run useInterface.go
Perimeter: 40
Is a square: {10}
100
40
Is a circle!
78.53981633974483
31.41592653589793
```

#### **Using switch with interface and data types**

In this subsection, you will learn how to use the switch statement to differentiate between different data types using the Go code of switch.go, which will be presented in four parts. The Go code of switch.go is partially based on useInterface.go, but it will add another type named rectangle and will not need to implement the methods of any interface.

The first part of the program is next:

```go
package main
import (
  "fmt"
)
```

As the code in switch.go will not work with the interface defined in myInterface.go, there is no need to import the myInterface package.

The second part is where you define the three new data types that will be used in the program:

```go
type square struct {
  X float64
}
type circle struct {
  R float64
}
type rectangle struct {
  X float64
  Y float64 
}
```

All three types are pretty simple.

The third code segment from switch.go comes with the following Go code:

```go
func tellInterface(x interface{}) {
  switch v := x.(type) {
    case square:
    fmt.Println("This is a square!")
    case circle:
    fmt.Printf("%v is a circle!\n", v)
    case rectangle:
    fmt.Println("This is a rectangle!")
    default:
    fmt.Printf("Unknown type %T!\n", v)
  }
}
```

Here you can see the implementation of a function named tellInterface() with a single parameter named x and type interface{}.

This trick will help you to differentiate between the different data types of the x parameter. All the magic is performed with the use of the x.(type) statement that returns the type of the x element. The %v verb used in fmt.Printf() allows you to acquire the value of the type.

The last part of switch.go contains the implementation of the main() function:

```go
func main() {
  x := circle{R: 10}
  tellInterface(x)
  y := rectangle{X: 4, Y: 1}
  tellInterface(y)
  z := square{X: 4}
  tellInterface(z)
  tellInterface(10)
}
```

Executing switch.go will generate the next kind of output:

```
$ go run switch.go
{10} is a circle!
This is a rectangle!
This is a square!
Unknown type int!
```

#### **Reflection**

**Reflection** is an advanced Go feature that allows you to **dynamically learn the type of an arbitrary object, as well as information about its structure.** Go offers the reflect package for working with reflection. What you should remember is that you will most likely not need to use reflection in every Go program. So, the first two questions are: **why is reflection necessary and when should you use it?**

Reflection is necessary for the implementation of packages such as fmt, text/template, and html/template. **In the fmt package, reflection saves you from having to explicitly deal with every data type that exists. However, even if you had the patience to write code to work with every data type that you know of, you would still not be able to work with all possible types!** In this case, reflection makes it possible for the methods of the fmt package to find the structure and to work with new types.

Therefore, you might need to use reflection when you want to be as generic as possible or when you want to make sure that you will be able to deal with data types that do not exist at the time of writing your code but might exist in the future. Additionally, reflection is handy when working with values of types that do not implement a common interface.

As you can see, reflection helps you to work with unknown types and unknown values of types. However, that flexibility comes at a cost.

The stars of the reflect package are two types named reflect.Value and reflect.Type. The former type is used for storing values of any type, whereas the latter type is used for representing Go types.

确实反射就像是泛型的一种补充。对于输出方法而言，输入的参数可能多种多样，对于每种类型的参数，会对应不同的输出方式。

##### **A simple reflection example**

This section will present you with a relatively simple reflection example in order to help you to feel comfortable with this advanced Go feature.

The name of the Go source file is reflection.go and it will be presented to you in four parts. The purpose of reflection.go is to examine an "unknown" structure variable and find out more about it at runtime. In order to be more interesting, the program will define two new struct types. Based on these two types, it will also define two new variables; however, it will only examine one of them.

If the program has no command-line arguments, it will examine the first one, otherwise it will explore the second one – practically, this means that the program will not know in advance (at runtime) the kind of struct variable it will have to process.

The first part of reflection.go contains the next Go code:

```go
package main
import (
  "fmt"
  "os"
  "reflect" )
type a struct {
  X int
  Y float64
  Z string
}
type b struct {
  F int
  G int
  H string
  I float64
}
```

In this part, you can see the definition of the struct data types that will be used in the program.

The second code segment from reflection.go is the following:

```go
func main() {
  x := 100
  xRefl := reflect.ValueOf(&x).Elem()
  xType := xRefl.Type()
  fmt.Printf("The type of x is %s.\n", xType)
```

The preceding Go code presents a small and naive reflection example. First you declare a variable named x and then you call the reflect.ValueOf(&x).Elem() function. Then you call xRefl.Type() in order to get the type of the variable, which is stored in xType. These three lines of code illustrate how you can get the data type of a variable using reflection. However, if all you care about is the data type of a variable, you can just call reflect.TypeOf(x) instead.

The third code portion from reflection.go contains the next Go code:

```go
  A := a{100, 200.12, "Struct a"}
  B := b{1, 2, "Struct b", -1.2}
  var r reflect.Value
  arguments := os.Args
  if len(arguments) == 1 {
    r = reflect.ValueOf(&A).Elem()
  } else {
    r = reflect.ValueOf(&B).Elem()
  }
```

In this part, you declare two variables named A and B. The type of the A variable is a and the type of the B variable is b. The type of the r variable should be reflect.Value because this is what the reflect.ValueOf() function returns. The Elem() method returns the value contained in the reflection interface (reflect.Value).

The last part of reflection.go is next:

```go
  iType := r.Type()
  fmt.Printf("i Type: %s\n", iType)
  fmt.Printf("The %d fields of %s are:\n", r.NumField(), iType)
  for i := 0; i < r.NumField(); i++ {
    fmt.Printf("Field name: %s ", iType.Field(i).Name)
    fmt.Printf("with type: %s ", r.Field(i).Type())
    fmt.Printf("and value %v\n", r.Field(i).Interface())
  } 
}
```

In this part of the program, you use the appropriate functions of the reflect package in order to obtain the desired information. The NumField() method returns the number of fields in a reflect.Value structure, whereas the Field() function returns the field of the structure that is specified by its parameter. The Interface() function returns the value of a field of the reflect.Value structure as an interface.

Executing reflection.go twice will generate the following output:

```
$ go run reflection.go 1
The type of x is int.
i Type: main.b
The 4 fields of main.b are:
Field name: F with type: int and value 1
Field name: G with type: int and value 2
Field name: H with type: string and value Struct b
Field name: I with type: float64 and value -1.2
$ go run reflection.go
The type of x is int.
i Type: main.a
The 3 fields of main.a are:
Field name: X with type: int and value 100
Field name: Y with type: float64 and value 200.12
Field name: Z with type: string and value Struct a
```

It is important to note that Go uses its internal representation to print the data types of variables A and B, which are main.a and main.b, respectively. However, this is not the case with variable x, which is an int.

我们先用`reflect.ValueOf(&p).Elem()`拿到reflection。然后使用ref.NumField拿到结构中field的个数。使用`ref.Type()`取到类型。`iType.Field(i).Name`获得子field的名称。`r.Field(i).Type()`获得子field的类型。`r.Field(i).Interface()`获得对应的值或者类型。

##### **A more advanced reflection example**

In this section, we are going to see more advanced uses of reflection illustrated in relatively small code blocks using the Go code of advRefl.go.

The advRefl.go program will be presented in five parts – its first part is next:

```go
package main
import (
  "fmt"
  "os"
  "reflect" 
)
type t1 int
type t2 int
```

Note that although both t1 and t2 types are based on int, and therefore are essentially the same type as int, Go treats them as totally different types. Their internal representation after Go parses the code of the program will be main.t1 and main.t2, respectively.

The second code portion from advRefl.go is the following:

```go
type a struct {
  X    int
  Y float64
  Text string 
}
func (a1 a) compareStruct(a2 a) bool {
  r1 := reflect.ValueOf(&a1).Elem()
  r2 := reflect.ValueOf(&a2).Elem()
  for i := 0; i < r1.NumField(); i++ {
    if r1.Field(i).Interface() != r2.Field(i).Interface() {
      return false
    }
  }
  return true 
}
```

这个例子举的好。如果是不同反射我们也许就是直接使用取值对比，但是引入了反射以后我们可以更general的去解决这类问题。

In this code, you define a Go structure type named a and implement a Go function named compareStruct(). The purpose of this function is to find out whether two variables of the a type are exactly the same or not. As you can see, compareStruct() uses Go code from reflection.go to perform its task.

The third code segment of advRefl.go comes with the following Go code:

```go
func printMethods(i interface{}) {
  r := reflect.ValueOf(i)
  t := r.Type()
  fmt.Printf("Type to examine: %s\n", t)
  for j := 0; j < r.NumMethod(); j++ {
    m := r.Method(j).Type()
    fmt.Println(t.Method(j).Name, "-->", m)
  } 
}
```

The printMethods() function prints the methods of a variable. The variable type that will be used in advRefl.go to illustrate printMethods() will be os.File.

The fourth code segment from advRefl.go contains the following Go code:

```go
func main() {
  x1 := t1(100)
  x2 := t2(100)
  fmt.Printf("The type of x1 is %s\n", reflect.TypeOf(x1))
  fmt.Printf("The type of x2 is %s\n", reflect.TypeOf(x2))
  var p struct{}
  r := reflect.New(reflect.ValueOf(&p).Type()).Elem()
  fmt.Printf("The type of r is %s\n", reflect.TypeOf(r))
```

The last code portion of advRefl.go is as follows:

```go
  a1 := a{1, 2.1, "A1"}
  a2 := a{1, -2, "A2"}
  if a1.compareStruct(a1) {
    fmt.Println("Equal!")
  }
  if !a1.compareStruct(a2) {
    fmt.Println("Not Equal!")
  }
  var f *os.File
  printMethods(f)
}
```

As you will see later, the a1.compareStruct(a1) call returns true because we are comparing a1 with itself, whereas the a1.compareStruct(a2) call will return false because the a1 and a2 variables have different values.

Executing advRefl.go will create the following output:

```
$ go run advRefl.go
The type of x1 is main.t1
The type of x2 is main.t2
The type of r is reflect.Value
Equal!
Not Equal!
Type to examine: *os.File
Chdir --> func() error
Chmod --> func(os.FileMode) error
Chown --> func(int, int) error
Close --> func() error
Fd --> func() uintptr
Name --> func() string
Read --> func([]uint8) (int, error)
ReadAt --> func([]uint8, int64) (int, error)
Readdir --> func(int) ([]os.FileInfo, error)
Readdirnames --> func(int) ([]string, error)
Seek --> func(int64, int) (int64, error)
Stat --> func() (os.FileInfo, error)
Sync --> func() error
Truncate --> func(int64) error
Write --> func([]uint8) (int, error)
WriteAt --> func([]uint8, int64) (int, error)
WriteString --> func(string) (int, error)
```

You can see that the type of the r variable, which is returned by reflect.New(), will be reflect.Value. Additionally, the output of the printMethods() method tells us that the *os.File type supports a plethora of methods, such as Chdir() and Chmod()

##### **The three disadvantages of reflection**

Without a doubt, reflection is a powerful Go feature. However, as with all tools, reflection should be used rationally for three main reasons. The first reason is that **extensive use of reflection will make your programs hard to read and maintain.** A potential solution to this problem is good documentation, but developers are famous for not having the time to write the required documentation.

The second reason is that the **Go code that uses reflection will make your programs slower.** Generally speaking, Go code that is made to work with a particular data type will always be faster than Go code that uses reflection to dynamically work with any Go data type. Additionally, such dynamic code will make it difficult for tools to refactor or analyze your code.

The last reason is that **reflection errors cannot be caught at build time and are reported at runtime as panics, which means that reflection errors can potentially crash your programs**. This can happen months or even years after the development of a Go program! One solution to this problem is extensive testing before a dangerous function call. However, this will add even more Go code to your programs, which will make them even slower.

## Reference

Mastering Go Create Golang production applications using network libraries, concurrency, machine learning, and advanced data structures, Mihalis Tsoukalos, 2019