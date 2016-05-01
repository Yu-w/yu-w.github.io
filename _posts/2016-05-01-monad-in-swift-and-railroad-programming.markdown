---
title:  "Moand in Swift and Railroad Programming"
date:   2016-05-01 14:48:23
categories: [Swift]
tags: [Swift, Haskell, Functional Programming]
---
### Monad? Monad?!
**Monad** has always been a mysterious thing when programmer learning Functional Programming language like Haskell or OCaml. Monad itself is such meaningless work, so people like to use metaphor to describe monad. Some people often say monad is a burrito, some describe monad as Voldemort (nobody wish to mention its name). I'd like to refer monad as a type of box, in which it can wrap something inside normally and also can be unboxed(unwrapped) in certain way. It is a typesafe way of chaining operations together like a railroad.

Alright, so what is a **monad**?

> "A monad is just a monoid in the category of endofunctors" --StackOverflow

Well, that's still too vague in every aspect for us to understand.

**Monad** is simple but it just coincidentally got a scary name. It is just a special type that has two functions. According to Haskell:

> A monad is a datatype that has two operations: `>>=` (aka bind) and `return` (aka unit).

For example, the one of the most used monad, in Haskell:

```haskell
data Maybe t = Just t
             | Nothing
```
A type called `Maybe` that takes a argument has two constructor `Just t` that containing the value `t` or `Nothing`, containing literally nothing, `t` can be anything, an Int, Double, etc.

It is **Maybe Monad** in Haskell, and as every monad does, it has two key operations mentioned above `>>=` and `return`:

```haskell
(>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b
Nothing  >>= _  =  Nothing    -- A failed computation returns Nothing
(Just x) >>= f  =  f x        -- Applies function f to value x
```

`>>=` (bind) takes a monad, unboxing it and then transforming it into another Maybe Monad

```haskell
return :: a -> Maybe a
return x = Just x       -- Wraps value x, returning a value of type (Maybe a)
```
`return` simply giving out the boxed value.


As writing through, sophisticated Swift developer should find these quiet familiar as we're actually dealing with `Maybe Monad` everything when we're writing `optionals`.

In iOS development community, most people who use Objective-C familiar with `performanceSelector:`, but not many people knows `map`, `flatMap`, `filter`, or `reduce` (`fold` in Haskell) in Swift
