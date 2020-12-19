## Tour 6

### 指针数组和数组指针

指针的数组；数组的指针。

#### 指针数组

指针数组，数组里的元素都是指针。go语言数组默认是值传递，在函数中修改传入的数组对原来的数组没有影响。

```go
func main() {
	var a [5]int
	fmt.Println(a) // [0 0 0 0 0]
	test(a)
	fmt.Println(a) // [0 0 0 0 0]
}


func test(a [5]int) {
	a[1] = 2
	fmt.Println(a) // [0 2 0 0 0]
}
```

如果传递的是指针数组，

```go
func main() {
	var a [5]*int     // 指针数组内部全都是一个一个指针
	fmt.Println(a)                // [<nil> <nil> <nil> <nil> <nil>]
	for i := 0; i < 5; i++ {
		temp := i
		a[i] = &temp
	}
	for i := 0; i < 5; i++ {
		fmt.Print(" ", *a[i])       // [0 1 2 3 4]
	}
	fmt.Println()
	test1(a)
	for i := 0; i < 5; i++ {
		fmt.Print(" ", *a[i])       // [0 2 2 3 4]
	}
}


func test1(a [5]*int) {
	*a[1] = 2
	for i := 0; i < 5; i++ {
		fmt.Print(" ", *a[i])        // [0 2 2 3 4]
	}
	fmt.Println()
}
```

如果我们使用`a[i]=&i`,

```go
func main() {
	var a [5]*int
	fmt.Println(a)
	for i := 0; i < 5; i++ {
		// temp := i
		a[i] = &i          
    fmt.Print("-", *a[i])       // -0 -1 -2 -3 -4
	}
	for i := 0; i < 5; i++ {
		fmt.Print(" ", *a[i])       // 5 5 5 5 5
	}
	fmt.Println()
}
```

> *在初始化的时候如果直接另a[i] = &i那么指针数组内部存储的全是同一个地址，所以输出结果也一定是相同的*

![image-20201219131859975](/Users/yao/Library/Application%20Support/typora-user-images/image-20201219131859975.png)

#### 数组指针

```go
func main() {
	var a [5]int
	var aPtr *[5]int
	aPtr = &a
	//这样简短定义也可以aPtr := &a
	fmt.Println(aPtr)              // &[0 0 0 0 0]
	test(aPtr)                     // &[0 5 0 0 0]
	fmt.Println(aPtr)              // &[0 5 0 0 0]
}


func test(aPtr *[5]int) {
	aPtr[1] = 5
	fmt.Println(aPtr)
}
```

![image-20201219131830126](/Users/yao/Library/Application%20Support/typora-user-images/image-20201219131830126.png)

*在这里我们可以用左值和右值来理解。我们不能改变数组本身，但是可以改变数组里面的元素。*

### String

A Go string is a read-only **byte slice** that can hold any type of bytes and can have an arbitrary length.

```go
package main
import (
  "fmt"
  s "strings"
  "unicode" 
)
var f = fmt.Printf

func main() {
  upper := s.ToUpper("Hello there!") 
  f("To Upper: %s\n", upper)                     // "HELLO THERE!"
  f("To Lower: %s\n", s.ToLower("Hello THERE"))  // "hello there!"
  f("%s\n", s.Title("tHis wiLL be A title!"))    // THis WILL BE A Title!
  f("EqualFold: %v\n", s.EqualFold("Mihalis", "MIHAlis")) // true
  f("EqualFold: %v\n", s.EqualFold("Mihalis", "MIHAli"))  // false
  f("Prefix: %v\n", s.HasPrefix("Mihalis", "Mi"))         // true
  f("Prefix: %v\n", s.HasPrefix("Mihalis", "mi"))         // false
  f("Suffix: %v\n", s.HasSuffix("Mihalis", "is"))         // true
  f("Suffix: %v\n", s.HasSuffix("Mihalis", "IS"))         // false
  f("Index: %v\n", s.Index("Mihalis", "ha"))              // 2
  f("Index: %v\n", s.Index("Mihalis", "Ha"))              // -1
  f("Count: %v\n", s.Count("Mihalis", "i"))               // 2
  f("Count: %v\n", s.Count("Mihalis", "I"))               // 0
  f("Repeat: %s\n", s.Repeat("ab", 5))                    // ababababab
  f("TrimSpace: %s\n", s.TrimSpace(" \tThis is a line. \n"))   //This is a line.
  f("TrimLeft: %s", s.TrimLeft(" \tThis is a\t line. \n", "\n\t")) // This is a    line.
  f("TrimRight: %s\n", s.TrimRight(" \tThis is a\t line. \n","\n\t ")) //     THis is a    line.
  f("Compare: %v\n", s.Compare("Mihalis", "MIHALIS")) // 1
  f("Compare: %v\n", s.Compare("Mihalis", "Mihalis")) // 0
  f("Compare: %v\n", s.Compare("MIHALIS", "MIHalis")) // -1
  f("Fields: %v\n", s.Fields("This is a string!"))    // [This is a string!]
  f("Fields: %v\n", s.Fields("Thisis\na\tstring!"))   // [Thisis a string!]
  f("%s\n", s.Split("abcd efg", ""))                  //    [a b c d   e f g]
  f("%s\n", s.Replace("abcd efg", "", "_", -1))    //    _a_b_c_d_ _e_f_g_
  f("%s\n", s.Replace("abcd efg", "", "_", 4))   //    _a_b_c_d efg
  f("%s\n", s.Replace("abcd efg", "", "_", 2))   //     _a_bcd efg
  lines := []string{"Line 1", "Line 2", "Line 3"} 
  f("Join: %s\n", s.Join(lines, "+++"))            //    Join: Line 1+++Line 2+++Line 3
  f("SplitAfter: %s\n", s.SplitAfter("123++432++", "++")) //    SplitAfter: [123++ 432++ ]
  trimFunction := func(c rune) bool {
    return !unicode.IsLetter(c)
  }
  f("TrimFunc: %s\n", s.TrimFunc("123 abc ABC \t .", trimFunction)) //    TrimFunc: abc ABC
                                 
}
```

**the strings.Fields() function splits the string parameter using whitespace characters as separators. The whitespace characters are defined in the unicode.IsSpace() function.**

**The strings.Replace() function takes four parameters. The first parameter is the string that you want to process. The second parameter contains the string that, if found, will be replaced by the third parameter of strings.Replace(). The last parameter is the maximum number of replacements that are allowed to happen. If that parameter has a negative value, then there is no limit to the number of replacements that can take place.**

**The last two statements of the program define a trim function, which allows you to keep the runes of a string that interest you and utilize that function as the second argument to the strings.TrimFunc() function.**

**Lastly, the strings.SplitAfter() function splits its first parameter string into substrings based on the separator string that is given as the second parameter to the function.**

### Rune

A **rune** is an int32 value, and therefore a Go type, that is used for representing a **Unicode code point**. A Unicode code point, or code position, is a numerical value that is usually used for representing single Unicode characters; however, it can also have alternative meanings, such as providing formatting information.

You can consider a string as a collection of runes.

*字符都是由字节码表示出来的*

## Reference

Mastering Go Create Golang production applications using network libraries, concurrency, machine learning, and advanced data structures, Mihalis Tsoukalos, 2019