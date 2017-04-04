# Pacemaker

This library provides an API for connecting to and working with the
Pacemaker cluster manager, specifically with the cluster configuration
(the CIB), from the Go programming language.

**Note:** This API is under heavy development.

Current features:

* Connect and get CIB as an XML `[]byte` block
* Decode CIB attributes and status section into a Go object structure
* Encode status section as JSON

Major missing features:

* Decoding / encoding configuration section
* Writing changes back to the CIB


* Get CIB as JSON
* Get CibObjects as JSON
* Make changes
* Create CibObjects
* Get status of resources and nodes
* History information
* Meta information about agents etc.



## Compatibility

Right now, I'm mainly focused on coming up with a good mapping between
native Go code and the CIB. The current version as of this work is
Pacemaker 1.1.15 with 1.1.16 to be released very soon, and I am making
no effort to stay compatible with older versions. On the flip side, no
effort is being made to tie the code to a specific release either.

## Compilation

The compile-time dependencies are Pacemaker, glib 2.0 and libxml2.

On openSUSE and similar distributions, this will get you all the
dependencies needed to compile:

    zypper in libpacemaker-devel libxml2-devel glib2-devel

To run the tests, the pacemaker schema files need to be available as
well. These are usually packaged separately, so to get these, you will
need to install the `pacemaker` package as well:

    zypper in pacemaker

## Usage

To include the library, import `github.com/krig/go-pacemaker`.

See `pacemaker_test.go` for usage examples.

Here's a brief example of retrieving a nicely formatted JSON version
of the current cluster status:

```go
cib, err := pacemaker.OpenCib()
defer cib.Close()
if err != nil {
	log.Fatal(err)
}

err = cib.Decode()
if err != nil {
    log.Fatal(err)
}

data, err := cib.ToJson()
if err != nil {
    log.Fatal(err)
}

var prettyJSON bytes.Buffer
err = json.Indent(&prettyJSON, data, "", "  ")
if err != nil {
    log.Fatal(err)
}

jsonstr := prettyJSON.String()
fmt.Println(jsonstr)
```
