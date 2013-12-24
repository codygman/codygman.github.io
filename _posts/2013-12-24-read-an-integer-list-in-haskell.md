---
layout: post
title: "Read an integer list in Haskell"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Was browsing my feed and came across something similar to this in stack overflow. So really quickly wrote out the imperative version:

{% highlight haskell %}
readInts :: String -> [Int]
readInts x = read x

main = do
  input <- getLine
  let list = read input :: [Int]
  print list
{% endhighlight %}

Then I of course rewrote it to the condensed version:

{% highlight haskell %}
readInts :: String -> [Int]
readInts x = read x

main = do
  getLine >>= print . readInts
{% endhighlight %}

I attempted to get rid of the readInts function like this:

{% highlight haskell %}
main = getLine >>=  print . (read :: [Int])
{% endhighlight %}

However I was met with the type error:
{% highlight shell %}
{% endhighlight %}
