---
title: "Golang Syntax, Concepts and Eco-System"
datePublished: Mon Feb 19 2024 04:50:19 GMT+0000 (Coordinated Universal Time)
cuid: clssgk69y001309l8g3oqar6j
slug: golang-syntax-concepts-and-eco-system
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1708318387267/4ff907d0-7ae7-4d81-8386-36017aeed22d.png
tags: go, golang, golang-developer, gobasics

---

In this blog, you are going to understand Go basics, concepts and the Go eco-system. In the end you will be more confident in your learnings, whether it is your first language or you are adding it to your stack.

## What is Go?

Go or golang is an open source, statically-typed and multi-platform programming language backed by Google.

This means Go is free to use and change as needed. It also means that in Go, variables have to have their type declared or inferred and that Go strictly checks this at compile time.

## Why Learn Go?

Go is designed to be simple, efficient, fast and easy to learn. It is built for modern workflows, scales effortlessly and is enterprise ready.

Additionally, Go is multipurpose and can be used across various domains including building web services and Command-line Interfaces (CLI).

Go exposes low-level control with high-level convenience, allowing the user to dive deeper into concepts such as concurrency and pointers while offering safety.

In that way, Go is able to provide a very good developer experience without sacrificing performance and reliability. As a result, Go is growing ever more popular and is developing a strong community support.

To get started learning and building with Go, you need to have the language installed. You can get it from the [official website](https://go.dev/learn/).

## The Go STD Library

Go comes with a pretty batteries included [standard library](https://pkg.go.dev/std) or "stdlib". This is a set of officially maintained packages that come bundled with Go.

These packages cover a wide range of use cases including cryptography, networking and testing. Thereby greatly reducing the need to rely on external libraries, making Go an incredibly robust and complete language.

## The Go Eco-System

Go has a strong eco-system around it that extend the functionality available in the stdlib. This eco-system is fed and maintained by the Go community.

The eco-system includes solutions for versioning and dependency management via Go Modules, Logging and simpler database access.

## The Go CLI

Go installation comes with a command line interface tool that allows you to interact with the Go compiler via a terminal or command line.

It comes with many helpful command. The most common ones are as below:

* `go version`**\-** Displays the version of Go installed in your system
    
* `go run <file_name.go>` - Compiles and runs the specified file
    
* `go build <file_name.go>` - Compiles the specified Go file into an executable binary
    
* `go get <package_name>` - Downloads and installs the specified Go package
    
* `go install <package_name>` - Installs Go executable binaries built from the specified package into the `$GOPATH/bin`.
    
* `go env` - Displays the environment information of your Go installation
    
* `go mod <some_command>` - Set of subcommands that allows you to work with Go modules
    

## Understanding Go Concepts

The following Go concepts are useful and will help in quickly getting productive with the language.

### Data Types

Data types refer to the categories of values that variables hold. They determine the characteristic of the variable. Go has the following in-built data types:

* bool - a boolean type that represent true or false values
    
* string - a sequence of characters making up a text
    
* int - an integer type holding whole numbers. Go has quite a lot of int types as has been documented [here](https://go.dev/tour/basics/11)
    
* byte - a set of 8 bits used to store [ASCII characters](https://ee.hawaii.edu/~tep/EE160/Book/chap4/subsection2.1.1.1.html#:~:text=The%20ASCII%20table%20has%20128,)%2C%20for%20an%20ASCII%20character.)
    
* rune - a unicode point, stores characters
    
* float32 - a 32 bit decimal number
    
* float64 - a 64 bit decimal number
    

### Variables and Variable Declarations

Variables are named locations in memory that store information. Variables are declared with the `var` keyword. Variables that are being both declared and initialised can use short hand `:=`.

This is because the compiler can infer the type of the variable if it is being initialised and you cannot declare a variable without stating its type

The shorthand keyword is scoped to a function while the var keyword can be used anywhere in the file like so:

```go
package main

//this is valid
var job = "teacher";

// this is not valid because of the scope
isLovable := true;
func main() {
    // this is invalid because name has to either be typed 
    // or initialised at declaration
    var name;

    // this is valid
    var color string;

    // this is also valid
    age := 16; 

}
```

Note that Go will still throw an error because it expects all declared variables to be used.

### Structs and Struct Tags

Structs are special data types that are user-defined and are composed of variables of different types known as fields. Structs can also include other structs in their fields. A Struct is similar to an Object in other programming languages.

An example of structs in Go:

```go
// notice Blog has an Author field 
// which is of type Struct itself
type Blog struct {
	Title   string
	Content string
	Author  Author
}

type Author struct {
	Name  string
	Email string
}
```

Struct tags are additional pieces of information or metadata about the fields of a struct. Struct tags do not change the functionality of the code.

Struct tags are defined in string literals and can be useful to pass important data about the field to external tools and libraries. Most commonly used as part of JSON encoding and decoding process.

An example of struct tags is as below:

```go
type Blog struct {
	Title   string `json:"title"`
	Content string `json:"content"`
	Author  Author `json:"author"`
}

type Author struct {
	Name  string `json:"name"`
	Email string `json:"email"`
}
```

Here the struct tags describe how the fields will look in JSON format.

### Functions and Methods

Functions are blocks of code that carry out particular tasks. They are declared with the `func` keyword, can accept a list of inputs known as parameters and have a return type. Functions must be called in order to run.

An example of a Go function is shown here:

```go
// 'sum' is the name of the function
// 'a' and 'b' are the inputs
// int after the bracker is the type of 
// what the function returns
func sum(a int, b int) int {
	return a + b
}

// call the function here 
func main() {
    ans := sum(1,1)
    fmt.Println(ans)
}
```

Methods are functions that are associated with a particular type. Typically, a struct. Methods operate on a specific instance of the associated type or the receiver. The receiver is declared as part of the method.

Here is an example of what that would look like:

```go
package main

import "fmt"

// Author represents an author with a name and email
type Author struct {
	Name  string `json:"name"`
	Email string `json:"email"`
}

// GetName is a method associated with the Author type
// it retrieves the name of the author
func (a *Author) GetName() string {
	return a.Name
}

func main() {
	// Create an instance of Author
	myAuthor := Author{Name: "John Doe", Email: "john@example.com"}

	// Call the GetName method on the Author instance
	authorName := myAuthor.GetName()

	// Print the retrieved name
	fmt.Println("Author's Name:", authorName)
}
```

### Public and Private Methods/Functions

Public functions and methods can be accessed in another file while private ones can only be accesed in the file they were declared in.

For example, in the above code snippet `GetName()` is a public method and would be private if it read `getName()` instead.

### Pointers and Addresses

Pointers are variables that store the memory address of another variable. They are used to indirectly access the value stored in a particular memory address. They provide a way to reference and modify the value of a variable indirectly.

Pointers are declared by `*` followed by the variable type it should point to. Pointers can be nil if they do not point to a valid memory address.

An example is shown below:

```go
// declare variable 'a' of type int
var a int

// declare variable 'ptr' that points 
// to an integer variable
var ptr *int
```

You can also get the memory address of a variable by declaring this symbol `&` followed by the variable whose address is being looked for.

An example is as below:

```go
a := 1
var ptr *int

// variable 'ptr' which is a pointer 
// is now pointing to the memory address of variable 'a'
ptr = &a
```

You can use the dereference operator to get the actual value of variable ptr as below:

```go
package main

import "fmt" 

func main() {
a := 1
var ptr *int

// variable 'ptr' which is a pointer 
// is now pointing to the memory address of variable 'a'
ptr = &a

// this prints 1, which is the value that ptr was pointing to
fmt.Println(*ptr)
}
```

### Packages and Imports

Packages are ways to organise Go code. A Go application normally has several packages. A Go file must declare its package at the top of the file.

All files in a folder share a package name, and this is usually the same name as the folder itself.

Go applications will need to import both local packages and their files as well as packages from the stdlib and 3rd party packages.

All imports are declared at the top of the files right below the `package <name>` declaration. Imports can be grouped like so, to avoid repeating the `import` keyword:

```go
package main

import (
    "path/to/yet/another/package"
	"path/to/another/package"
	"path/to/package"
)
```

Imports can also be individually written like so:

```go
package main

import "path/to/package"
import "path/to/another/package"
import "path/to/yet/another/package"
```

Here is an example on how to import functions from other folders in Go:

```go
// your project structure
myproject/
|-- main.go
|-- utils/
|   |-- math.go
```

And this how math.go looks like:

```go
package utils

func Sum(a int, b int) int {
    return a + b
}
```

This is how to call this function in main.go:

```go
package main

// notice we imported the utils folder
import (
    "fmt"
    "myproject/utils"
)

func main() {
    // here we can access all public methods from
    // the utils folder without needing to 
    // specify the math.go file
    result := utils.Add(3, 4)

    fmt.Println("Result:", result)
}
```

### Arrays and Slices

Arrays and Slices in Go are data types that can contain multiple elements of the same type. They are represented by this symbol `{}`

The difference between them is that Arrays have a fixed size while Slices are dynamic and resizable.

### How to Loop Through Arrays and Slices

You use the `for` keyword to iterate through arrays and slices. At every iteration, you get access to the current index and element.

Here is an example of the same:

```go
package main

import "fmt"

func main() {
    // declare and initialise a slice
    numbers := []int{1, 2, 3, 4, 5}

    // iterate through the slice using a for loop
    for index, value := range numbers {
        fmt.Println( "Index", index, "Value", value)
    }
}
```

### How to ParseInt in Go

In Go, we use the Atoi method from the "strconv" package from the stdlib to parse strings to integers.

This code snippet illustrates how:

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	str := "123"

	// Parse the string to an integer
    // the method returns a possible error
	num, err := strconv.Atoi(str)

	// deal with any errors from Atoi method
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	fmt.Println("Parsed Integer:", num)
}
```

## Resources

The following resources are helpful:

* [Learn Go from the source](https://go.dev/learn/)
    
* [The Go Handbook](https://www.freecodecamp.org/news/go-beginners-handbook/)