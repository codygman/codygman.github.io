---
layout: post
title: Go vs Haskell: Text processing lorem ipsum
description: ""
category: 
tags: []
---

Inspired by: [comparing text processing performance of nodejs, python,](http://honza.ca/2012/10/haskell-strings) and [golang](http://www.reddit.com/r/golang/comments/12196f/simple_text_processing_benchmark_vs_node_python/).



*File Size*:

```shell
cody@cody-XPS-L521X:~/programming/benchmarks/lorem-ipsum-text-processing/haskell$ ls -larth input.txt 
-rw-rw-r-- 1 cody cody 171M Dec 14 04:37 input.txt
```

##Haskell##

```haskell
module Main where

import qualified Data.ByteString as B
import           Data.Word8      (Word8, isAsciiLower, _cr, _nbsp, _space, _tab)

isSpace :: Word8 -> Bool
isSpace w = w == _space || w == _nbsp || w <= _cr && w >= _tab

toUpper :: Word8 -> Word8
toUpper w = if isAsciiLower w then w - _space else w

op :: Bool -> Word8 -> (Bool, Word8)
op flag c = if isSpace c then (True,c) else (False,d)
where d = if flag then toUpper c else c

main :: IO ()
main = B.readFile "input.txt" >>= B.putStr . snd . B.mapAccumL op True
```

```shell
cody@cody-XPS-L521X:~/programming/benchmarks/lorem-ipsum-text-processing/haskell$ time ./.cabal-sandbox/bin/lorem-ipsum-processing > output.txt 

real	0m1.165s
user	0m0.524s
sys	0m0.640s
```

##Go##

```go
package main

import (
  "bytes"
  "os"
  "log"
  "io"
)

func main() {
  b := make([]byte, 4096)
  f, err := os.Open("large-file.txt")
  if err != nil {
    log.Fatal("Could not read from large-file.txt")
  }

  for {
    // Read from file
    n, err := f.Read(b)
    if err == io.EOF {
      break
    } else if err != nil {
      log.Fatal("An error occurred reading from large-file.txt.")
    }

    bTitle := bytes.Title(b[0:n])

    // Write to stdout
    if _, err := os.Stdout.Write(bTitle); err != nil {
      log.Fatal("An error occurred writing to Stdout.")
    }
  }
}
```


```shell
cody@cody-XPS-L521X:~/programming/benchmarks/lorem-ipsum-text-processing/go$ time ./lorem large-file.txt > output.txt

real	0m3.970s
user	0m3.322s
sys	0m0.645s
```

##Bonus: Very short (but not as performant Haskell version):##
```haskell
import           Data.Char
import qualified Data.Text.Lazy    as T
import qualified Data.Text.Lazy.IO as TIO

convert = T.tail . T.scanl (\ a b -> if isSpace a then toUpper b else b) ' '

main = TIO.readFile "input.txt" >>= (\fc -> TIO.putStrLn $ convert fc)
```

```shell
cody@cody-XPS-L521X:~/programming/benchmarks/lorem-ipsum-text-processing/haskell$ time ./.cabal-sandbox/bin/lorem-ipsum-processing > output.txt 

real	0m19.787s
user	0m17.910s
sys	0m1.854s
```
