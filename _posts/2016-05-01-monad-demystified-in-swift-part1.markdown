---
title:  "Monad Demystified, in Swift: Part I"
date:   2016-05-01 14:48:23
categories: [Swift]
tags: [Swift, Haskell, Functional Programming]
---
**Monad** has always been a mysterious thing when programmer learning Functional Programming languages like Haskell and OCaml. Monad itself is such a meaningless word, so people like to use metaphors to describe monads. Some people often say monad is a burrito, some may describe monad as Voldemort (nobody wish to mention its name). I'd like to refer monad as *a type* of box, in which it can wrap something inside and it also can be unboxed (unwrapped) in certain ways. In general, monad is a type-safe and trouble-free way of chaining operations together like a railroad (aka smarter way of programming).

### Monad? Monad?!
Alright, so what is a **monad**?

> "A monad is just a monoid in the category of endofunctors" --StackOverflow

Well, that's too vague in every aspect for us to understand.

Actually **monad** is simple, but it just coincidentally got a scary name. It is a special type that has two functions, according to Haskell:

> A monad is a datatype that has two operations: `>>=` (aka bind) and `return` (aka unit).

For example, one of the most used monad, in Haskell:

```haskell
data Maybe t = Just t
             | Nothing
```
A type called `Maybe` that takes a argument has two constructor `Just t` that containing the value `t` or `Nothing`, containing literally nothing, `t` can be anything, an Int, Double, etc.

It is **Maybe Monad** in Haskell, and as every monad does, it has two key operations mentioned above, `>>=` and `return`:

```haskell
(>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b
Nothing  >>= _  =  Nothing    -- A failed computation returns Nothing
(Just x) >>= f  =  f x        -- Applies function f to value x
```
`>>=` (bind) takes a monad, unboxing it and then transforming it into another Maybe Monad which is the main idea of chaining.

![alt text][monad_bind_img]

```haskell
return :: a -> Maybe a
return x = Just x       -- Wraps value x, returning a value of type (Maybe a)
```
`return` simply giving out the boxed value.

An example illustrating the use of `Maybe` in looking up certain pair in a dictionary:

```python
ghci> let dictionary = [(1, "one"), (2, "two"), (3, "three"), (4, "four")]
ghci> lookup 1 dictionary
Just "one"
ghci> lookup 5 dictionary
Nothing
```

### Monad in Swift

As reading through, sophisticated Swift developer should find these quiet familiar as we're actually dealing with `Maybe Monad` everyday when we're writing `Optional`.

```swift
1> let dictionary = [1: "one", 2: "two", 3: "three", 4: "four"]
2> print(dictionary[1])
Optional("one")
3> print(dictionary[5])
nil
```

You may wonder where's `>>=` (bind) in Swift? Actually it just got a different name, called `flatMap`. So here's my short explanation of monad in Swift.

> In Swift, any type that can be `flatMap` over is a **monad**.

Therefore, don't be surprised, `Array` is also a monad because it has `flatMap` defined. `Optional` is a monad. `RACSignal` gotten from *ReactiveCocoa* or `Observable` from *RxSwift* are monads. `Result` pulled from *Alamofire* is a monad. And even `Promise` in *PromiseKit* or in *Javascript* is also a monad ...

In iOS development community, most people who use Objective-C familiar with `performanceSelector:`, but not many people know `map`, `flatMap`, `filter`, or `reduce` in Swift, which are the functional features that makes Swift so fascinating and beautiful.

Let's take a closer look at how `Optional` and `flatMap` are defined:

```swift
enum Optional<Wrapped> {
    case None
    case Some(Wrapped)
}
```
An optional type can be either contains `Some` value or `None`. Maybe few `init:` would make the code more comprehensive, but not discuss for here.

```swift
extension Optional {
    public func flatMap<U>(@noescape f: Wrapped -> U?) -> U? {
        switch self {
            case .Some(let x):
                return f(x)
            case .None:
                return nil
        }
    }
}
```
As you can see, it is just syntactic sugar as the `>>=` in Haskell seen above. If there's something wrapped in current optional, then bind it to another optional (another monad). If none, then return none (still another monad, remember `.None` is also an `Optional`).

Also, we can define the same operator `>>=` (aka bind) as in Haskell.

```swift
infix operator >>= { associativity left }
func >>=<T, U>(a: T?, f: T -> U?) -> U? {
    return a.flatMap(f)
}
```
Here, I define a new function that takes an `Int` and divides three, then return `Optional<Int>`. Return the computed result if it is still an integer after division; otherwise return nil.

```swift
func divideThree(a: Int) -> Int? {
    return a % 3 == 0 ? a / 3 : nil
}
```

Chain the function three times and check the printed values.

```swift
print(Optional(9) >>= divideThree)
print(Optional(9) >>= divideThree >>= divideThree)
print(Optional(9) >>= divideThree >>= divideThree >>= divideThree)
```

```ruby
Optional(3)
Optional(1)
nil
```

Just think how much line of code would *Imperative Programming* would have. Also, in *Functional Programming*, the input matches the output; thus, bug has less chance to take place.


[monad_bind_img]: https://www.uraimo.com/imgs/bind.png
