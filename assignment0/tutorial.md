# INFO 441 Assignment 0 Tutorial


## Hello!
Welcome to INFO 441 Winter 2020. I'm Rico, one of your two TAs for this class. I've TA'ed this class last quarter and this is the third time that William has done it so hopefully you guys will find us helpful.

This is the first of many text tutorials that I'll be doing over the course and William mainly focuses on doing video tutorials. These tutorials serve as your resource to troubleshoot/prime you for the upcoming assignments. 

## Setting up
### Install Go
Download the official installer for your OS at https://golang.org/dl/

### Notes for installing Go on Mac
Although you *can* install go using [Homebrew](https://formulae.brew.sh/formula/go) by running `brew install go`, the brew tap for go isn't officially maintained by Google. 

It's still recommended to run the [official installer](https://golang.org/dl/). I personally installed go via Homebrew and haven't encountered any issues but it's your judgemental call to make.

### Post-installation configurations
After installing go, try running `go version` in your shell/powershell and you should see something like this, if not identical.
```bash
$ go version
go version go1.13.5 darwin/amd64
```

Go keeps track of and looks for two different locations when compiling codes, namely `GOROOT` and `GOPATH`. `GOROOT`, as its name suggests, stores the binary distributions. **You should never touch `GOROOT` under most circumstances.** `GOPATH` is used to resolve import statments and locate your project files. 

More elaborated explanation is available at https://stackoverflow.com/a/10847122.

In most cases, `GOROOT` is preconfigured when you run the installer. You do, however, need to set up `GOPATH`. To do so, simply make a new directory of your choice and we assume it to be your `GOPATH`. Under `GOPATH`, make three more directories and name them `src`, `bin`, and `pkg`. Your directory layout should look **exactly** like this:
```text
GOPATH=/home/user/go

/home/user/go/
    src/
        foo/
            info441/               (go code in package info441)
                info441_main.go
    bin/
        info441_main               (Compiled go executable)
    pkg/
```
Source: [Command go](https://golang.org/cmd/go/#hdr-GOPATH_environment_variable)

- For *nix based OS (Linux, MacOS, etc.):
    1. Open `~/.bashrc` or `~/.bash_profile` (or `~/.zshrc` if you're using zsh);
    2. Export your `GOPATH` by creating a new line and stating `export GOPATH=[your_directory]`.
    3. Append your `GOPATH` to `PATH` by chaining using the colon sign `:` such that `export PATH="$GOPATH:$GOROOT:$PATH"`. Note that you may have different shell configurations -- just always chain more environment variables before `$PATH`. For example, I have a very heavily modified shell which makes my whole `PATH` look like this: 
    ```bash
    ### Set up your GOPATH
    export GOPATH="/Users/ryocown/go"

    ### Bunch of other stuff that I use
    # Replace coreutils
    export GNUBIN="/usr/local/opt/coreutils/libexec/gnubin"
    # Replace Mac's OpenSSL with brew's OpenSSL
    export OPENSSL="/usr/local/opt/openssl/bin"
    export QT5="/usr/local/opt/qt5/bin"
    export MYSQL_CLIENT="/usr/local/opt/mysql-client/bin"
    # Fun fact, Python 2 is finally officially deprecated
    export PYTHON2="/Users/ryocown/Library/Python/2.7/bin"
    export PYTHON3="/Users/ryocown/Library/Python/3.7/bin"

    ### Set up your PATH
    export PATH="$QT5:$GNUBIN:$GOPATH:$GOROOT:$OPENSSL:$MYSQL_CLIENT:$PYTHON2:$PYTHON3:$PATH" # < note $PATH is always at the end
    ```
    4. Here is a quick reminder of what `PATH` is -- [PATH definition](http://www.linfo.org/path_env_var.html).
    5. Finally, if you set up your `PATH` environment variable correctly, your should see all of your executables resovled to absolute paths.
    ```bash
    $ echo $PATH
    /usr/local/opt/qt5/bin:/usr/local/opt/coreutils/libexec/gnubin:/Users/ryocown/go::/usr/local/opt/openssl/bin:/usr/local/opt/mysql-client/bin:/Users/ryocown/Library/Python/2.7/bin:/Users/ryocown/Library/Python/3.7/bin:/usr/local/opt/qt5/bin:/usr/local/opt/coreutils/libexec/gnubin:/Users/ryocown/go::/usr/local/opt/openssl/bin:/usr/local/opt/mysql-client/bin:/Users/ryocown/Library/Python/2.7/bin:/Users/ryocown/Library/Python/3.7/bin:/Users/ryocown/Utils/google-cloud-sdk/bin:/usr/local/opt/qt5/bin:/usr/local/opt/coreutils/libexec/gnubin:/Users/ryocown/go::/usr/local/opt/openssl/bin:/usr/local/opt/mysql-client/bin:/Users/ryocown/Library/Python/2.7/bin:/Users/ryocown/Library/Python/3.7/bin:/Users/ryocown/Utils/google-cloud-sdk/bin:/usr/local/opt/qt5/bin:/usr/local/opt/coreutils/libexec/gnubin:/Users/ryocown/go::/usr/local/opt/openssl/bin:/usr/local/opt/mysql-client/bin:/Users/ryocown/Library/Python/2.7/bin:/Users/ryocown/Library/Python/3.7/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
    # see how GOPATH is added as the third argument "/Users/ryocown/go" and GOROOT is just empty "::"
    ```

- For Windows, check out this link: [How to change environment variables on Windows 10
](https://www.architectryan.com/2018/08/31/how-to-change-environment-variables-on-windows-10/).

If you set up `GOPATH` correctly, run `go env` in your shell/powershell and you should see something like this:
```bash
$ go env
GO111MODULE=""
GOARCH="amd64"
GOBIN=""
GOCACHE="/Users/ryocown/Library/Caches/go-build"
GOENV="/Users/ryocown/Library/Application Support/go/env"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="darwin"
GONOPROXY=""
GONOSUMDB=""
GOOS="darwin"
GOPATH="/Users/ryocown/go" # < GOPATH here
GOPRIVATE=""
GOPROXY="https://proxy.golang.org,direct"
GOROOT="/usr/local/Cellar/go/1.13.5/libexec" # < GOROOT here, Cellar is the directory used by Homebrew indicating I installed go via Homebrew; if you're on Windows, you should see "c:\Go"
GOSUMDB="sum.golang.org"
GOTMPDIR=""
GOTOOLDIR="/usr/local/Cellar/go/1.13.5/libexec/pkg/tool/darwin_amd64"
GCCGO="gccgo"
AR="ar"
CC="clang"
CXX="clang++"
CGO_ENABLED="1"
GOMOD=""
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=/var/folders/rx/1btbtr3x5lg8n4t6hrqwqww80000gn/T/go-build250414485=/tmp/go-build -gno-record-gcc-switches -fno-common"
```

## Execise guide

In this guide, I'll be going over the `Zipserver` exercise and providing a solution to prepare you for Assignment 1. Note that William and I may have different solutions there are literally infinite ways to implement this.

### Setting up
Since I mainly work on Mac, the following setups is under the MacOS environment. The actual shell commands vary very little for other OS and are principally the same.

1. Navigate to your `$GOPATH/src` where your codes should be located in by running `cd $GOPATH/src`.
2. Download your exercises after accepting the GitHub Classroom assignment by running `git clone [YOUR_PERSONAL_GITHUB_CLASSROOM_REPO]`.
3. Open your favourite code editor and locate the folders `zipserver` and `zipclient`.

In my case, I get the following outputs in my zsh:
```bash
$ cd $GOPATH/src
~/go/src $ git clone https://github.com/UW-Info-441-Winter-Quarter-2020/exercises-bryoco
Cloning into 'exercises-bryoco'...
remote: Enumerating objects: 2130, done.
remote: Counting objects: 100% (2130/2130), done.
remote: Compressing objects: 100% (1644/1644), done.
remote: Total 2130 (delta 421), reused 2130 (delta 421), pack-reused 0
Receiving objects: 100% (2130/2130), 17.94 MiB | 18.76 MiB/s, done.
Resolving deltas: 100% (421/421), done.
~/go/src $ code exercises-bryoco
```

### Structuring go programs
Every Go program must be included in a package. A standalone executable Go program must have a main function and must be included in the main package. The main function is the entry point for execution.
```bash
~/go/src/exercises-bryoco $ tree zipserver
zipserver
├── handlers    # package handlers
│   ├── constants.go
│   └── zips.go
├── main.go     # package main
├── models      # package models
│   ├── zip.go
│   └── zip_test.go
└── zips.csv
```
If you open each file inside this project folder, we see your starter codes have pre-defined three packages -- `handlers`, `models` and `main`. For example, for our main.go:

```go
// in main.go
// defines package
package main

import (
    "net/http"
    "os"
    "fmt"
    // note that imports in golang are always resolved to $GOPATH/src/[absolute-path]
    // so in my case it would be: 
    // "exercises-bryoco/models"
    // "exercises-bryoco/handlers"
    // etc.
)

// main is our main entry point
func main() {
    // ...
}
```

With `package main/handlers/models`, we have defined the package for each file. Now let's take a look at the `main()` function provided. Note that I removed some of the `else` statements from the starter codes to make it slightly easier to read.

```go
// in main.go
func main() {
    // create a new "mux" (router)
    // see: https://drstearns.github.io/tutorials/goweb/ 
	mux := http.NewServeMux()

    // open the zips.csv and report any errors
    f, err := os.Open("zips.csv")

    // "Defer" is used to ensure that a function call is performed later in a program’s execution, usually for purposes of cleanup
    // In this context, we use "defer" to close the file
    // see: https://gobyexample.com/defer
	defer f.Close()

    // Error handling for opening the file
	if err != nil {
        // fmt writes outputs to your console, not the client
        fmt.Println("Error couldn't read file.")
        // return immediately "returns" the function and executes any defer statments right after
		return
	}

    // Here we see the first method that we need to implement
	zList, err := models.LoadZips(f, 42614)
	if err != nil {
		fmt.Println("Failed to create ZipIndex")
		return
	}

    // We will discuss this later
	ctx := handlers.Ctx{
		Db: models.BuildIndex(zList),
	}

    // Register your handler function
    // I also moved IndexHandler to the handlers package,
    mux.HandleFunc("/", handlers.IndexHandler)   
    // and renamed Handler to SearchHandler, for better readability
	mux.HandleFunc("/search", ctx.SearchHandler)
	http.ListenAndServe(":4000", mux)
}
```

Now let's move to `handlers/zips.go` and implement several things.
```go
// in handlers/zips.go

// Golang doesn't have classes/objects, instead we have structs. 
// However, we can "register" or "attach" methods to structs to make them behave like objects.
// See Dr. Stearns tutorial: https://drstearns.github.io/tutorials/gojson/
// and Structs Instead of Classes - OOP in Go: https://golangbot.com/structs-instead-of-classes/
type Ctx struct {
	Db models.ZipIndex
}

// NewZipCtx constructs a new Ctx.
// The `db` parameter will be used to get the zips for the requested key.
func NewZipCtx(db models.ZipIndex) *Ctx {
	return &ZipIndexHandler{
		Db: db,
	}
}

// Register router for "/" 
// If you see the definition of mux.HandleFunc, it takes two arguments: 
// func(ResponseWriter, *Request))
// Here we conform to that requirement with http.ResponseWriter and *http.Request
// "*" denotes that the argument is a pointer -- to put it simply, a pointer is a *reference* to the struct as as opposed to a *copy* of the original struct.
// See: https://gobyexample.com/pointers and
//      https://medium.com/rungo/pointers-in-go-a789eafccd53
func IndexHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Add(headerAccessControlAllowOrigin, "*")
	fmt.Fprintf(w, "Try requesting /zips/city/seattle")
}

// We "attach" SearchHandler function to Ctx so we have access to the fields in Ctx
func (ctx *Ctx) SearchHandler(w http.ResponseWriter, r *http.Request) {
	//resource path will be like: /zips/city/seattle
	//get the last part of the path and convert it
	//to lower-case
	key := path.Base(r.URL.Path)
	key = strings.ToLower(key)
	
	//tell the client that the response body is JSON
	//and allow cross-origin requests
	w.Header().Add(headerContentType, contentTypeJSON)
	w.Header().Add(headerAccessControlAllowOrigin, "*")
	
	//get the zips for the requested key
	//and encode them into JSON
	zips := zih.index[key]
	json.NewEncoder(w).Encode(zips)
}
```

Now that we have implemented `handlers/zips.go`, let's head back to main.go to change a few things.

Previously in `main.go`, we just sort of "spawned" a Ctx struct with:
```go
ctx := handlers.Ctx{
    Db: models.BuildIndex(zList),
}

// Now that we've written a proper constructor, we can replace these codes with:
// Note that the new ctx is a *pointer* to the struct
ctx := handlers.NewZipCtx(zList)
```

Moving onto `models/zip.go` -- we've still got a couple of things to implement.
```go
// in handlers/zip.go

// LoadZips loads the zip code records from CSV stream
// returning a ZipSlice or an error. The expectedNumber
// should be set to the expected number of records, or
// zero if you don't know how many records there will be.
func LoadZips(r io.Reader, expectedNumber int) (ZipSlice, error) {
	// create a new CSV reader over the input stream
	reader := csv.NewReader(r)

	// read the header row and ensure that there are at least 7 fields
	fields, err := reader.Read()
	if err != nil {
		return nil, fmt.Errorf("error reading header row: %v", err)
	}
	if len(fields) < 7 {
		return nil, fmt.Errorf("CSV has %d fields but we require %d", len(fields), 7)
	}

	// make a slice of *Zip with enough capacity
	// to hold expectedNumber. This is just an
	// optimization so that we can avoid reallocations
	// as we append to the slice
	zips := make(ZipSlice, 0, expectedNumber)

	// loop until we return...
	for {
		// read a row from the CSV file
        
        fields, err := reader.Read()
		// if we got io.EOF as an error, we are
		// done reading the input stream (End Of File)
		if err == io.EOF {
			return zips, nil
        }
        
		// if we got some other error, return it
		if err != nil {
			return nil, fmt.Errorf("Error parsing CSV: %v", err)
        }
        
		// Create a *Zip and initialize the fields
        // These numbers represent the respective fields that we are interested in
        // Note that the "&" 
		z := &Zip{
			Code:  fields[0],
			City:  fields[3],
			State: fields[6],
        }
        
		// append the *Zip to the slice
		zips = append(zips, z)
	}
}
```

### Testing go codes with unit tests
You may have noticed that a `zip_test.go` is provided to your `models` folder. We will cover how to write unit tests in go later, but for now, run the following codes in your shell/powershell to execute the tests.
```bash
# the ./... means to run all available tests that go-test can find in the current directory
~/go/src/exercises-bryoco/zipserver/models $ go test ./...
# if you've successfully implemented models.LoadZips, you should see the following outputs
ok  	exercises-bryoco/zipserver/models	0.004s
```

### Checking with solutions
We've provided you with sample solutions to all exercises in the `complete` branch in your repo. Go check them out if you run into any issues.