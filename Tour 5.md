## Tour 5

### Arrays, Slices, Maps

#### Arrays

An array is a numbered sequence of elements of a single type with a fixed length. In Go, they look like this:

`var x [5]int` - x is an example of an array that is composed of five ints.

```go
package main

import "fmt"

func main() {
  var x [5]int
  x[4] = 100
  fmt.Println(x) // [0 0 0 0 100]
}
```

 we can make is to use a special form of the for loop:

```go
//会报错 原因是go编译器不允许你创建了一个变量但是不使用
var total float64 = 0
for i, value := range x {
  total += value
}
fmt.Println(total / float64(len(x)))
```

In this for loop, i represents the current position in the array and value is the same as x[i]. We use the keyword range followed by the name of the variable we want to loop over.

*这个range函数反而像Python里面的enumerate函数*

The Go compiler won’t allow you to create variables that you never use. Because we don’t use i inside of our loop, we need to change it to this

```go
var total float64 = 0
for _, value := range x {
  total += value
}
fmt.Println(total / float64(len(x)))
```

A single underscore (_) is used to tell the compiler that we don’t need this (in this case, we don’t need the iterator variable).

Go also provides a shorter syntax for **creating arrays**:

```go
x := [5]float64{ 98, 93, 77, 82, 83 }
```

We no longer need to specify the type because Go can figure it out. Sometimes arrays like this can get too long to fit on one line, so Go allows you to break it up like this:

```go
x := [5]float64{ 
  98, 
  93, 
  77, 
  82, 
  83, 
}
```

**Because the length of an array is part of its type name**, working with arrays can be a little cumbersome. Adding or removing elements as we did here requires also changing the length inside the brackets. *Because of this and other limitations, you rarely see arrays used directly in Go code*. **Instead, you will usually use a *slice*, which is a type built on top of an array.**

##### Multi-dimensional arrays

```go
 twoD := [4][4]int{
   {1, 2, 3, 4}, 
   {5, 6, 7, 8}, 
   {9, 10, 11, 12},
   {13, 14, 15, 16}
 }
 threeD := [2][2][2]int{
   {
     {1, 0}, 
     {-2, 4}
   }, 
   {
     {5, -1}, 
     {7, 0}
   }
 }
```



#### Slices

对应动态数组

A slice is a segment of an array. Like arrays, slices are indexable and have a length. Unlike arrays, this length is allowed to change. Here’s an example of a slice:

`var x []float64`

The **only difference between this and an array is the missing length between the brackets**. In this case, x has been created with a length of zero.

If you want to create a slice, you should use the built-in make function:

`x := make([]float64, 5)`

This creates a slice that is associated with an underlying float64 array of **length 5**. Slices are always associated with some array, and although they can never be longer than the array, they can be smaller. The make function also allows a third parameter:

`x := make([]float64, 5, 10)`

*10 represents the capacity of the underlying array that the slice points to* 

- 5：length/size
- 10: capacity

Another way to create slices is to use the [low : high] expression:

```go
arr := [5]float64{1, 2, 3, 4, 5}
x := arr[0:5]
```

low is the index of where to start the slice and high is the index of where to end it (but not including the index itself). For example, while arr[0:5] returns [1,2,3,4,5], arr[1:4] returns [2,3,4].

*依旧保持左闭右开的原则*

For convenience, we are also allowed to omit low, high, or even both low and high. `arr[0:]` is the same as `arr[0:len(arr)]`, `arr[:5]` is the same as `arr[0:5]`, and `arr[:]` is the same as `arr[0:len(arr)]`.

*这种写法和Python很像*

In addition to the indexing operator, Go includes two built-in functions to assist with slices: append and copy.

##### append

append adds elements onto the end of a slice. If there’s sufficient capacity in the underlying array, the element is placed after the last element and the length is incre‐ mented. However, if there is not sufficient capacity, a new array is created, all of the existing elements are copied over, the new element is added onto the end, and the new slice is returned.

*这个细节和其他语言是一样的*

The definition of append can be a bit confusing but it’s easier to grasp once you see it used. Here is an example:

```go
func main() {
  slice1 := []int{1, 2, 3}
  slice2 := append(slice1, 4, 5)
  fmt.Println(slice1, slice2)
}
```

After running this program, slice1 has [1,2,3] and slice2 has [1,2,3,4,5]. append creates a new slice by taking an existing slice (the first argument) and append‐ ing all the following arguments to it.

##### copy

copy takes two arguments: *dst and src*. All of the entries in src are copied into dst overwriting whatever is there. If the lengths of the two slices are not the same, the smaller of the two will be used.

*前者为destination，后者是source*

```go
func main() {
  slice1 := []int{1, 2, 3}
  slice2 := make([]int, 2)
  copy(slice2, slice1)
  fmt.Println(slice1, slice2)
}
```

After running this program slice1 has [1,2,3] and slice2 has [1,2]. The contents of slice1 are copied into slice2, but because slice2 has room for only two ele‐ ments, only the first two elements of slice1 are copied.

Slices are typically used to represent lists of items, particularly when you need to access the *n*th item quickly—for example, player #33, or the 10th most popular search query. 

##### **Multi-dimensional slices**

```go
s1 := make([][]int, 4)
```

##### Sorting slices using sort.Slice()

```go
package main

import (
  "fmt"
  "sort" 
)
   
type aStructure struct {
  person string
	height int
	weight int 
}

func main() {
  mySlice := make([]aStructure, 0)
  mySlice = append(mySlice, aStructure{"Mihalis", 180, 90})
  mySlice = append(mySlice, aStructure{"Bill", 134, 45})
  mySlice = append(mySlice, aStructure{"Marietta", 155, 45})
  mySlice = append(mySlice, aStructure{"Epifanios", 144, 50})
  mySlice = append(mySlice, aStructure{"Athina", 134, 40})
  fmt.Println("0:", mySlice)
  
  //后面的func也就是Comparator<>
  sort.Slice(mySlice, func(i, j int) bool {
  	return mySlice[i].height < mySlice[j].height
  })
  fmt.Println("<:", mySlice)
  
  sort.Slice(mySlice, func(i, j int) bool {
    return mySlice[i].height > mySlice[j].height
  })
  fmt.Println(">:", mySlice)

}
```

##### **Appending an array to a slice**

```go
package main
import (
  "fmt"
)

func main() {
  s := []int{1, 2, 3}
  a := [3]int{4, 5, 6}
  ref := a[:] //类似Python的浅拷贝方式？
  fmt.Println("Existing array:\t", ref) // [4, 5, 6]
  t := append(s, ref...) //这种写法像ES6
  fmt.Println("New slice:\t", t) // [1 2 3 4 5 6]
  s = append(s, ref...)  
  fmt.Println("Existing slice:\t", s) // [1 2 3 4 5 6]
  s = append(s, s...)
  fmt.Println("s+s:\t\t", s) // [1 2 3 4 5 6 1 2 3 4 5 6]
}
```

#### Maps

A *map* is an unordered collection of key-value pairs (maps are also sometimes called *associative arrays, hash tables, or dictionaries*). Maps are used to look up a value by its associated key.

`var x map[string] int`

The map type is represented by the keyword map, followed by the key type in brackets and finally the value type. 

Like arrays and slices, maps can be accessed using brackets.

```go
// 该程序会报错，我们需要先初始化
var x map[string]int
x["keys"] = 10
fmt.Println(x)
```

The problem with our program is that **maps have to be initialized** before they can be used. We should have written this:

```go
var x := make(map[string]int) // make就像其他语言里面的new
x["keys"] = 10
fmt.Println(x["keys"])
```

This looks very much like an array, but there are a few differences. First, the length of a map (found by doing len(x)) can change as we add new items to it. When initially created, it has a length of 0; after x[1] = 10 it has a length of 1. Second, maps are not sequential. We have x[1], and with an array that would imply there must be an x[0], but maps don’t have this requirement.

元素无序并且容器大小可增

We can also delete items from a map using the built-in delete function:

`delete(x, 1)`

*此处的1对应的是key*

You can iterate over all the elements of a map using the next technique:

```go
// range with Map 其实格式上和Slice是统一的 都是k-v
for key, value := range iMap {
   fmt.Println(key, value)
}
```

```go
package main

import "fmt"

func main() {
	elements := make(map[string]string) 
  elements["H"] = "Hydrogen" 
  elements["He"] = "Helium" 
  elements["Li"] = "Lithium" 
  elements["Be"] = "Beryllium" 
  elements["B"] = "Boron" 
  elements["C"] = "Carbon" 
  elements["N"] = "Nitrogen" 
  elements["O"] = "Oxygen" 
  elements["F"] = "Fluorine" 
  elements["Ne"] = "Neon"
  fmt.Println(elements["Li"])
}
```

elements is a map that represents the first 10 chemical elements indexed by their symbol. This is a very common way of using maps: as a lookup table or a dictionary. Suppose we tried to look up an element that doesn’t exist

`fmt.Println(elements["Un"])`

If you run this, you should see nothing returned. Technically, a map returns the zero value for the value type (which for strings is the empty string). 

*这一点和C++一样，访问一个不存在key并不会报错，而是在底层直接以默认value的方式生成一个新的k-v键值对*

Although we could check for the zero value in a condition ( elements["Un"] == ""), Go provides a better way:

```go
name, ok := elements["Un"]
fmt.Println(name, ok)
```

Accessing an element of a map can return two values instead of just one. **The first value is the result of the lookup, the second tells us whether or not the lookup was successful.** 

```go
if name, ok := elements["Un"]; ok {
  fmt.Println(name, ok)
}
```

First, we try to get the value from the map. Then, if it’s successful, we run the code inside of the block.

先取值name然后再判断如果不是nil再接下来的操作， 相当于对取值nil的判断。类似于Swift里面的safe unwarp?

Maps are also often used to store general information. Let’s modify our program so that instead of just storing the name of the element, we store its standard state (state at room temperature) as well:

```go
func main() {
elements := map[string]map[string]string{ // 这种写法感觉并不优雅
	"H": map[string]string{ 
    "name":"Hydrogen", 
    "state":"gas",
	},
	"He": map[string]string{
     "name":"Helium",
     "state":"gas",
  },
}

if el, ok := elements["Li"]; ok {
  fmt.Println(el["name"], el["state"])
}
```

*即多维字典*

## Reference

Mastering Go Create Golang production applications using network libraries, concurrency, machine learning, and advanced data structures, Mihalis Tsoukalos, 2019

Introducing Go Build Reliable, Scalable Programs, Caleb Doxsey, 2016