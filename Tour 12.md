## Tour 12

### Testing

Programming is not easy; even the best programmers are incapable of writing pro‐ grams that work exactly as intended every time. Therefore, an important part of the software development process is testing. Writing tests for our code is a good way to ensure quality and improve reliability.

Go includes a special program that makes writing tests easier: go test. The Go compiler knows to ignore code in any files that end with *_test.go*, so the code defined in this file is only used by go test (and not go install or go build).

根据filename的名称判断是否为test文件 如果是那么就不compile

we import the special testing package and define a function that starts with the word Test (case matters) followed by whatever we want to name our test. We’ll be testing the Average function we wrote before, so let’s name it TestAverage:

```go
package math

import "testing"

func TestAverage(t *testing.T) { 
  v := Average([]float64{1,2}) 
  if v!=1.5 {
  	t.Error("Expected 1.5, got ", v)
	}
}
```

For the body of the function, we invoke the Average function on a hardcoded slice of floats (Average([]float64{1,2})). We then take that value and compare it to 1.5 and if they’re not the same, we use the special t.Error function (which is very much like fmt.Println) to signal an error to the go test program.

To actually run the test, run the following in the same directory:

```
go test
```

You should see this:

```
$ go test
PASS
ok golang-book/chapter8/math 0.032s
```

The go test command will look for any tests in any of the files in the current folder and run them. Tests are identified by starting a function with the word Test and taking one argument of type *testing.T.

Once we have set up the testing function, we write tests that use the code we’re testing. In this case, we know the average of [1,2] should be 1.5 so that’s what we check. It’s probably a good idea to test many different combinations of numbers, so let’s slightly modify our test program:

```go
package math
import "testing"

type testpair struct { 
  values []float64
  average float64 
}

var tests = []testpair{
  { []float64{1,2}, 1.5 },
  { []float64{1,1,1,1,1,1}, 1 }, 
  { []float64{-1,1}, 0 },
}
func TestAverage(t *testing.T) { 
  for _, pair := range tests {
  	v := Average(pair.values) 
    if v != pair.average {
    	t.Error(
      	"For", pair.values,
      	"expected", pair.average,
      	"got", v,
    	)	 
    }
	} 
}
```

This is a very common way to set up tests (abundant examples can be found in the source code for the packages included with Go). We create a struct to represent the inputs and outputs for the function:

```go
type testpair struct { 
  values []float64
	average float64 
}
```

Then we create a list of these **testpairs**, loop through each one, and run the func‐ tion:

```go
for _, pair := range tests { 
  v := Average(pair.values) 
  if v != pair.average {
    t.Error(
      "For", pair.values,
      "expected", pair.average,
      "got", v,
		)	 
  }
}
```

Creating a good set of tests, and in particular, knowing precisely which values to test, takes a bit of practice. For a list of floating-point numbers, it’s a good idea to test a variety of cases: an empty list, several random values, repeated or negative numbers, and so on. But even a small set of basic tests is better than none.

## Reference

Introducing Go Build Reliable, Scalable Programs, Caleb Doxsey, 2016