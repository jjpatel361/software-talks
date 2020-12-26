### The Great Golang

Cool things about Golang

- a lot of the dev religious discussion is wrapped up inside the language
- the lang has built-in opinions on certain ways. Brings devs to concensus on building rather than petty discussion. I love this
- `go run` command builds the executable and executes it and throws away the build
- `go build` command builds the artifact based on the target machine. linux x86, windows - exe etc
- unused vars throw build error
- lint, doc, test tooling is already built in the lang. thank god no more third party libs needed for minimal setup
- cool features link dynamic linking with C code, memory profiling, benchmarking test etc. in the lang! Might be cool for refactoring ancient C code base or interacting with non native apps.
- `errors` are values! You don't throw exception but you return an `error` and consumer does an if condition check
- `panic` way to interrupt or exit the program exitcode 0
- `_` is special for overcoming unused vars
    ```go
    func main() {
        var _, secondMagicNumber = magicNumbers()
        // see how we use _ as placeholder
        // if we destructured both vars then we need to use both of them.
    }

    func magicNumbers() int, int {
        return 10, 1
    }
    ```
- multiple returns possible. No need to encapsulate into object. just return comma separated.
- inference based typing
- strong typing
- support for pointers! yay! 
- function promotes the pointer address from function scope to outer level
    ```go
    func main() {
        var datapointer = getPointer()
        // datapointer is valid here. and not lost after local var
        // local var address is promoted
    }

    func getPointer() *User {
        return &User {
            name = Jay
        }
    }
    ```
- no classes just structs! 
- no constructors per se, you can use new<StructName> as the constructor function!
- no `public` and `private` keyword accessors. 
- `FancyMethod` is public because first letter is UPPERCASE `nonfancymethod` is private because first letter is lowercase
- Folder based package access
- `vendor` directory modules are used for version locking only valid for workspaces. 
- `function` are first class citizen! you can pass functions as parameters, as return values etc.
- anonymous function much like IIFE
    ```go
    func main() {
        func () { // <- see no function name 
            fmt.Println("Hi from Anon");
        }() // <- see how it is invoked. much like JS 
    }
    ```
- functions as variables
    ```go
    func main() {
        a  := func() {
            fmt.Println("Hi from Anon")
        }

        // a is now a function reference! 
        a() // calls the function
    }
    ```
- implicit interface 
    ```go
    type BadReader struct {
        err := error
    }

    // Read is a method over io.Reader we just copied method signature 
    func (br BadReader) Read(p []byte) (n int, err error) {
        return -1, br.err // we return the err field attached to br object
    }

    func reader() {
        // see how the reader is an object of io.Reader 
        // right hand side of assignment is BadReader which has the method 
        // Read overriden by the function above
        var reader io.Reader = BadReader {
            err= error.New("weird reader error")
        }

        value, err := reader.Read() // this calls the implementation of BadReader!
    }
    ```
- `defer` is cool version of finally. when you want to do cleanup acts before function return. Add a defer function handler

    ```go
    func filereader() {
        defer func() {
            // close file reader 
        }

        defer func() {
            //
        }

        // do your stuff. 
        // if error occurs or everything goes smooth  
        // defer invoked after function has finished executing
        // add all defer to the function start
        // multiple defer functions are possible with stack behavior FILO 

        return;
    }
    ```
