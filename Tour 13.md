## Tour 13

### Concurrency

Large programs are often made up of many smaller subprograms. For example, a web server handles requests made from web browsers and serves up HTML web pages in response. Each request is handled like a small program.

It would be ideal for programs like these to be able to run their smaller components at the same time (in the case of the web server, to handle multiple requests). Making progress on more than one task simultaneously is known as *concurrency*. Go has rich support for concurrency using goroutines and channels.

#### Goroutines

**A goroutine is a function that is capable of running concurrently with other functions.** To create a goroutine, we use the keyword go followed by a function invocation:

```go
package main

import "fmt"

func f(n int) { 
  for i:=0;i<10;i++ {
  	fmt.Println(n, ":", i)
	}
}

func main() { 
  go f(0)
  var input string
  fmt.Scanln(&input)
}
```

This program consists of two goroutines. **The first goroutine is implicit and is the main function itself.** **The second goroutine is created when we call go f(0).** Normally, when we invoke a function, our program will execute all the statements in a function and then return to the next line following the invocation. With a goroutine, we return immediately to the next line and don’t wait for the function to complete. This is why the call to the Scanln function has been included; without it, the program would exit before being given the opportunity to print all the numbers.

Goroutines are lightweight and we can easily create thousands of them. We can modify our program to run 10 goroutines by doing this:

```go
func main() { 
  for i:=0;i<10;i++ {
  	go f(i) 
  }
  var input string
  fmt.Scanln(&input)
}
```

You may have noticed that when you run this program it seems to run the goroutines in order rather than simultaneously. Let’s add some delay to the function using time.Sleep and rand.Intn:

```go
package main
import ( 
  "fmt"
	"time"
	"math/rand"
)

func f(n int) { 
  for i:=0;i<10;i++ {
    fmt.Println(n, ":", i)
    amt := time.Duration(rand.Intn(250))
    time.Sleep(time.Millisecond * amt)
	} 
}

func main() { 
  for i:=0;i<10;i++ {
		go f(i) 
  }
	var input string
  fmt.Scanln(&input)
}
```

When run, this program produces the following:

```
4:0 
6:0 
5:0 
7:0 
0:0 
8:0 
2:0 
3:0 
1:0 
9:0 
3:1 
9:1 
7:1 
9:2 
8:1 
0:1 
4:1 
5:1
```

f prints out the numbers from 0 to 10, waiting between 0 and 250 ms after each one. The goroutines should now run simultaneously.

#### Channels

Channels provide a way for two goroutines to communicate with each other and synchronize their execution. Here is an example program using channels:

```go
package main
import ( 
  "fmt"
	"time"
)

func pinger(c chan string) { 
  for i:=0;;i++ {
		c <- "ping" 
  }
}

func printer(c chan string) { 
  for {
		msg := <-c 
    fmt.Println(msg) 
    time.Sleep(time.Second * 1)
	} 
}

func main() {
	var c chan string = make(chan string)
	go pinger(c) 
  go printer(c)
	var input string
  fmt.Scanln(&input)
}
```

This program will print ping forever (hit Enter to stop it). A channel type is represented **with the keyword chan followed by the type of the things that are passed on the channel** (in this case, we are passing strings). **The left arrow operator (<-) is used to send and receive messages on the channel. c <- "ping" means send "ping". msg := <- c means receive a message and store it in msg.** The fmt line could also have been written like fmt.Println(<-c), in which case we could remove the previous line.

Using a channel like this synchronizes the two goroutines. When pinger attempts to send a message on the channel, it will wait until printer is ready to receive the message (this is known as *blocking*). Let’s add another sender to the program and see what happens. Add this function:

```go
func ponger(c chan string) { 
  for i:=0;;i++ {
		c <- "pong" 
  }
}
```

And modify main:

```go
func main() {
	var c chan string = make(chan string)
	go pinger(c) 
  go ponger(c) 
  go printer(c)
	var input string
  fmt.Scanln(&input)
}
```

The program will now take turns printing ping and pong.

##### **Signal channels**

A signal channel is one that is used just for signaling. Put simply, y**ou can use a signal channel when you want to inform another goroutine about something.** Signal channels should not be used for the transferring of data.

##### **Buffered channels**

The topic of this subsection is buffered channels. **These are channels that allow the Go scheduler to put jobs in the queue quickly in order to be able to deal with more requests.** Moreover, you can use buffered channels as **semaphores** in order to limit the throughput of your application.

The technique presented here works as follows: all incoming requests are forwarded to a channel, which processes them one by one. When the channel is done processing a request, it sends a message to the original caller saying that it is ready to process a new one. So, the capacity of the buffer of the channel restricts the number of simultaneous requests that it can keep.

The technique will be presented with the help of the code found in bufChannel.go, which is broken down into four parts.

The first part of the code of bufChannel.go is as follows:

```go
package main
import (
	"fmt"
)
```

The second code segment of bufChannel.go contains the following Go code:

```go
func main() {
  numbers := make(chan int, 5)
  counter := 10
```

The definition presented of the numbers channel gives it a place to store up to five integers.

The third part of the code of bufChannel.go is shown in the following Go code:

```go
  for i := 0; i < counter; i++ {
    select {
      case numbers <- i:
    default:
      fmt.Println("Not enough space for", i)
    }
  }
```

In the preceding code, we tried to put 10 integers in the numbers channel. However, as the numbers channel has room for only five integers, we will not be able to store all 10 integers in it.

The remaining Go code of bufChannel.go follows:

```go
  for i := 0; i < counter+5; i++ {
    select {
      case num := <-numbers:
        fmt.Println(num)
      default:
        fmt.Println("Nothing more to be done!")
        break
    } 
  }
}
```

In the preceding Go code, we tried to read the contents of the numbers channel using a for loop and a select statement. As long as there is something to read from the numbers channel, the first branch of the select statement will get executed. As long as the numbers channel is empty, the default branch will be executed.

Executing bufChannel.go will create the following type of output:

```
$ go run bufChannel.go
Not enough space for 5
Not enough space for 6
Not enough space for 7
Not enough space for 8
Not enough space for 9
0
1
2
3
4
Nothing more to be done!
Nothing more to be done!
Nothing more to be done!
Nothing more to be done!
Nothing more to be done!
Nothing more to be done!
Nothing more to be done!
Nothing more to be done!
Nothing more to be done!
Nothing more to be done!
```

##### **Nil channels**

In this subsection, you will learn about nil channels. These are a special kind of channel because they always block. They are illustrated in nilChannel.go, which will be presented in four code segments.

The first part of nilChannel.go is as follows:

```go
package main
import (
  "fmt"
  "math/rand"
  "time" 
)
```

The second code portion of nilChannel.go is shown in the following Go code:

```go
func add(c chan int) {
  sum := 0
  t := time.NewTimer(time.Second)
	for { 
    select {
      case input := <-c:
      sum = sum + input
      case <-t.C:
      c = nil
      fmt.Println(sum)
    }
  }
}
```

The add() function demonstrates how a nil channel is used. The <-t.C statement blocks the c channel of the t timer for the time that is specified in the time.NewTimer() call. Do not confuse channel c, which is the parameter of the function, with channel t.C, which belongs to timer t. **When the time expires, the timer sends a value to the t.C channel**. This will trigger the execution of the relevant branch of the select statement, which will assign the value nil to channel c and print the sum variable.

The third code segment of nilChannel.go is as follows:

```go
func send(c chan int) {
  for {
    c <- rand.Intn(10)
  }
}
```

The purpose of the send() function is to generate random numbers and continue sending them to a channel for as long as the channel is open.

The remaining Go code of nilChannel.go is as follows:

```go
func main() {
  c := make(chan int)
  go add(c)
  go send(c)
  time.Sleep(3 * time.Second)
}
```

**The time.Sleep() function is used to give enough time to the two goroutines to operate.**

Executing nilChannel.go will generate the following output:

```
$ go run nilChannel.go
13167523
$ go run nilChannel.go
12988362
```

Since the number of times that the first branch of the select statement in the add() function will be executed is not fixed, you get different results from executing nilChannel.go.

##### **Channels of channels**

A channel of channels is a special kind of channel variable that works with channels instead of other types of variables. Nevertheless, you still have to declare a data type for a channel of channels. You can define a channel of channels using the chan keyword twice in a row, as shown in the following statement:

```go
c1 := make(chan chan int)
```

The use of channels of channels is illustrated using the code found in chSquare.go, which will be presented in four parts.

The first part of chSquare.go is as follows:

```go
package main
import (
  "fmt"
  "os"
  "strconv"
  "time"
)
var times int
```

The second code portion from chSquare.go is shown in the following Go code:

```go
func f1(cc chan chan int, f chan bool) {
  c := make(chan int)
  cc <- c
  defer close(c)
  sum := 0
  select {
    case x := <-c:
    for i := 0; i <= x; i++ {
      sum = sum + i
    }
    c <- sum
    case <-f:
    return 
  }
}
```

After declaring a regular int channel, you send that to the channel of channels variable. Then, you use a select statement in order to be able to read data from the regular int channel or exit your function **using the f signal channel**.

Once you read a single value from the c channel, you start a for loop that calculates the sum of all integers from 0 up to the integer value that you just read. Next, you send the calculated value to the c int channel and you are done.

The third part of chSquare.go contains the following Go code:

```go
func main() {
  arguments := os.Args
  if len(arguments) != 2 {
    fmt.Println("Need just one integer argument!")
    return 
  }
  times, err := strconv.Atoi(arguments[1])
  if err != nil {
    fmt.Println(err)
    return 
  }
  cc := make(chan chan int)
```

The last statement in the preceding code is where you declare a channel of channels variable named cc, which is the star of the program because everything depends on that variable. The cc variable is passed to the f1() function, and it will be used in the for loop that is coming next.

The remaining Go code of chSquare.go is as follows:

```go
  for i := 1; i < times+1; i++ {
    f := make(chan bool)
    go f1(cc, f)
    ch := <-cc

    ch <- i
    for sum := range ch {
      fmt.Print("Sum(", i, ")=", sum)
    }
    fmt.Println()
    time.Sleep(time.Second)
    close(f)
  }
}
```

The f channel is a signal channel used to end the goroutine when the real work is finished. **The ch := <-cc statement allows you to get a regular channel from the channel of channels variable in order to be able to send an int value to it using ch <- i.** After that, you start reading from it using a for loop. Although the f1() function is programmed to send a single value back, you can also read multiple values. Notice that each value of i is served by a different goroutine.

The type of a signal channel can be anything you want, including bool, which is used in the preceding code, and struct{}, which will be used in the signal channel in the next section. The main advantage of a struct{} signal channel is that no data can be sent to it, which can save you from bugs and misconceptions.

Executing chSquare.go will generate the following type of output:

```
$ go run chSquare.go 4
Sum(1)=1
Sum(2)=3
Sum(3)=6
Sum(4)=10
$ go run chSquare.go 7
Sum(1)=1
Sum(2)=3
Sum(3)=6
Sum(4)=10
Sum(5)=15
Sum(6)=21
Sum(7)=28
```





#### Channel Direction

We can specify a direction on a channel type, thus restricting it to either sending or receiving. For example, pinger’s function signature can be changed to this:

```go
func pinger(c chan<- string)
```

Now pinger is only allowed to send to c. Attempting to receive from c will result in a compile-time error. Similarly, we can change printer to this:

```go
func printer(c <-chan string)
```

A channel that doesn’t have these restrictions is known as *bidirectional*. A bidirec‐ tional channel can be passed to a function that takes send-only or receive-only channels, but the reverse is not true.

#### Select

Go has a special statement called select that works like a switch but for channels:

```go
func main() {
	c1 := make(chan string) 
  c2 := make(chan string)
	go func() { 
    for {
			c1 <- "from 1"
      time.Sleep(time.Second * 2)
    }
	}()

  go func() { 
    for {
			c2 <- "from 2"
		  time.Sleep(time.Second * 3)
    }
  }()

  go func() { 
    for {
			select {
			case msg1 := <- c1:
				fmt.Println(msg1) 
      case msg2 := <- c2:
        fmt.Println(msg2)
      }
    } 
  }()
  
  var input string
  fmt.Scanln(&input)
}
```

This program prints “from 1” every 2 seconds and “from 2” every 3 seconds. select picks the first channel that is ready and receives from it (or sends to it). If more than one of the channels are ready, then it randomly picks which one to receive from. If none of the channels are ready, the statement blocks until one becomes available.

The select statement is often used to implement a timeout:

```go
select {
case msg1 := <- c1:
	fmt.Println("Message 1", msg1) 
case msg2 := <- c2:
	fmt.Println("Message 2", msg2) 
case <- time.After(time.Second):
  fmt.Println("timeout")
}
```

time.After creates a channel, and after the given duration, will send the current time on it (we weren’t interested in the time, so we didn’t store it in a variable). We can also specify a default case:

```go
select {
case msg1 := <- c1:
	fmt.Println("Message 1", msg1) 
case msg2 := <- c2:
	fmt.Println("Message 2", msg2) 
case <- time.After(time.Second):
	fmt.Println("timeout") 
default:
  fmt.Println("nothing ready")
}
```

The default case happens immediately if none of the channels are ready.

#### Buffered Channels

It’s also possible to pass a second parameter to the make function when creating a channel:

```go
c := make(chan int, 1)
```

This creates a buffered channel with a capacity of 1. Normally, channels are synchronous; both sides of the channel will wait until the other side is ready. A buffered chan‐ nel is asynchronous; sending or receiving a message will not wait unless the channel is already full. If the channel is full, then sending will wait until there is room for at least one more int.

#### An Example

Here is an example program that uses goroutines and channels. It fetches several web pages simultaneously using the net/http package, and prints the URL of the biggest home page (defined as the most bytes in the response):

```go
package main
import ( 
  "fmt"
	"io/ioutil"
	"net/http"
)

type HomePageSize struct { 
  URL string
	Size int 
}

func main() {
	urls := []string{
    "http://www.apple.com",
    "http://www.amazon.com",
    "http://www.google.com",
    "http://www.microsoft.com",
	}
	results := make(chan HomePageSize)
	for _, url := range urls { 
    go func(url string) {
			res, err := http.Get(url) 
      if err!=nil {
				panic(err) 
    	}
			defer res.Body.Close()
			bs, err := ioutil.ReadAll(res.Body) 
    	if err!=nil {
				panic(err) 
    	}
    	results <- HomePageSize{
      	URL:  url,
      	Size: len(bs),
    	}
		}(url) 
  }
	var biggest HomePageSize 
  for range urls {
    result := <-results
		if result.Size > biggest.Size {
      biggest = result
    }
	}
	fmt.Println("The biggest home page:", biggest.URL)
}
```

We define a type that will store home page sizes:

```go
type HomePageSize struct { 
  URL string
	Size int 
}
```

Then we create a list of URLs:

```go
urls := []string{ 
  "http://www.apple.com", 
  "http://www.amazon.com", 
  "http://www.google.com", 
  "http://www.microsoft.com",
}
```

Then we create a channel and start a new goroutine for each URL (so we will be fetching four URLs simultaneously). For each URL, we make an HTTP get request:

```go
res, err := http.Get(url) 
if err!=nil {
	panic(err) 
}
defer res.Body.Close()
```

And we store the size of the response body:

```go
bs, err := ioutil.ReadAll(res.Body) 
if err!=nil {
	panic(err) 
}
results <- HomePageSize{
  URL:  url,
  Size: len(bs),
}
```

Notice that this is an unnamed function that is immediately invoked. This is a common pattern with goroutines, but also notice that we defined the function as taking a single parameter (the url). The reason this function doesn’t reference the url directly —which it is allowed to do—is that the rules of closure are such that if we did that, all four goroutines would probably end up seeing the same value for url. This is because url is changed by the for loop.

Finally, at the end of our program, we loop four times to pull the four results off of the channel, compare that results to the current biggest, and swap if it’s bigger:

```go
var biggest HomePageSize
for range urls {
	result := <-results
	if result.Size > biggest.Size {
    biggest = result
  }
}
```

## Reference

Mastering Go Create Golang production applications using network libraries, concurrency, machine learning, and advanced data structures, Mihalis Tsoukalos, 2019

Introducing Go Build Reliable, Scalable Programs, Caleb Doxsey, 2016