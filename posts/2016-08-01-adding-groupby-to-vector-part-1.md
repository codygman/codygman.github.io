---
layout: post
title: "Adding GroupBy to vector part 1: Initial implementation"
description: "Initial research and implementation of GroupBy for Haskell's vector library"
category: 
tags: [Haskell vector]
---

One of my primary interests is real world application of functional programming paradigms and strong static type systems, namely with Haskell. It's only natural that after doing some data analysis at work that involved SQL-like operations in memory, I began to wonder what those operations would look like in Haskell. Not only how they would look, but how would they compare to the Go implementation in:

1) How naturally could the language express the business logic?
2) How performant is the end solution? How hard is optimizing it?
3) What advantages or disadvantages did Haskell's strong type system give?
4) What advantages or disadvantages did functional programming have?
5) Which one was better?
6) Which one was more enjoyable?

While trying to implement a task similar to one at work, I decided to use the [highly performant Haskell vector library](https://hackage.haskell.org/package/vector) since I was using around 6GB of data in memory. An alternative could be using something like [pipes](https://hackage.haskell.org/package/pipes) or [conduit](https://hackage.haskell.org/package/conduit)with regular lists, or in combination with vectors. I'll almost certainly explore these alternatives in future posts.

This post is about something I couldn't find in the vector library that was central to the kind of calculations I was doing: GroupBy

[Groupby is present in Haskell's Data.List library](https://hackage.haskell.org/package/base-4.9.0.0/docs/Data-List.html#v:groupBy), but not in the vector library. Wondering why this was, I started googling around for discussion. Someone else had to have needed something like groupBy that is so central to many types of data processing, right? Looks like someone did ask in June 2014 in #haskell:

```
2014-06-14 00:36:32 +0200	<intrados> 	Am I likely to get better performance with `Vector.fromList . groupBy p . Vector.toList` or a naively written Vector-native groupBy?
2014-06-14 00:37:32 +0200	<carter> 	intrados: write it using Vector.Generic
```

I also searched for the key term "group" on [vector's github issues](https://github.com/haskell/vector/issues?utf8=%E2%9C%93&q=group%20) but didnt' find anything.

I did however find a "groupBy" in [ClassyPrelude Vector](), the source is below:

```haskell
instance CanGroupBy (Vector a) a where
    -- | Implementation is stolen from <http://hackage.haskell.org/packages/archive/storablevector/latest/doc/html/src/Data-StorableVector.html#groupBy>
    groupBy k xs =
      switchL []
        (\h t ->
          let n = 1 + findIndexOrEnd (not . k h) t
          in  Vector.unsafeTake n xs : groupBy k (Vector.unsafeDrop n xs))
        xs
```

That says it is stolen from [Data.StoreableVector](http://hackage.haskell.org/packages/archive/storablevector/latest/doc/html/src/Data-StorableVector.html#groupBy) whose latest docs (at time of writing) [can be found here](http://hackage.haskell.org/package/storablevector-0.2.10/docs/Data-StorableVector.html).

Their latest implementation looks like this:

```haskell
groupBy :: (Storable a) => (a -> a -> Bool) -> Vector a -> [Vector a]
groupBy k xs =
   switchL []
      (\ h t ->
          let n = 1 + findIndexOrEnd (not . k h) t
          in  unsafeTake n xs : groupBy k (unsafeDrop n xs))
      xs
{-# INLINE groupBy #-}
```
