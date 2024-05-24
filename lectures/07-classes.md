---
title: Typeclasses
date: 2019-05-29
headerImg: books.jpg
---

## Past two Weeks

How to *implement* a language?

- Interpreter
- Parser

## Next two Weeks

Modern language features for structuring programs

- Type classes
- Monads

Will help us add cool features to the Nano interpreter!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Type Classes: Outline

1. **Why type classes?**
2. Standard type classes
3. Creating new instances
4. Using type classes
5. Creating new type classes

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Overloading Operators: Arithmetic

The `+` operator works for a bunch of different types.

For `Integer`:

```haskell
λ> 2 + 3
5
```

for `Double` precision floats:

```haskell
λ> 2.9 + 3.5
6.4
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Overloading Comparisons

Similarly we can _compare_ different types of values

```haskell
λ> 2 == 3
False

λ>  [2.9, 3.5] == [2.9, 3.5]
True

λ> ("cat", 10) < ("cat", 2)
False

λ> ("cat", 10) < ("cat", 20)
True
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Operator Overloading

Seems unremarkable?

Languages have supported "operator overloading" since the dawn of time

<br>
<br>
<br>
<br>
<br>

## Haskell has no caste system

No distinction between operators and functions

- All are first class citizens!

You can implement *functions* like `(+)` and `(==)` yourself from scratch!

- But then, what type do we give them?

<br>
<br>
<br>
<br>
<br>

## QUIZ

Which of the following type annotations would work for `(+)` ?

**(A)** `(+) :: Int    -> Int    -> Int`

**(B)** `(+) :: Double -> Double -> Double`

**(C)** `(+) :: a      -> a      -> a`

**(D)** _Any_ of the above

**(E)** _None_ of the above

<br>

(I) final

    *Answer:* E

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

`Int -> Int -> Int` is bad because?

- Then we cannot add `Double`s!

`Double -> Double -> Double` is bad because?

- Then we cannot add `Int`s!

`a -> a -> a` is bad because?

- I don't know how to implement this

- For some `a`s it doesn't make sense: how do I add two `Bool`s? Or two `Char`s?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Ad-hoc Polymorphism

We have seen **parametric polymorphism**:

```haskell
-- Append two lists:
(++) :: [a] -> [a] -> [a]
(++) []     ys = ys
(++) (x:xs) ys = x:(xs ++ ys)
```

`(++)` works for all list types 

  - *Doesn't care* what the list elements are
  
  - *The same* implementation works for `a = Int`, `a = Bool`, etc.

<br>
<br>

Now we need **ad-hoc polymorphism**:
```haskell
(+) :: a -> a -> a -- Almost, but not really 
(+) x y = ???
```

`(+)` should work for many (but not all) types

  - *Different* implementation for `a = Int`, `a = Double`, etc.

*Ad-hoc* means "created or done for a particular purpose as necessary."

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Type Classes for Ad Hoc Polymorphism 

Haskell solves this problem with a mechanism called *type classes*

- Introduced by [Wadler and Blott](http://portal.acm.org/citation.cfm?id=75283) 

![](/static/img/blott-wadler.png)

This is a very cool and well-written paper! Read it!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Constrained Types

Let's ask GHCi:

```haskell
λ> :type (+)
(+) :: (Num a) => a -> a -> a
```

We call this a **constrained** (or **qualified**) type

<br>
<br>

Read it as:

  - `(+)` takes in two `a` values and returns an `a` value  
  - for any type `a` that 
  - _is an instance of_ the `Num` type class  
  
  - or, in Java terms: _implements_ the `Num` interface
  - [similar but not the same idea!](https://www.parsonsmatt.org/2017/01/07/how_do_type_classes_differ_from_interfaces.html)

The "`(Num a) =>`" part is called the *constraint*

<br>
<br>
<br>
<br>
<br>

Some types are `Num`s:

  - For example, `Int`, `Integer`, `Double`
  - Values of those types can be passed to `(+)`:
  
```haskell
λ> 2 + 3
5
```

<br>
<br>  

Other types *are not* `Num`s:

  - For example, `Bool`, `Char`, `String`, function types, ...
  - Values of those types _cannot_ be passed to `(+)`:

```haskell
λ> True + False

<interactive>:15:6:
    No instance for (Num Bool) arising from a use of ‘+’
    In the expression: True + False
    In an equation for ‘it’: it = True + False
```

<br>
<br>

**Aha!** _Now_ those `no instance for` error messages should make sense!

- Haskell is complaining that `True` and `False` are of type `Bool` 
- and that `Bool` is _not_ an instance of `Num`

<br>
<br>
<br>
<br>
<br>
<br>
<br> 

## QUIZ

What would be a reasonable type for the equality operator?

**(A)** `(==) :: a -> a -> a`

**(B)** `(==) :: a -> a -> Bool`

**(C)** `(==) :: (Eq a) => a -> a -> a`

**(D)** `(==) :: (Eq a) => a -> a -> Bool`

**(E)** None of the above


<br>

(I) final

    *Answer:* D. Not B because we can't compare functions!
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>    

## Type Classes: Outline

1. Why type classes? [done]
2. **Standard type classes**
3. Creating new instances
4. Using type classes
5. Creating new type classes

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## What is a type class?

A type class is a *collection of methods* (functions, operations) that must exist for every instance

<br>
<br>
<br>

What are some useful type classes in the Haskell standard library?

<br>
<br>
<br>
<br>
<br>
<br>

## The `Eq` Type Class

The simplest typeclass is `Eq`:

```haskell
class  Eq a  where
  (==) :: a -> a -> Bool
  (/=) :: a -> a -> Bool
```

A type `T` is _an instance of_ `Eq` if there are two functions

- `(==) :: T -> T -> Bool` that determines if two `T` values are _equal_
- `(/=)  :: T -> T -> Bool`  that determines if two `T` values are _disequal_

<br>
<br>

*Lifehack:* You can ask GHCi about a type class and it will tell you

  - its methods
  - all the instances it knows

```haskell
λ> :info Eq
class  Eq a  where
  (==) :: a -> a -> Bool
  (/=) :: a -> a -> Bool
...
instance Eq Int
instance Eq Double
...
```

<br>
<br>
<br>
<br>
<br>
<br>

## The `Num` Type Class

The type class `Num` requires that instances define a bunch of arithmetic operations

```haskell
class Num a where
  (+) :: a -> a -> a
  (-) :: a -> a -> a
  (*) :: a -> a -> a
  negate :: a -> a
  abs :: a -> a
  signum :: a -> a
  fromInteger :: Integer -> a
```

<br>
<br>
<br>
<br>
<br>
<br>

## The `Show` Type Class

The type class `Show` requires that instances be convertible to `String`

```haskell
class Show a  where
  show :: a -> String
```

Indeed, we can test this on different (built-in) types

```haskell
λ> show 2
"2"

λ> show 3.14
"3.14"

λ> show (1, "two", ([],[],[]))
"(1,\"two\",([],[],[]))"
```

<br>
<br>
<br>
<br>
<br>
<br>

## The `Ord` Typeclass

The type class `Ord` is for totally ordered values:

```haskell
class Eq a => Ord a where
  (<)  :: a -> a -> Bool
  (<=) :: a -> a -> Bool
  (>)  :: a -> a -> Bool
  (>=) :: a -> a -> Bool
```

For example:

```haskell
λ> 2 < 3
True

λ> "cat" < "dog"
True
```

<br>
<br>

Note `Eq a =>` in the class definition!

A type `T` _is an instance of_ `Ord` if

1. `T` is *also* an instance of `Eq`, and
2. It defines functions for comparing values for inequality

<!--
  TODO
  - Move Read and type annotations here
  - Rename "Using Typeclasses" to "Type Classes and Polymorphism"
-->

<br>
<br>
<br>
<br>
<br>
<br>

## Standard Typeclass Hierarchy

Haskell comes equipped with a rich set of built-in classes.

![Standard Typeclass Hierarchy](/static/img/haskell98-classes.png)

In the above picture, there is an edge from `Eq` to `Ord`
because for something to be an `Ord` it must also be an `Eq`.

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Type Classes: Outline

1. Why type classes? [done]
2. Standard type classes [done]
3. **Creating new instances**
4. Using type classes
5. Creating new type classes

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Showing Your Colors

<!-- Add parameters? Nice to show deriving -->

Let's create a new datatype:

```haskell
data Color = Red | Green
```
and play with it in GHCi:

```haskell
λ> let col = Red
λ> :type col
x :: Color
```

So far, so good... but we **cannot view** them!

```haskell
λ> col

<interactive>:1:0:
    No instance for (Show Color)
      arising from a use of `print' at <interactive>:1:0
    Possible fix: add an instance declaration for (Show Color)
    In a stmt of a 'do' expression: print it
```

Why is this happening?

<br>
<br>
<br>

When we type an expression into GHCi it: 

  1. evaluates it to a value, then
  2. calls `show` on that value to convert it to a string
  
But our new type is *not an instance* of `Show`!  

<br>
<br>
<br>
<br>
<br>
<br>

We also **cannot compare** colors!

```haskell
λ> col == Green

<interactive>:1:0:
    No instance for (Eq Color)
      arising from a use of `==' at <interactive>:1:0-5
    Possible fix: add an instance declaration for (Eq Color)
    In the expression: col == Green
    In the definition of `it': it = col == Green
```

<br>
<br>

How do we *add an instance declaration* for `Show Color` and `Eq Color`?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Creating Instances

To tell Haskell how to show or compare values of type `Color`

  - **create instances** of `Eq` and `Show` for that type:

```haskell
instance Show Color where
  show Red   = "Red"
  show Green = "Green"
  
instance Eq Color where
  ???
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## EXERCISE: Creating Instances

Create an instance of `Eq` for `Color`:

```haskell
data Color = Red | Green
  
instance Eq Color where
  ???

-- Reminder:
class  Eq a  where
  (==) :: a -> a -> Bool
  (/=) :: a -> a -> Bool
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## QUIZ 

Which of the following `Eq` instances for `Color` are valid?

```haskell
-- (A)
instance Eq Color where
  (==) Red   Red   = True
  (==) Green Green = True
  (==) _     _     = False

-- (B)
instance Eq Color where
  (/=) Red   Red   = False
  (/=) Green Green = False
  (/=) _     _     = True

-- (C) Neither of the above

-- (D) Either of the above
```

<br>

(I) final

    *Answer:* D

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Default Method Implementations   

The `Eq` class is actually defined like this:

```haskell
class  Eq a  where
  (==) :: a -> a -> Bool
  (==) x y = not (x /= y) -- Default implementation!
  
  (/=) :: a -> a -> Bool
  (/=) x y = not (x == y) -- Default implementation!
```

<br>
<br>

The class provides **default implementations** for its methods

  - An instance can define *any* of the two methods and get the other one for free
  
  - Use `:info` to find out which methods you have to define:
  
```haskell
λ> :info Eq
class  Eq a  where
  (==) :: a -> a -> Bool
  (/=) :: a -> a -> Bool
  {-# MINIMAL (==) | (/=) #-} -- HERE HERE!!!
```  

<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

If you define:

```haskell
instance Eq Color where
  -- Nothing here!
```

what will the following evaluate to?

```haskell
λ> Red == Green
```

**(A)** Type error

**(B)** Runs forever / stack overflow

**(C)** Other runtime error

**(D)** `False`

**(E)** `True`

<br>

(I) final

    *Answer:* B

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Automatic Derivation

This is silly: we _should_ be able to compare and view `Color`s "automatically"!

Haskell lets us _automatically derive_ functions for some classes in the standard library.

To do so, we simply dress up the data type definition with

```haskell
data Color = Red | Green
  deriving (Eq, Show) -- please generate instances automatically!
```

Now we have

```haskell
λ> let col = Red

λ> col
Red

λ> col == Red
True
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Type Classes: Outline

1. Why type classes? [done]
2. Standard type classes [done]
3. Creating new instances [done]
4. **Using type classes**
5. Creating new type classes

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Using Typeclasses

Now let's see how to write code that uses type classes!

<br>
<br>

Let's build a small library for dictionaries mapping keys `k` to values `v`

```haskell
data Dict k v
  = Empty                -- empty dictionary
  | Bind k v (Dict k v)  -- bind key `k` to the value `v`
  deriving (Show)
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>

## The API

We want to be able to do the following with `Dict`:

```haskell
>>> let dict0 = add "cat" 10.0 (add "dog" 20.0 empty)

>>> get "cat" dict0
10

>>> get "dog" dict0
20

>>> get "horse" dict0
error: key not found
```

<br>
<br>

Okay, lets implement!

```haskell
-- | 'add key val dict' returns a new dictionary
-- | that additionally maps `key` to `val`
add :: k -> v -> Dict k v -> Dict k v
add key val dict = ???

-- | 'get key dict' returns the value of `key` 
get :: k -> Dict k v -> v
get key dict = ???
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

But we get a type error!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Constraint Propagation

Lets _delete_ the types of `add` and `get` and see what Haskell says their types are! 

```haskell
λ> :type get
get :: (Eq k) => k -> v -> Dict k v -> Dict k v
```

Haskell tells us that we can use any `k` type as a *key*
as long as this type is an instance of the `Eq` typeclass.

How, did GHC figure this out? 

- If you look at the code for `get` you'll see that we check if two keys _are equal_!

<br>
<br>
<br>
<br>
<br>
<br>

## TRY AT HOME: A Faster Dictionary

Write an optimized version of

- `add` that ensures the keys are in _increasing_ order
- `get` that gives up the moment
   we see a key that's larger than the one we're looking for

_(How) do you need to change the types of `get` and `add`?_

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Explicit Type Annotations

Consider the standard typeclass `Read`:

```haskell
-- Not the actual definition, but almost:
class Read a where
  read :: String -> a
```

`Read` is the _opposite_ of `Show`
 
  - It requires that every instance `T` can parse a string and turn it into `T`
  
  - Just like with `Show`, most standard type are instances of `Read`:
  
    - `Int`, `Integer`, `Double`, `Char`, `Bool`, etc

<br>
<br>
<br>
<br>
<br>
<br>
<br>  
<br>
<br>  

## QUIZ

What does the expression `read "2"` evaluate to?

**(A)** Type error

**(B)** `"2"`

**(C)** `2`

**(D)** `2.0`

**(E)** Run-time error

<br>
<br>
<br>
<br>
<br>
<br>

Haskell is foxed!

- Doesn't know _what type_ to convert the string to!
- Doesn't know _which_ of the `read` functions to run!

Did we want an `Int` or a `Double` or maybe something else altogether?

<br>
<br>

Thus, here an **explicit type annotation** is needed to tell Haskell
what to convert the string to: 

```haskell
λ> (read "2") :: Int
2

λ> (read "2") :: Float
2.0

λ> (read "2") :: String
???
```

Note the different results due to the different types.

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Type Classes: Outline

1. Why type classes? [done]
2. Standard type classes [done]
3. Creating new instances [done]
4. Using type classes [done]
5. **Creating new type classes**

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Creating Typeclasses

Typeclasses are useful for *many* different things
  
  - Improve readability
  - Promote code reuse

We will see some very interesting use cases over the next few lectures.

<br>
<br>

Lets conclude today's class with a quick example that provides a taste.

<br>
<br>
<br>
<br>
<br>
<br>
<br>
 

## JSON

*JavaScript Object Notation* or [JSON](http://www.json.org/) is a simple format for
transferring data around. Here is an example:

```json
{ "name"    : "Nadia"
, "age"     : 39.0
, "likes"   : [ "poke", "tea", "pasta" ]
, "hates"   : [ "beets" , "milk" ]
, "lunches" : [ {"day" : "mon", "loc" : "fan fan"}
              , {"day" : "tue", "loc" : "home"}
              , {"day" : "wed", "loc" : "curry up now"}
              , {"day" : "thu", "loc" : "home"}
              , {"day" : "fri", "loc" : "rubios"} ]
}
```

Each JSON value is either

- a *base* value like a string, a number or a boolean,

- an (ordered) *array* of values, or

- an *object*, i.e. a set of *string-value* pairs.

<br>
<br>
<br>
<br>
<br>
<br>
<br>

## A JSON Datatype

We can represent (a subset of) JSON values with the Haskell datatype

```haskell
data JVal
  = JStr  String
  | JNum  Double
  | JBool Bool
  | JObj  [(String, JVal)]
  | JArr  [JVal]
  deriving (Eq, Ord, Show)
```

<br>
<br>

The above JSON value would be represented by the `JVal`

```haskell
js1 =
  JObj [("name", JStr "Nadia")
       ,("age",  JNum 38.0)
       ,("likes",   JArr [ JStr "poke", JStr "coffee", JStr "pasta"])
       ,("hates",   JArr [ JStr "beets", JStr "milk"])
       ,("lunches", JArr [ JObj [("day",  JStr "mon")
                                ,("loc",  JStr "fan fan")]
                         , JObj [("day",  JStr "tue")
                                ,("loc",  JStr "home")]
                         , JObj [("day",  JStr "wed")
                                ,("loc",  JStr "curry up now")]
                         , JObj [("day",  JStr "thu")
                                ,("loc",  JStr "home")]
                         , JObj [("day",  JStr "fri")
                                ,("loc",  JStr "rubios")]
                         ])
       ]  
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Serializing Haskell Values to JSON

Lets write a small library to _serialize_ Haskell values as JSON

  - Base types `String`, `Double`, `Bool` are serialized as base JSON values
  - Lists are serialized into JSON arrays
  - Lists of key-value pairs are serialized into JSON objects
  
<br>
<br>  

We could write a bunch of functions like

```haskell
doubleToJSON :: Double -> JVal
doubleToJSON = JNum

stringToJSON :: String -> JVal
stringToJSON = JStr

boolToJSON   :: Bool -> JVal
boolToJSON   = JBool
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Serializing Lists

But what about collections, namely _lists_ of things?

```haskell
doublesToJSON    :: [Double] -> JVal
doublesToJSON xs = JArr (map doubleToJSON xs)

boolsToJSON      :: [Bool] -> JVal
boolsToJSON xs   = JArr (map boolToJSON xs)

stringsToJSON    :: [String] -> JVal
stringsToJSON xs = JArr (map stringToJSON xs)
```

<br>
<br>

This is **getting rather tedious**

- Lots of repetition :(

<br>
<br>
<br>
<br>
<br>
<br>

## Serializing Collections (refactored with HOFs)

You could abstract by making the *element-converter* a parameter

```haskell
listToJSON :: (a -> JVal) -> [a] -> JVal
listToJSON f xs = JArr (map f xs)

mapToJSON :: (a -> JVal) -> [(String, a)] -> JVal
mapToJSON f kvs = JObj [ (k, f v) | (k, v) <- kvs ]
```

<br>
<br>

But this is *still rather tedious* as you have to pass 
in the individual data converter (yuck)

```haskell
λ> doubleToJSON 4
JNum 4.0

λ> listToJSON stringToJSON ["poke", "coffee", "pasta"]
JArr [JStr "poke",JStr "coffee",JStr "pasta"]

λ> mapToJSON stringToJSON [("day", "mon"), ("loc", "fan fan")]
JObj [("day",JStr "mon"),("loc",JStr "fan fan")]
```
<br>
<br>

This gets more hideous when you have richer objects like

```haskell
lunches = [ [("day", "mon"), ("loc", "fan fan")]
          , [("day", "tue"), ("loc", "home")]
          ]
```

because we have to go through gymnastics like

```haskell
λ> listToJSON (mapToJSON stringToJSON) lunches
JArr [ JObj [("day",JStr "monday")   ,("loc",JStr "fan fan")]
     , JObj [("day",JStr "tuesday")  ,("loc",JStr "home")]
     ]
```

Yikes. So much for _readability_

Is it too much to ask for a magical `toJSON` that _just works?_

<br>
<br>
<br>
<br>
<br>
<br>

## Typeclasses To The Rescue

Lets _define_ a typeclass that describes types `a` that can be converted to JSON.

```haskell
class JSON a where
  toJSON :: a -> JVal
```

Now, just make all the above instances of `JSON` like so

```haskell
instance JSON Double where
  toJSON = JNum

instance JSON Bool where
  toJSON = JBool

instance JSON String where
  toJSON = JStr
```

This lets us uniformly write

```haskell
λ> toJSON 4
JNum 4.0

λ> toJSON True
JBool True

λ> toJSON "guacamole"
JStr "guacamole"
```
<br>
<br>
<br>
<br>
<br>
<br>

## Bootstrapping Instances

The real fun begins when we get Haskell to automatically
bootstrap the above functions to work for lists and key-value lists!

```haskell
instance JSON a => JSON [a] where
  toJSON xs = JArr [toJSON x | x <- xs]
```

The above says, if `a` is an instance of `JSON`, that is,
if you can convert `a` to `JVal` then here's a generic
recipe to convert lists of `a` values!

```haskell
λ> toJSON [True, False, True]
JArr [JBln True, JBln False, JBln True]

λ> toJSON ["cat", "dog", "Mouse"]
JArr [JStr "cat", JStr "dog", JStr "Mouse"]
```

or even lists-of-lists!

```haskell
λ> toJSON [["cat", "dog"], ["mouse", "rabbit"]]
JArr [JArr [JStr "cat",JStr "dog"],JArr [JStr "mouse",JStr "rabbit"]]
```

We can pull the same trick with key-value lists

```haskell
instance (JSON a) => JSON [(String, a)] where
  toJSON kvs = JObj [ (k, toJSON v) | (k, v) <- kvs ]
```

after which, we are all set!

```haskell
λ> toJSON lunches
JArr [ JObj [ ("day",JStr "monday"), ("loc",JStr "zanzibar")]
     , JObj [("day",JStr "tuesday"), ("loc",JStr "farmers market")]
     ]
```

<br>
<br>
<br>

It is also useful to bootstrap the serialization for tuples (upto some
fixed size) so we can easily write "non-uniform" JSON objects where keys
are bound to values with different shapes.

```haskell
instance (JSON a, JSON b) => JSON ((String, a), (String, b)) where
  toJSON ((k1, v1), (k2, v2)) =
    JObj [(k1, toJSON v1), (k2, toJSON v2)]

instance (JSON a, JSON b, JSON c) => JSON ((String, a), (String, b), (String, c)) where
  toJSON ((k1, v1), (k2, v2), (k3, v3)) =
    JObj [(k1, toJSON v1), (k2, toJSON v2), (k3, toJSON v3)]

instance (JSON a, JSON b, JSON c, JSON d) => JSON ((String, a), (String, b), (String, c), (String,d)) where
  toJSON ((k1, v1), (k2, v2), (k3, v3), (k4, v4)) =
    JObj [(k1, toJSON v1), (k2, toJSON v2), (k3, toJSON v3), (k4, toJSON v4)]

instance (JSON a, JSON b, JSON c, JSON d, JSON e) => JSON ((String, a), (String, b), (String, c), (String,d), (String, e)) where
  toJSON ((k1, v1), (k2, v2), (k3, v3), (k4, v4), (k5, v5)) =
    JObj [(k1, toJSON v1), (k2, toJSON v2), (k3, toJSON v3), (k4, toJSON v4), (k5, toJSON v5)]
``` 

Now, we can simply write

```haskell
hs = (("name"   , "Nadia")
     ,("age"    , 38.0)
     ,("likes"  , ["poke", "coffee", "pasta"])
     ,("hates"  , ["beets", "milk"])
     ,("lunches", lunches)
     )     
```

which is a Haskell value that describes our running JSON example, 
and can convert it directly like so

```haskell
js2 = toJSON hs
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## EXERCISE: Dictionary with Default Values

Modify our implementation of fast dictionaries
so that `get` returns a default value if the key is not found.

You should get the following behavior:
  
```haskell
-- Note: the default value is NOT passed into `get`
λ> get 2       (add 5 "five"   (add 10 "ten"  (add 3 "three" empty)))
""

λ> get "horse" (add "cat" 10.0 (add "dog" 20.1 empty))
0.0
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


That's all folks! 