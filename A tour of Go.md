## Tour 1
### History of Go
> "Go is a modern, generic-purpose, open-source programming language that was officially announced at the end of 2009. Go began as an internal Google project, which means that it was started as an experiment, and has since been inspired by many other programming languages, including C, Pascal, Alef, and Oberon. Go's spiritual fathers are the professional programmers Robert Griesemer, Ken Thomson, and Rob Pike. They designed Go as a language for professional programmers who want to build reliable, robust, and efficient software. Apart from its syntax and its standard functions, Go comes with a pretty rich standard library." (Mihalis, 2019)

### Where is Go going

> "Some of the big changes that are being considered for Go 2 are generics, package versioning, and improved error handling. All these new features are under discussion at the moment and you should not be worried about them, but it is worthwhile to have an idea of the direction that Go is going in." (Mihalis, 2019)

### Features of Go

> The Go compiler prints practical warning and error messages that help you to solve the actual problem. Putting it simply, the Go compiler is there to help you, not to make your life miserable by printing pointless output! 
>
> Go has support for procedural, concurrent, and distributed programming.
>
> Go supports garbage collection, so you do not have to deal with memory allocation and deallocation.
>
> Go does not have a preprocessor and does high-speed compilation. As a consequence, Go can also be used as a scripting language.
>
> Go can build web applications and provides a simple web server for testing purposes.
>
> The standard Go library offers many packages that simplify the work of the developer. Additionally, the functions found in the standard Go library are tested and debugged in advance by the people who develop Go, which means that most of the time, they come without bugs. 
>
> Go uses static linking by default, which means that the binary files produced can be easily transferred to other machines with the same OS. As a consequence, once a Go program is compiled successfully and an executable file is generated, you do not need to worry about libraries, dependencies, and different library versions anymore.
>
> You will not need a graphical user interface (GUI) for developing, debugging, and testing Go applications, as Go can be used from the command-line, which I think many UNIX people prefer.
>
> Go supports Unicode, which means that you do not need any extra code for printing characters from multiple human languages.
>
> Go keeps concepts orthogonal because a few orthogonal features work better than many overlapping ones.

### Not perfect

> Go does not have direct support for object-oriented programming, which can be a problem for programmers who are used to writing code in an object-oriented manner. Nevertheless, you can use composition in Go to mimic inheritance.
>
> C is still faster than any other programming language for systems programming and this is mainly because UNIX is written in C.

### Read Go program

```go
package main

import (
	"fmt"
  _ "os"
) // The import keyword is how we include code from other packages to use with our program. The fmt package (shorthand for format) implements formatting for input and output. (Caleb Doxsey, 2016)

// single line comment
/*
	multiple line comments
*/

func main() {
  fmt.Println("Hello World!") // fmt.Println() : System.out.println() | std:: cout >>
}
```



### 初步感受：

1. Go是一门语法严格的语言。比如在导入依赖的时候，如果代码不需要执行依赖库，该程序将报错（可以适应underscore来避免报错）；在大括号的书写上也就是严格要求。



### Reference
Mastering Go Create Golang production applications using network libraries, concurrency, machine learning, and advanced data structures, Mihalis Tsoukalos, 2019

Introducing Go Build Reliable, Scalable Programs, Caleb Doxsey, 2016