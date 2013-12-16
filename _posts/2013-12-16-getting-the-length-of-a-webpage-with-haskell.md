---
layout: post
title: "Getting the length of a webpage with haskell"
description: ""
category: 
tags: []
---
{% include JB/setup %}

I was wanting to see how many words were on a webpage today, so I opened up a python interpreter and typed in the following code:

{% highlight python %}
import urllib
print len(urllib.urlopen("http://www.reddit.com").read().split())
{% endhighlight %}

Very succinct code that does what I need it to. I've been using haskell lately though and wondered what it's solution would look like. First I wrote a very imperative version:

{% highlight haskell %}
main = putStrLn "Enter a url: "
     url <- getLine
     pageText <- C.simpleHttp url
     let pageTextCount = length . words . BCL.unpack $ pageText
     print $ "There are " ++ show pageTextCount ++ " words!"
{% endhighlight %}

This isn't nearly as short as the python version, but its still pretty understandable to most. Having to unpack the lazy bytestring might throw a few off, but everything else is straightforward. After writing this, I realized I could use bind and functional composition to shorten things up even more than python!

{% highlight haskell %}
import Network.HTTP.Conduit
import qualified Data.ByteString.Lazy.Char8 as BCL
main = getLine >>= simpleHttp >>= print . length . BCL.words
{% endhighlight %}

After that I wondered if I could use the (official?) Network.HTTP package easier, and made a very similar version with it.

{% highlight haskell %}
import Network.HTTP
main = getLine >>= simpleHTTP . getRequest >>= getResponseBody >>= print . length . words
{% endhighlight %}

The main difference being that Network.HTTP which returns String's instead of Lazy Bytestrings. However it's not shorter because it doesn't return the page content directly. My full working code example is below or you can look at the <a href="https://gist.github.com/codygman/7984368">github gist</a>.
{% highlight haskell %}
import qualified Data.ByteString.Char8 as BC
import qualified Data.ByteString.Lazy.Char8 as BCL
import qualified Network.HTTP.Conduit as C
import Network.HTTP

pageCountShort :: IO ()
-- prints word count of a page using Network.HTTP.Conduit
pageCountShort = getLine >>= C.simpleHttp >>= print . length . words . BCL.unpack 

main :: IO ()
main = do
	-- pageCountVerbose
	pageCountShort
	-- pageCountShortAlt

-- other ways of doing this
pageCountVerbose :: IO ()
-- imperative and verbose first try getting number of words on a webpage
pageCountVerbose = do
	putStrLn "Enter a url: "
	url <- getLine
	pageText <- C.simpleHttp url
	let pageTextCount = length . words . BCL.unpack $ pageText
	print $ "There are " ++ show pageTextCount ++ " words!"

pageCountShortAlt :: IO ()
-- print word count of a page using Network.HTTP
pageCountShortAlt = getLine >>= simpleHTTP . getRequest >>= getResponseBody >>= print . lengtwords
{% endhighlight %}

Hope this helps :)
