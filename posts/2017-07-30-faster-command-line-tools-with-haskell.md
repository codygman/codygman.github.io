---
layout: post
title: "Faster command line tools with Haskell"
description: "Initial research and implementation of GroupBy for Haskell's vector library"
category: 
tags: [Haskell vector]
---

Inspired by [Faster command line tools with Go](https://aadrake.com/posts/2017-05-29-faster-command-line-tools-with-go.html) which itself was inspired by [Faster Command Line Tools in D](http://dlang.org/blog/2017/05/24/faster-command-line-tools-in-d/).

The original problem statement:



>It’s a common programming task: Take a data file with fields separated by a delimiter (comma, tab, etc), and run a mathematical calculation involving several of the fields. Often these programs are one-time use scripts, other times they have longer shelf life. Speed is of course appreciated when the program is used more than a few times on large files.

> The specific exercise we’ll explore starts with files having keys in one field, integer values in another. … Fields are delimited by a TAB, and there may be any number of fields on a line. The file name and field numbers of the key and value are passed as command line arguments.

We'll use the same file described in [Faster command line tools with Go](https://aadrake.com/posts/2017-05-29-faster-command-line-tools-with-go.html) :

> The data we will use is a file of n-grams from the Google Books dataset. The file is 184 Megabytes, uncompressed, and has a total of 10,512,769 lines.

First, lets set a baseline by getting the runtime for the fastest Go program from that post. I'm using Go 1.6 which is missing quite a few performance improvements, so I'll update to Go 1.8 using the `ppa:gophers/archive` ppa.

All done, now the fastest version of the Golang code that operates on bytes directly can be found at the bottom of , but it's short enough to paste inline here:

```go
func processLine(b []byte) (int, int) {
	key := 0
	val := 0
	i := 0
	for b[i] != '\t' {
		i++
	}
	for i++; b[i] != '\t'; i++ {
		key = key*10 + int(b[i]) - '0'
	}
	for i++; b[i] != '\t'; i++ {
		val = val*10 + int(b[i]) - '0'
	}
	return key, val
}

func processFile(file *os.File) (int, int) {
	var sumByKey [2009]int

	scanner := bufio.NewScanner(file)
	for scanner.Scan() {
		line := scanner.Bytes()
		k1, v1 := processLine(line)
		sumByKey[k1] += v1
	}
	var k int
	var v int
	for i, val := range sumByKey {
		if val > v {
			k = i
			v = val
		}
	}
	return k, v

}
```

And it's benchmark:

```go
func Benchmark_processFile(b *testing.B) {
	file, err := os.Open("../ngrams.tsv")
	defer file.Close()
	if err != nil {
		b.Fatal(err)
	}

	data, err := ioutil.ReadAll(file)
	if err != nil {
		b.Fatal(err)
	}

	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		k, v := processFile(data)
		if k != 2006 || v != 22569013 {
			b.Fatalf(`bad result %v | %v`, k, v)
		}
	}
}

```

The code from the bottom of the [Faster command line tools with Go](https://aadrake.com/posts/2017-05-29-faster-command-line-tools-with-go.html) post actually gave a compiler error because the benchmark didn't match exactly to the code given. After fixing that, here is the code I used ([Github](https://github.com/codygman/faster-command-line-tools-with-haskell/tree/master/go)):

```go
func processLine(b []byte) (int, int) {
	key := 0
	val := 0
	i := 0
	for b[i] != '\t' {
		i++
	}
	for i++; b[i] != '\t'; i++ {
		key = key*10 + int(b[i]) - '0'
	}
	for i++; b[i] != '\t'; i++ {
		val = val*10 + int(b[i]) - '0'
	}
	return key, val
}

func processFile(file *os.File) (int, int) {
	file.Seek(0, 0)
	var sumByKey [2009]int

	scanner := bufio.NewScanner(file)
	for scanner.Scan() {
		line := scanner.Bytes()
		k1, v1 := processLine(line)
		sumByKey[k1] += v1
	}
	var k int
	var v int
	for i, val := range sumByKey {
		if val > v {
			k = i
			v = val
		}
	}
	return k, v

}
```

Benchmark:

```go
package main

import (
	"os"
	"testing"
)

func Benchmark_processFile(b *testing.B) {
	file, err := os.Open("../ngrams.tsv")
	defer file.Close()
	if err != nil {
		b.Fatal(err)
	}
	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		k, v := processFile(file)
		if k != 2006 || v != 22569013 {
			b.Fatalf(`bad result %v | %v`, k, v)
		}
	}
}
```

And it's runtime:

```
cody@zentop:~/faster-command-line-tools-with-haskell/go$ go test -v -bench=.
Benchmark_processFile-4                5         252309722 ns/op
PASS
ok      _/home/cody/faster-command-line-tools-with-haskell/go   2.274s
cody@zentop:~/faster-command-line-tools-with-haskell/go$ go test -v -bench=.
Benchmark_processFile-4                5         252163852 ns/op
PASS
ok      _/home/cody/faster-command-line-tools-with-haskell/go   2.274s
cody@zentop:~/faster-command-line-tools-with-haskell/go$ go test -v -bench=.
Benchmark_processFile-4                5         252327565 ns/op
PASS
ok      _/home/cody/faster-command-line-tools-with-haskell/go   2.274s
```

Now, I want to create a Haskell version and compare it's performance and if necessary, the process of profiling performance!

Navigating back...

`cd /home/cody/faster-command-line-tools-with-haskell`

Creating a new Haskell project using [LTS Haskell 9.0 (ghc-8.0.2)](https://www.stackage.org/lts-9.0):

`
stack new haskell-version simple --resolver lts-9.0
`

We'll use the [bytestring](https://www.stackage.org/lts-9.0/package/bytestring-0.10.8.1) package by adding it to `haskell-version.cabal`:

```
name:                haskell-version
version:             0.1.0.0
... snip ...
build-depends:       base >= 4.7 && < 5
                     , bytestring >= 0.10.8.11

```
