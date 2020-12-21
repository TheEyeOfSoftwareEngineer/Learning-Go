## Tour 8

### Structs and Interfaces

Although it would be possible for us to write programs only using Go’s built-in data types, at some point it would become quite tedious. Consider a program that interacts with shapes:

```go
package main
import ("fmt"; "math")

func distance(x1, y1, x2, y2 float64) float64 {
  a:=x2–x1
	b:=y2–y1
	return math.Sqrt(a*a + b*b) 
}

func rectangleArea(x1, y1, x2, y2 float64) float64 { 
  l := distance(x1, y1, x1, y2)
	w := distance(x1, y1, x2, y1)
	return l*w
}

func circleArea(x, y, r float64) float64 { 
  return math.Pi * r*r
}

func main() {
	var rx1, ry1 float64 = 0, 0
	var rx2, ry2 float64 = 10, 10 
  var cx,cy,cr float64=0,0,5
  fmt.Println(rectangleArea(rx1, ry1, rx2, ry2))  // 100
  fmt.Println(circleArea(cx, cy, cr))             // 78.53981633974483
}
```

This program finds the area of a rectangle and a circle. Keeping track of all the coor‐ dinates makes it difficult to see what the program is doing and will likely lead to mis‐ takes.

#### Structs

An easy way to make this program better is to use a struct. **A struct is a type that contains named fields**. For example, we could represent a circle like this:

```go
type Circle struct { 
  x float64
	y float64
	r float64 
}
```

The type keyword introduces a new type. It’s followed by the name of the type (Circle), the keyword struct to indicate that we are defining a struct type, and a list of fields inside of curly braces.

Fields are like a set of grouped variables. Each field has a name and a type and is stored adjacent to the other fields in the struct. Like with functions, we can collapse fields that have the same type:

```go
type Circle struct { 
	x, y, r float64
}

```

##### Initialization

We can create an instance of our new Circle type in a variety of ways:

```go
var c Circle
```

Like with other data types, **this will create a local Circle variable that is by default set to zero.** For a struct, zero means each of the fields is set to their corresponding zero value (**0 for ints, 0.0 for floats, "" for strings, nil for pointers, etc.**) We can also use the new function:

```go
c := new(Circle)
```

This allocates memory for all the fields, sets each of them to their zero value, and returns **a pointer to the struct (*Circle)**. **Pointers are often used with structs so that functions can modify their contents.**

Using new in this way is somewhat uncommon. More typically, we want to give each of the fields an initial value. We can do this in two ways.

```go
// option1 参数名传参
c := Circle{x: 0, y: 0, r: 5}
// option2 位置传参
c := Circle{0, 0, 5}
```

This creates the same Circle as the previous example. If you want a pointer to the

struct, use &:

```go
c := &Circle{0, 0, 5}
```

##### Fields

We can access fields using the . operator:

```go
fmt.Println(c.x, c.y, c.r) 
c.x=10
c.y=5
```

Let’s modify the circleArea function so that it uses a Circle:

```go
func circleArea(c Circle) float64 { 
  return math.Pi * c.r * c.r
}
```

In main, we have:

```go
c := Circle{0, 0, 5}
fmt.Println(circleArea(c))
```

**One thing to remember is that arguments are always copied in Go**. If we attempted to modify one of the fields inside of the circleArea function, it would not modify the original variable. **Because of this, we would typically write the function using a pointer to the Circle:**

```go
func circleArea(c *Circle) float64 { 
  return math.Pi * c.r * c.r
}
```

And change main to use & before c:

```go
c := Circle{0, 0, 5}
fmt.Println(circleArea(&c))
```

##### Methods

Although this is better than the first version of this code, we can improve it significantly by using a special type of function known as a *method*:

```go
func (c *Circle) area() float64 { 
  return math.Pi * c.r * c.r
}
```

这种写法还挺奇怪的

In between the keyword func and the name of the function, we’ve added a *receiver*. The receiver is like a parameter—it has a name and a type—but by creating the function in this way, it allows us to call the function using the . operator:

receiver就是类型的实例

```go
fmt.Println(c.area())
```

This is much easier to read. **We no longer need the & operator (*Go automatically knows to pass a pointer to the circle for this method*)**, and *because this function can only be used with Circles, we can rename the function to just area*.

Let’s do the same thing for the rectangle:

```go
type Rectangle struct { 
  x1, y1, x2, y2 float64
}

func (r *Rectangle) area() float64 {
	l := distance(r.x1, r.y1, r.x1, r.y2) 
  w := distance(r.x1, r.y1, r.x2, r.y1) 
  return l*w
}
```

main has:

```go
r := Rectangle{0, 0, 10, 10}
fmt.Println(r.area())
```

##### Embedded Types

A struct’s fields usually represent the has-a relationship (e.g., a Circle has a radius). The following snippet shows an example of a person struct:

```go
type Person struct { 
  Name string
}
func (p *Person) Talk() { 
  fmt.Println("Hi, my name is", p.Name)
}
```

Now suppose we wanted to create a new Android struct. We could do this:

```go
type Android struct { 
  Person Person
  Model string 
}
```

**This would work, but we would rather say an android *is* a person, rather than an android *has* a person.** Go supports relationships like this by using embedded types (sometimes also referred to as anonymous fields)—they look like this:

```go
type Android struct { 
  Person
	Model string 
}
```

We use the type (Person) and don’t give it a name. When defined this way, the Person struct can be accessed using the type name:

```go
a := new(Android)
a.Person.Talk()
```

But we can also call any Person methods directly on the Android:

```go
a := new(Android)
a.Talk()
```

The is-a relationship works this way intuitively: people can talk, an android is a person, therefore an android can talk.

这种写法还是比较奇怪

#### Interfaces

You may have noticed that we were able to name the Rectangle’s area method the same thing as the Circle’s area method. This was no accident. In both real life and in programming, relationships like these are commonplace. Go has a way of making these accidental similarities explicit through a type known as an interface. Here is an example of a Shape interface:

```go
type Shape interface { 
  area() float64
}
```

**Like a struct, an interface is created using the type keyword, followed by a name and the keyword interface.** But **instead of defining fields, we define a *method set*.** **A method set is a list of methods that a type must have in order to *implement* the interface.**

In our case, both Rectangle and Circle have area methods that return float64s, so both types implement the Shape interface. By itself, this wouldn’t be particularly useful, but we can use interface types as arguments to functions.

Suppose we want to write a function that calculates the area of several shapes. Using the techniques we’ve discussed so far, we might start to write the function like this:

```go
func totalArea(circles ...Circle) float64 { 
  var total float64
	for _, c := range circles {
    total += c.area()
  }
	return total 
}
```

And then we’d try to add in Rectangles:

```go
// THIS IS INVALID
func totalArea(circles ...Circle, rectangles ...Rectangle) float64 { 
  var total float64
	for _, c := range circles {
    total += c.area()
  }
	for _, r := range rectangles { 
    total += r.area()
	}
	return total 
}
```

But we can’t write a function that contains two variadic parameters, so we would have to modify the program:

```go
func totalArea(circles []Circle, rectangles []Rectangle) float64 { 
  var total float64
	for _, c := range circles {
    total += c.area()
  }
	for _, r := range rectangles { 
    total += r.area()
  }
  return total 
}
```

This works, but it has a major issue—whenever we define a new shape, **we have to change our function to handle it (a third parameter for Polygons, a fourth for Squares, etc.).**

所以我们的思路是在目标下，寻找事物的共性。比如我们只关心area那么我们就不需要去在乎field。在这种妥协下我们可以在其他方面获得一些提升。

This is the problem interfaces are designed to solve. Because both of our shapes have an area method, they both implement the Shape interface and we can change our function to this:

```go
func totalArea(shapes ...Shape) float64 { 
  var area float64
  for _, s := range shapes { 
    area += s.area()
	}
  return area 
}
```

We would call this function like this:

```go
fmt.Println(totalArea(&c, &r))
```

All totalArea knows about each shape is that it has an area method:

```go
type Shape interface { 
  area() float64
}
```

So totalArea would not be able to access the struct fields for the circle or rectangle (or any other possible methods).

Nothing additional is required to implement an interface (there is no implements or extends keyword). It’s sufficient to merely have a method with the same name and signature.

**Interfaces can also be used as fields.** Consider a MultiShape that is made up of several smaller shapes:

```go
type MultiShape struct { 
  shapes []Shape
}
```

We can create a MultiShape like this:

```go
multiShape := MultiShape { 
  shapes: []Shape {
    Circle{0, 0, 5},
    Rectangle{0, 0, 10, 10},
  },
}
```

We can even turn MultiShape itself into a Shape by giving it an area method:

```go
func (m *MultiShape) area() float64 { 
  var area float64
	for _, s := range m.shapes {
    area += s.area()
  }
  return area 
}
```

Now a MultiShape can contain Circles, Rectangles, or even other MultiShapes.

When building new programs, you often won’t know what your types should look like when you start—and that’s OK. In Go, you generally focus more on the behavior of your program than on a taxonomy of types. Create a few small structs that do what you want, add in methods that you need, and as you build your program, useful interfaces will tend to emerge. There’s no need to have them all figured out ahead of time.

Interfaces are particularly useful as software projects grow and become more complex. They allow us to hide the incidental details of implementation (e.g., the fields of our struct), which makes it easier to reason about software components in isolation. In our example, as long as the area methods we defined continue to produce the same results, we’re free to change how a Circle or Rectangle is structured without having to worry about whether or not the totalArea function will continue to work.

Go also has a mechanism for combining interfaces, types, variables, and functions together into a single component known as a *package*. 

## Reference

Introducing Go Build Reliable, Scalable Programs, Caleb Doxsey, 2016