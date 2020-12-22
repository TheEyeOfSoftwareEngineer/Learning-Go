## Tour 9

### Packages

Go was designed to be a language that encourages good software engineering practi‐ ces. An important part of high-quality software is code reuse—embodied in the prin‐ ciple “Don’t Repeat Yourself.”

functions are the first layer we utilize to allow code reuse. Go also provides another mechanism for code reuse: packages. Nearly every program we’ve seen so far included this line:

```go
import "fmt"
```

`fmt` is the name of a package that includes a variety of functions related to formatting

and output to the screen. Bundling code in this way serves three purposes:

- It reduces the chance of having overlapping names, and in turn keeps our function names short and succinct.
- It organizes code so that it’s easier to find code you want to reuse.
- It speeds up the compiler by only requiring recompilation of smaller chunks of a program. **Although we use the package fmt, we don’t have to recompile it every time we change our program.**

#### The Core Packages

Instead of writing everything from scratch, most real-world programming depends on our ability to interface with existing libraries. This chapter will take a look at some of the most commonly used packages included with Go.

First, a word of warning: although some of these libraries are fairly obvious, many of the libraries included with Go require specialized, domain-specific knowledge (e.g., cryptography). 

##### Strings

Go includes a large number of functions to work with strings in the strings package. To search for a smaller string in a bigger string, use the Contains function:

```go
package main
import ( 
  "fmt"
	"strings"
)

func main() {
	// Contains(s, substr string) bool 
  fmt.Println(strings.Contains("test", "es")) 
  // => true
}
```

To count the number of times a smaller string occurs in a bigger string, use the Count function:

```go

package main
import ( 
  "fmt"
	"strings"
)
func main() {
	// func Count(s, sep string) int 
  fmt.Println(strings.Count("test", "t")) 
  // => 2
}
```

To determine if a bigger string starts with a smaller string, use the HasPrefix function:

```go
package main
import ( 
  "fmt"
	"strings"
)
func main() {
	// func HasPrefix(s, prefix string) bool 
  fmt.Println(strings.HasPrefix("test", "te"))
  // => true
}
```

To determine if a bigger string ends with a smaller string, use the HasSuffix function:

```go
package main
import ( 
  "fmt"
	"strings"
)
func main() {
	// func HasSuffix(s, suffix string) bool
  fmt.Println(strings.HasSuffix("test", "st")) 
  // => true
}
```

To find the position of a smaller string in a bigger string, use the Index function (**it returns -1 if not found**):

```go
package main
import ( 
  "fmt"
	"strings"
)
func main() {
	// func Index(s, sep string) int 
  fmt.Println(strings.Index("test", "e")) 
  // => 1
}
```

To take a list of strings and join them together in a single string separated by another string (e.g., a comma), use the Join function:

```go

package main
import ( 
  "fmt"
	"strings"
)
func main() {
	// func Join(a []string, sep string) string
  fmt.Println(strings.Join([]string{"a","b"}, "-")) 
  // => "a-b"
}
```

To repeat a string, use the Repeat function:

```go
package main
import ( 
  "fmt"
	"strings"
)
func main() {
	// func Repeat(s string, count int) string 
  fmt.Println(strings.Repeat("a", 5))
	// => "aaaaa"
}
```

To replace a smaller string in a bigger string with some other string, use the Replace function. In Go, **Replace also takes a number indicating how many times to do the replacement (pass -1 to do it as many times as possible**):

```go

package main
import ( 
  "fmt"
	"strings"
)
func main() {
	// func Replace(s, old, new string, n int) string 
  fmt.Println(strings.Replace("aaaa", "a", "b", 2)) 
  // => "bbaa"
}
```

To split a string into a list of strings by a separating string (e.g., a comma), use the Split function (Split is the reverse of Join):

```go
package main
import ( 
  "fmt"
	"strings"
)
func main() {
	// func Split(s, sep string) []string 
  fmt.Println(strings.Split("a-b-c-d-e", "-"))) 
  // => []string{"a","b","c","d","e"}
}
```

To convert a string to all lowercase letters, use the ToLower function:

```go
package main
import ( 
  "fmt"
  "strings"
)
func main() {
	// func ToLower(s string) string
	fmt.Println(strings.ToLower("TEST")) 
  // => "test"
}
```

To convert a string to all uppercase letters, use the ToUpper function:

```go
package main
import ( 
  "fmt"
	"strings"
)
func main() {
	// func ToUpper(s string) string
	fmt.Println(strings.ToUpper("test")) 
  // => "TEST"
}
```

Sometimes we need to work with strings as binary data. To convert a string to a slice of bytes (and vice versa), do this:

```go
arr := []byte("test")
str := string([]byte{'t','e','s','t'})
```

##### Input/Output

Before we look at files, we need to understand Go’s io package. The io package consists of a few functions, but mostly interfaces used in other packages. The two main interfaces are **Reader and Writer**. Readers support reading via the Read method. Writers support writing via the Write method. Many functions in Go take Readers or Writers as arguments. For example, the io package has a Copy function that copies data from a Reader to a Writer:

```go
func Copy(dst Writer, src Reader) (written int64, err error)
```

To read or write to a []byte or a string, you can use the Buffer struct found in the

bytes package:

```go
var buf bytes.Buffer
buf.Write([]byte("test"))
```

这就是我们需要序列化和反序列化的意义。不同的格式会有不同的要求，但是最终都是比特流。

A Buffer doesn’t have to be initialized, and it supports both the Reader and Writer interfaces. You can convert it into a []byte by calling buf.Bytes(). If you only need to read from a string, **you can also use the strings.NewReader function, which is more efficient than using a buffer.**

##### Files and Folders

To open a file in Go, use the Open function from the os package. Here is an example of how to read the contents of a file and display them on the terminal:

```go
package main
import ( 
  "fmt"
	"os"
)

func main() {
	file, err := os.Open("test.txt") 
  if err!=nil {
    // handle the error here
		return
	}
	defer file.Close()
  
  // get the file size
	stat, err := file.Stat() 
  if err!=nil {
		return
	}
  
  // read the file
	bs := make([]byte, stat.Size()) 
  _, err = file.Read(bs) 
  if err!=nil {
		return
	}
  
	str := string(bs)
	fmt.Println(str)
}
```

**We use defer file.Close() right after opening the file to make sure the file is closed as soon as the function completes.** Reading files is very common, so there’s a shorter way to do this:

```go
package main
import ( 
  "fmt"
	"io/ioutil"
)

func main() {
	bs, err := ioutil.ReadFile("test.txt") 
  if err!=nil {
		return
	}
  str := string(bs)
	fmt.Println(str)
}
```

**To create a file, use the os.Create function.** It takes the name of the file, creates it in the current working directory, and returns an os.File and possibly an error (if it was unable to create it for some reason). Here’s an example program:

```go
package main
import ( 
  "os"
)

func main() {
	file, err := os.Create("test.txt") 
  if err!=nil {
  	// handle the error here
		return
	}
	defer file.Close()
  
	file.WriteString("test")
}

```

To get the contents of a directory, we use the same os.Open function but give it a directory path instead of a file name. Then we call the Readdir method:

```go

package main
import ( 
  "fmt"
	"os"
)

func main() {
	dir, err := os.Open(".") 
  if err!=nil {
		return
	}
	defer dir.Close()
  
	fileInfos, err := dir.Readdir(-1) 
  if err!=nil {
		return
	}
	for _, fi := range fileInfos {
	  fmt.Println(fi.Name())
  }
}
```

Readdir takes a single argument that limits the number of entries returned. By passing in -1, we return all of the entries.

-1和replace一样，在这种场景下都是全部的意思

Often, we want to recursively walk a folder (read the folder’s contents, all the subfold‐ ers, all the sub-subfolders, etc.). To make this easier, there’s a Walk function provided in the path/filepath package:

```go
package main
import ( 
  "fmt"
	"os"   
  "path/filepath"
)
func main() {
	filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
    fmt.Println(path)
		return nil
  }) 
}
```

The function you pass to Walk is called for **every file and folder in the root folder** (in this case, .). It’s passed three arguments: **path, which is the path to the file; info, which is the information for the file (the same information you get from using os.Stat); and err, which is any error that was received while walking the directory.** **The function returns an error and you can return filepath.SkipDir to stop walking immediately.**

##### Errors

Go has a built-in type for errors that we have already seen (the error type). We can create our own errors by using the New function in the errors package:

```go

package main
import "errors"
func main() {
	err := errors.New("error message")
}
```

##### Containers and Sort

- list

In addition to lists and maps, Go has several more collections available underneath the container package. We’ll take a look at the container/list package as an example

The container/list package implements a doubly linked list. 

Each node of the list contains a value (1, 2, or 3, in this case) and a pointer to the next node. Because this is a doubly linked list, each node will also have pointers to the previous node. This list could be created by this program:

```go
package main
import ("fmt" ; "container/list")

func main() {
	var x list.List
  x.PushBack(1)
	x.PushBack(2)
	x.PushBack(3)
	for e := x.Front(); e != nil; e=e.Next() { 
    fmt.Println(e.Value.(int))
	} 
}
```

基本和STL是一样的

The zero value for a List is an empty list (a *List can also be created using list.New). Values are appended to the list using PushBack. We loop over each item in the list by getting the first element, and following all the links until we reach nil.

- sort

The sort package contains functions for sorting arbitrary data. There are several predefined sorting functions (for slices of ints and floats) Here’s an example for how to sort your own data:

```go

package main
import ("fmt" ; "sort")

type Person struct { 
  Name string
	Age int 
}

type ByName []Person
func (ps ByName) Len() int { 
  return len(ps)
}
func (ps ByName) Less(i, j int) bool {
	return ps[i].Name < ps[j].Name 
}
func (ps ByName) Swap(i, j int) { 
  ps[i], ps[j] = ps[j], ps[i]
}
func main() {
	kids := []Person{
		{"Jill",9},
		{"Jack",10},
	}
	sort.Sort(ByName(kids))
	fmt.Println(kids)
}
```

The Sort function in sort takes a sort.Interface and sorts it. **The sort.Interface requires three methods: Len, Less, and Swap.**

Len should return the length of the thing we are sorting. For a slice, simply return len(ps).

Less is used to determine whether the item at position i is strictly less than the item at position j. In this case, we simply compare ps[i].Name to ps[j].Name.

Swap swaps the items.

To define our own sort, we create a new type (ByName) and make it equivalent to a slice of what we want to sort. We then define the three methods.

Sorting our list of people is then as easy as casting the list into our new type. We could also sort by age by doing this:

```go
type ByAge []Person
func (this ByAge) Len() int {
	return len(this) 
}
func (this ByAge) Less(i, j int) bool { 
  return this[i].Age < this[j].Age
}
func (this ByAge) Swap(i, j int) {
	this[i], this[j] = this[j], this[i]
}
```

##### Hashes and Cryptography

A hash function takes a set of data and reduces it to a smaller fixed size. **Hashes are frequently used in programming for everything from looking up data to easily detecting changes. Hash functions in Go are broken into two categories: cryptographic and non-cryptographic.**

加密与非加密

The non-cryptographic hash functions can be found underneath the hash package and include adler32, crc32, crc64, and fnv. Here’s an example using crc32:

```go
package main
import ( 
  "fmt"
	"hash/crc32"
)

func main() {
	// create a hasher
	h := crc32.NewIEEE()
	// write our data to it 
  h.Write([]byte("test"))
	// calculate the crc32 checksum 
  v := h.Sum32()
	fmt.Println(v)
}
```

The crc32 hash object implements the Writer interface, so we can write bytes to it like any other Writer. Once we’ve written everything we want, we call Sum32() to return a uint32. **A common use for crc32 is to compare two files**. **If the Sum32 value for both files is the same, it’s highly likely (though not 100% certain) that the files are the same.** If the values are different, then the files are definitely not the same:

```go
package main
import ( 
  "fmt"
	"hash/crc32"
	"io/ioutil"
)

func getHash(filename string) (uint32, error) { 
  // open the file
	f, err := os.Open(filename) 
  if err!=nil {
		return 0, err 
  }
	// remember to always close opened files
	defer f.Close() 
  // create a hasher
  h := crc32.NewIEEE()
	// copy the file into the hasher
	// - copy takes (dst, src) and returns (bytesWritten, error)
	_, err := io.Copy(h, f)
	// we don't care about how many bytes were written, but we do want to 
  // handle the error
	if err!=nil {
		return 0, err 
  }
	return h.Sum32(), nil 
}


func main() {
	h1, err := getHash("test1.txt") 
  if err!=nil {
		return
	}
	h2, err := getHash("test2.txt") 
  if err!=nil {
		return
	}
	fmt.Println(h1, h2, h1 == h2)
}
```

Cryptographic hash functions are similar to their non-cryptographic counterparts, but **they have the added property of being hard to reverse.** Given the cryptographic hash of a set of data, it’s extremely difficult to determine what made the hash. These hashes are often used in security applications.

One common cryptographic hash function is known as SHA-1. Here’s how it is used:

```go
package main
import ( 
  "fmt"
	"crypto/sha1"
)

func main() {
	h := sha1.New()
	h.Write([]byte("test")) 
  bs := h.Sum([]byte{}) 
  fmt.Println(bs)
}
```

This example is very similar to the crc32 one, because both crc32 and sha1 implement the hash.Hash interface. **The main difference is that whereas crc32 computes a 32-bit hash, sha1 computes a 160-bit hash.** There is no native type to represent a 160- bit number, so we use a slice of 20 bytes instead.

#### Servers

Writing distributed, networked applications in Go is relatively straightforward. We will briefly take a look at three common approaches to communicating between mul‐ tiple computers: **TCP servers, HTTP servers, and RPC.**

##### TCP

TCP is the primary protocol used for communication over the Internet. Any time you interact with a web page, play a multiplayer computer game, stream a movie, or video chat, there’s a good chance your computer is communicating with a remote server using TCP.

In Go, **we can create a TCP server using the net package’s Listen function. Listen takes a network type (in our case, tcp) and an address and port to bind, and returns a net.Listener:**

```go
type Listener interface {
	// Accept waits for and returns the next connection to the 
  listener.Accept() (c Conn, err error)
	// Close closes the listener.
	// Any blocked Accept operations will be unblocked and return errors. 
  Close() error
  // Addr returns the listener's network address.
	Addr() Addr 
}
```

Once we have a Listener, we call Accept, which waits for a client to connect and returns a net.Conn. A net.Conn implements the io.Reader and io.Writer interfaces, so we can read from it and write to it just like a file.

Here’s a complete example:

```go
package main
import ( 
  "encoding/gob"
	"fmt"
	"net"
)

func server() {
	// listen on a port
	ln, err := net.Listen("tcp", ":9999") 
  if err!=nil {
		fmt.Println(err)
		return
	}
  for {
		// accept a connection 
    c, err := ln.Accept() 
    if err!=nil {
	    fmt.Println(err)
			continue
		}
    // handle the connection
		go handleServerConnection(c) 
  }
}

func handleServerConnection(c net.Conn) { 
  // receive the message
	var msg string
	err := gob.NewDecoder(c).Decode(&msg) 
  if err!=nil {
		fmt.Println(err) }
  else{
    fmt.Println("Received", msg)
  }
	c.Close() 
}

func client() {
	// connect to the server
	c, err := net.Dial("tcp", "127.0.0.1:9999") 
  if err!=nil {   
    fmt.Println(err)
		return
	}
  // send the message
	msg := "Hello, World" 
  fmt.Println("Sending", msg)
	err = gob.NewEncoder(c).Encode(msg) 
  if err!=nil {
    fmt.Println(err)
  }
	c.Close() 
}

func main() { 
  go server() 
  go client()
	var input string
  fmt.Scanln(&input)
}
```

This example uses the encoding/gob package, which makes it easy to encode Go val‐ ues so that other Go programs (or the same Go program, in this case) can read them. Additional encodings are available in packages underneath encoding (like encoding/ json) as well as in third-party packages (e.g., we could use labix.org/v2/mgo/bson for bson support).

##### HTTP

HTTP servers are even easier to set up and use:

```go
package main
import ("net/http" ; "io")

func hello(res http.ResponseWriter, req *http.Request) { 	
  res.Header().Set(
    "Content-Type",
    "text/html",
	)
  io.WriteString(
	  res,
    `<DOCTYPE html>
    <html>
      <head>
		    <title>Hello, World</title>
      </head>
      <body>
		     Hello, World!
      </body>
		</html>`,   // raw template写法 
  )
}
func main() {
  http.HandleFunc("/hello", hello)
  http.ListenAndServe(":9000", nil) 
}
```

HandleFunc handles a URL route (/hello) by calling the given function. We can also handle static files by using FileServer:

```go
http.Handle(
  "/assets/",
  http.StripPrefix(
    "/assets/",
    http.FileServer(http.Dir("assets")),
  ),
)
```

##### RPC

The net/rpc (remote procedure call) and net/rpc/jsonrpc packages provide an easy way to expose methods so they can be invoked over a network (rather than just in the program running them):

net / rpc（远程过程调用）和net / rpc / jsonrpc包提供了一种公开方法的简便方法，因此可以通过网络（而不是仅在运行它们的程序中）调用它们。所以这就是FasS的基础？

```go
package main
import ( 
  "fmt" 
  "net"
	"net/rpc"
)

type Server struct {}

func (this *Server) Negate(i int64, reply *int64) error {
  *reply = -i
	return nil
}

func server() { 
  rpc.Register(new(Server))
	ln, err := net.Listen("tcp", ":9999") 
  if err!=nil {
    fmt.Println(err)
    return
	} 
  for {
    c, err := ln.Accept() 
    if err!=nil {
      continue
		}
		go rpc.ServeConn(c) 
  }
}

func client() {
	c, err := rpc.Dial("tcp", "127.0.0.1:9999") 
  if err!=nil {
    fmt.Println(err)
		return
	}
	var result int64
	err = c.Call("Server.Negate", int64(999), &result) 
  if err!=nil {
		fmt.Println(err) 
  } else {
    fmt.Println("Server.Negate(999) =", result)
  }
}

func main() {
	go server() 
  go client()
  var input string
  fmt.Scanln(&input)
}
```

This program is similar to the TCP example, except now we created an object to hold all the methods we want to expose and we call the Negate method from the client. See the documentation in net/rpc for more details.

##### Parsing Command-Line Arguments

When we invoke a command on the terminal, it’s possible to pass that command arguments. We’ve seen this with the go command:

```go
go run myfile.go
```

run and myfile.go are arguments. We can also pass flags to a command:

```go
go run -v myfile.go
```

The flag package allows us to parse arguments and flags sent to our program. Here’s an example program that generates a number between 0 and 6. We can change the max value by sending a flag (-max=100) to the program:

```go
package main
import ("fmt";"flag";"math/rand")

func main() {
	// Define flags
	maxp := flag.Int("max", 6, "the max value") 
  // Parse
	flag.Parse()
	// Generate a number between 0 and max 
  fmt.Println(rand.Intn(*maxp))
}
```

Any additional non-flag arguments can be retrieved with flag.Args(), which returns a []string.

#### Creating Packages

Packages only really make sense in the context of a separate program that uses them. Without this separate program, we have no way of using the package we create. Let’s create an application that will use a package we will write. Create a folder in *~/src/ golang-book* called *chapter8*. Inside that folder, create a file called *main.go* using the following code:

```go
package main 
import "fmt"
import "golang-book/chapter8/math"

func main() {
	xs := []float64{1,2,3,4} 
  avg := math.Average(xs) 
  fmt.Println(avg)
}
```

Now create another folder inside of the *chapter8* folder called *math*. Inside of this folder, create a file called *math.go* that contains this:

```go
package math
func Average(xs []float64) float64 { 
  total := float64(0) 
  for _,x := range xs{
		total += x 
  }
	return total / float64(len(xs)) 
}
```

Using a terminal, change directory into the *math* folder you just created and run go install. This will compile the math.go program and create a linkable object file: *~/pkg/os_arch/golang-book/chapter8/math.a* (where *os* is something like *windows* and *arch* is something like *amd64*).

**所以我们在完成被导入文件后还需要将其执行为可连接的对象文件**

Now change back into the *chapter8* folder and run go run main.go. You should see 2.5. Some things to note:

- math is the name of a package that is part of Go’s standard distribution, but because Go packages can be hierarchical, we are safe to use the same name for our package (the real math package is just math, ours is golang-book/chapter8/ math). 文件层级不同
- When we import our math library, we use its full name (import "golang-book/ chapter8/math"), but inside of the *math.go* file, we only use the last part of the name (package math).
- We also only use the **short name math when we reference functions from our library. If we wanted to use both libraries in the same program, Go allows us to use an alias (m is the alias)**:

```go
import m "golang-book/chapter8/math"

func main() {
	xs := []float64{1,2,3,4} 
  avg := m.Average(xs)
  fmt.Println(avg)
}
```

- You may have noticed that every function in the packages we’ve seen starts with a capital letter. In Go, if something starts with a capital letter, that means other packages (and programs) are able to see it. If we had named the function average instead of Average, our main program would not have been able to see it.             函数名开头大写将对其他文件可视

  It’s a good practice to only expose the parts of our package that we want other packages using and hide everything else. This allows us to freely change those parts later without having to worry about breaking other programs, and it makes our package easier to use.

- Package names match the folders they fall in. There are ways around this, but it’s a lot easier if you stay within this pattern.

##### **Compiling a Go package**

Although you cannot execute a Go package if it does not include a main() function, you are still allowed to compile it and create an object file, as follows:

```go
$ go tool compile aPackage.go
$ ls -l aPackage.*
```

##### **Private variables and functions**

What differentiates private variables and functions from public ones is that private ones can be strictly used and called internally in a package. Controlling which functions, constants, and variables are public or not is also known as **encapsulation**.

Go follows a simple rule that **states that functions, variables, types, and so forth that begin with an uppercase letter are public,** whereas functions, **variables, types, and so on that begin with a lowercase letter are private**. *This is the reason that fmt.Println() is named Println() instead of println()*. However, this rule does not affect package names that are allowed to begin with uppercase and lowercase letters.

##### **The init() function**

Every Go package can optionally have a private function named **init() that is automatically executed at the beginning of the execution time.**

> The init() function is a private function by design, which means that it cannot be called from outside the package in which it is contained. Additionally, as the user of a package has no control over the init() function, you should think carefully before using an init() function in public packages or changing any global state in init().

I will now present a code example with multiple init() functions from multiple Go packages. Examine the code of the following basic Go package, which is simply called a:

```go
package a
import (
  "fmt"
)
func init() {
  fmt.Println("init() a")
}
func FromA() {
  fmt.Println("fromA()")
}
```

The a package implements an init() function and a public one named FromA().

After that, you will need to execute the following commands from your UNIX shell so that the package becomes available to the current UNIX user:

```go
$ mkdir ~/go/src/a
$ cp a.go ~/go/src/a/
$ go install a
```

Now, look at the code of the next Go code package, which is named b:

```go
package b
import ( 
  "a"
	"fmt" 
)
func init() {
  fmt.Println("init() b")
}
func FromB() {
  fmt.Println("fromB()")
  a.FromA()
}
```

What is happening here? Package a uses the fmt standard Go package. However, package b needs to import package a, as it uses a.FromA(). Both a and b have an init() function.

As before, you will need to install that package and make it available to the current UNIX user by executing the following commands from your UNIX shell:

```go
$ mkdir ~/go/src/b
$ cp b.go ~/go/src/b
$ go install b
```

Thus, we currently have two Go packages that both have an init() function. Now try to guess the output that you will get from executing manyInit.go, which comes with the following code:

```go
package main
import ( 
  "a"
	"b"	
	"fmt" 
)
func init() {
  fmt.Println("init() manyInit")
}
func main() {
  a.FromA()
  b.FromB()
}
```

```go
$ go run manyInit.go
init() a
init() b
init() manyInit
fromA()
fromB()
fromA()
```

#### Documentation

Go has the ability to automatically generate documentation for packages we write in a similar way to the standard package documentation. In a terminal, run this command:

```go
godoc golang-book/chapter8/math Average
```

You should see information displayed for the function we just wrote:

```go
func Average(xs []float64) float64
```

We can improve this documentation by adding a comment before the function:

```go
// Finds the average of a series of numbers
func Average(xs []float64) float64 {
```

If you rerun the godoc command, you should see our comment below the function definition:

```go
func Average(xs []float64) float64
	Finds the average of a series of numbers
```

This documentation is also available in web form by running this command:

```go
godoc -http=":6060"
```

and entering this URL into your browser: `http://localhost:6060/pkg/`

You should be able to browse through all of the packages installed on your system.

这一点比较像javadoc

## Reference

Mastering Go Create Golang production applications using network libraries, concurrency, machine learning, and advanced data structures, Mihalis Tsoukalos, 2019

Introducing Go Build Reliable, Scalable Programs, Caleb Doxsey, 2016