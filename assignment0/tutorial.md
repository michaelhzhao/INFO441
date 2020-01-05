# INFO 441 Assignment 0 Tutorial

## Hello!
Welcome to INFO 441 Winter 2020. I'm Rico, one of your two TAs for this class. I've TA'ed this class last quarter and this is the third time that William has done it so hopefully you guys will find us helpful.

This is the first of many text tutorials that I'll be doing over the course and William mainly focuses on doing video tutorials. These tutorials serve as your resource to troubleshoot/prime to the upcoming assignments. 

## Install Go
Download the official installer for your OS at https://golang.org/dl/

## Notes for installing Go on Mac
Although you *can* install go using [Homebrew](https://formulae.brew.sh/formula/go) by running `brew install go`, the brew tap for go isn't officially maintained by Google. 

It's still recommended to run the [official installer](https://golang.org/dl/). I personally installed go via Homebrew and haven't encountered any issued but it's your judgemental call to make.

## Post-installation configurations
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