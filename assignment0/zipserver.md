# Execise guide

In this guide, I'll be going over the `ZipServer` exercise and providing a solution to prepare you for Assignment 1. 

## Setting up
Since I mainly work on Mac, the following setups is under the MacOS environment. The actual shell commands vary very little for other OS and are principally the same.

1. use `go get github.com/UW-Info-441-Winter-Quarter-2020/exercises-[username]`
2. Navigate to `$GOPATH/src/github.com/UW-Info-441-Winter-Quarter-2020/exercises-[username]`
3. Open your favorite code editor and find the `zipserver` and `zipclient` folders 

## Clear your files for zipserver
**ZipServer's base code was changed between different versions, so follow these steps to be able to follow along with this tutorial or the video tutorial**

zipserver/main.go:

```golang
package main

func main() {

}
```

zipserver/handlers/zips.go:

```golang
package handlers
```

zipserver/models/zip.go:

```golang
package models

import (
	"encoding/csv"
	"fmt"
	"io"
)

//Zip represents a zip code record.
//The `json:"..."` field tags allow us to change
//the name of the field when it is encoded into JSON
//see https://drstearns.github.io/tutorials/gojson/
type Zip struct {
	Code  string `json:"code,omitempty"`
	City  string `json:"city,omitempty"`
	State string `json:"state,omitempty"`
}

//ZipSlice is a slice of *Zip,
//that is, pointers to Zip struct instances
type ZipSlice []*Zip

//ZipIndex is a map from strings to ZipSlices
type ZipIndex map[string]ZipSlice

//LoadZips loads the zip code records from CSV stream
//returning a ZipSlice or an error. The expectedNumber
//should be set to the expected number of records, or
//zero if you don't know how many records there will be.
func LoadZips(r io.Reader, expectedNumber int) (ZipSlice, error) {
	//create a new CSV reader over the input stream
	reader := csv.NewReader(r)

	//read the header row and ensure that there are at least 7 fields
	fields, err := reader.Read()
	if err != nil {
		return nil, fmt.Errorf("error reading header row: %v", err)
	}
	if len(fields) < 7 {
		return nil, fmt.Errorf("CSV has %d fields but we require %d", len(fields), 7)
	}

	//make a slice of *Zip with enough capacity
	//to hold expectedNumber. This is just an
	//optimization so that we can avoid reallocations
	//as we append to the slice
	zips := make(ZipSlice, 0, expectedNumber)

	//loop until we return...
	for {
		//read a row from the CSV file
		fields, err := reader.Read()
		//if we got io.EOF as an error, we are
		//done reading the input stream (End Of File)
		if err == io.EOF {
			return zips, nil
		}
		//if we got some other error, return it
		if err != nil {
			return nil, fmt.Errorf("Error parsing CSV: %v", err)
		}
		//create a *Zip and initialize the fields
		// These numbers represent the respective fields that we are interested in
		z := &Zip{
			Code:  fields[0],
			City:  fields[3],
			State: fields[6],
		}
		//append the *Zip to the slice
		zips = append(zips, z)
	}
}
```

## ZipServer

The point of ZipServer is to create a server that will send out data about zip codes associated with a city based on what is requested. This server will be a process that runs on your computer or a remote computer somewhere on a server host, and anyone will be able to contact the server and make a request. Later on, you will create a ZipClient that fetches data from your ZipServer, similar to how you may have requested data from other. 

First, you will want to read through and sort of understand what the `LoadZips` function is doing in the code above. The `io.Reader` type you can use a `File` type for, which we will see how to get.

Our main server code will need to load in the zips data before it actually starts up the server, and we do that by using the LoadZips. See below for the final code and explanations about each line of code.

main.go:

```golang
package main

import (
	"log"
	"net/http"
	"os"
	"strings"

	// this import location will be different for you. Using VSCode with extensions
	// will automatically import these when you call functions from these packages!
	// Work smarter, not harder.
	"exercises/zipserver/handlers"
	"exercises/zipserver/models"
)

func main() {
	//open the zips.csv file and report an errors
	f, err := os.Open("zips.csv")
	if err != nil {
		//if we can't open the file, we can't continue
		//so use log.Fatalf() to report the error and
		//stop the process
		log.Fatalf("error opening zips.csv: %v", err)
	}
	//load the zip code records from the file
	//and report any errors
	//42613 is the number of rows in the data
	zips, err := models.LoadZips(f, 42613)
	if err != nil {
		//again, we can't continue in this case
		//so use log.Fatal()
		log.Fatalf("error parsing CSV: %v", err)
	}
	//close the file so we don't leave it open
	//while the web server is running
	f.Close()
	log.Printf("loaded %d zips", len(zips))

	//build a map index from city name (lower-cased)
	//to a slice of *Zip for that city
	cityIndex := models.ZipIndex{}
	for _, z := range zips {
		cityLower := strings.ToLower(z.City)
		cityIndex[cityLower] = append(cityIndex[cityLower], z)
	}
	log.Printf("there are %d zips in Seattle", len(cityIndex["seattle"]))

	//construct a new ZipIndexHandler, passing the
	//cityIndex as the index to use
	cityZipHandler := handlers.NewZipIndexHandler(cityIndex)

	//get the address to listen on from the
	//ADDR environment variable, otherwise use
	//4000 as the default
	addr := os.Getenv("ADDR")
	if len(addr) == 0 {
		addr = ":4000"
	}

	//create a new mux and add two routes:
	// / => root handler
	// /zips/city/<city-name> => cityZipHandler
	mux := http.NewServeMux()
	mux.Handle("/zips/city/", cityZipHandler)

	//start the web server
	log.Printf("server is listening at http://%s", addr)
	log.Fatal(http.ListenAndServe(addr, mux))
}
```

handlers/zips.go:

```golang
package handlers

import (
	"encoding/json"
	"fmt"
	"net/http"
	"path"
	"strings"

	"exercises/zip/zipserver/models"
)

//ZipIndexHandler handles requests that should
//return a slice of *Zip records for a given key
type ZipIndexHandler struct {
	index models.ZipIndex
}

//NewZipIndexHandler constructs a new ZipIndexHandler.
//The `index` parameter will be used to get the zips
//for the requested key.
func NewZipIndexHandler(index models.ZipIndex) *ZipIndexHandler {
	return &ZipIndexHandler{
		index: index,
	}
}

//ServeHTTP handles the HTTP requests. It is a receiver function, which means we will be able to
//access fields from `zih` as if it was `this` in java or javascript.
func (zih *ZipIndexHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
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

Once you type out all that code, you should be able to just run the code by doing `go install` in your terminal or powershell, then `zipserver` in your console. Try it and see if you get a `server running on port _____` message!

If you see that, try to navigate to `http://localhost:4000/zips/city/seattle` in your browser and see if you get any data back (if you used a different port, replace `4000` with that port). If you do, try another city you know of to see if it works! 

## ZipClient

The ZipClient has already been provided for you, but it uses an old url for it. We have not implemented the roothandler (just `http://localhost:4000/` without any routes) so don't worry about the "Fetch failed" message you may get. 

Find line 50's `fetch` statement and replace the `http://localhost:4000/search?q=` with `http://localhost:4000/zips/city/` to match the route you created. What we originally planned here was to use query parameters which is another way to pass data to a server-- both ways are valid, but our server code expects the url path method.

The rest should work!

Please let us know if you have any questions!