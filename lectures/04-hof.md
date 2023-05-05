---
title: Higher-Order Functions 
date: 2022-10-18
headerImg: books.jpg
---

## Bottling Computation Patterns

Another way to practice **D.R.Y**:

  * spot common computation patterns

  * refactor them into reusable *higher-order functions* (HOFs)

  * useful library HOFs: `map`, `filter`, and `fold`
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>    


## Example: evens

Let's write a function `evens` that extracts only even elements from an integer list.

```haskell
-- evens []        ==> []
-- evens [1,2,3,4] ==> [2,4]
evens       :: [Int] -> [Int]
evens []     = ... 
evens (x:xs) = ...
```

<br>
<br>
<br>
<br>
<br>
<br>

## Example: four-letter words

Let's write a function `fourChars`:

```haskell
-- fourChars [] ==> []
-- fourChars ["i","must","do","work"] ==> ["must","work"]
fourChars :: [String] -> [String]
fourChars []     = ... 
fourChars (x:xs) = ...
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

## Yikes, Most Code is the Same

Lets rename the functions to `foo`:

```haskell
foo []            = []
foo (x:xs)
  | x mod 2 == 0  = x : foo xs
  | otherwise     =     foo xs

foo []            = []
foo (x:xs)
  | length x == 4 = x : foo xs
  | otherwise     =     foo xs
```

Only difference is the **condition**

- `x mod 2 == 0` vs `length x == 4`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Moral of the day

<br>

**D.R.Y.** Don't Repeat Yourself!

<br>

Can we 

  * *reuse* the general pattern and
  * *substitute in* the custom condition?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## HOFs to the rescue!

General **Pattern**
  
  - expressed as a *higher-order function*
  - takes customizable operations as *arguments* 

Specific **Operation**

  - passed in as an argument to the HOF


<br>
<br>
<br>
<br>
<br>
<br>

## The "filter" pattern

![The `filter` Pattern](/static/img/filter-pattern.png)

General Pattern

- HOF `filter` 
- Recursively traverse list and pick out elements that satisfy a predicate

Specific Operations

- Predicates `isEven` and `isFour`

![`filter` instances](/static/img/filter-pattern-instance.png)

<br>
<br>
<br>

`filter` bottles the common pattern of selecting elements from a list that satisfy a condition:

![Fairy In a Bottle](/static/img/fairy.png)


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Let's talk about types

```haskell
-- evens [1,2,3,4] ==> [2,4]
evens :: [Int] -> [Int]
evens xs = filter isEven xs
  where
    isEven :: Int -> Bool
    isEven x  =  x `mod` 2 == 0
```

```haskell
filter :: ???
```

<br>
<br>
<br>
<br>
<br>

```haskell
-- fourChars ["i","must","do","work"] ==> ["must","work"]
fourChars :: [String] -> [String]
fourChars xs = filter isFour xs
  where
    isFour :: String -> Bool
    isFour x  =  length x == 4
```

```haskell
filter :: ???
```

<br>
<br>
<br>
<br>
<br>
<br>

So what's the type of `filter`?

```haskell
filter :: (Int -> Bool) -> [Int] -> [Int] -- ???

filter :: (String -> Bool) -> [String] -> [String] -- ???
```

<br>

* It *does not care* what the list elements are

    * as long as the predicate can handle them
  
* It's type is **polymorphic** (generic) in the type of list elements

<br>

```haskell
-- For any type `a`
--   if you give me a predicate on `a`s
--   and a list of `a`s,
--   I'll give you back a list of `a`s 
filter :: (a -> Bool) -> [a] -> [a]
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


## Example: all caps

Lets write a function `shout`:

```haskell
-- shout []                    ==> []
-- shout ['h','e','l','l','o'] ==> ['H','E','L','L','O'] 
```

```haskell
shout :: [Char] -> [Char]
shout []     = ...
shout (x:xs) = ... 
```

<br>
<br>
<br>
<br>
<br>
<br>

## Example: squares

Lets write a function `squares`:

```haskell
-- squares []        ==> []
-- squares [1,2,3,4] ==> [1,4,9,16] 
```

```haskell
squares :: [Int] -> [Int]
squares []     = ...
squares (x:xs) = ... 
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

## Yikes, Most Code is the Same

Lets rename the functions to `foo`:

```haskell
-- shout
foo []     = []
foo (x:xs) = toUpper x : foo xs

-- squares
foo []     = []
foo (x:xs) = (x * x)   : foo xs
```

<br>
<br>

Lets **refactor** into the **common pattern**

```haskell
pattern = ...
```

<br>
<br>
<br>
<br>
<br>
<br>

## The "map" pattern

![The `map` Pattern](/static/img/map-pattern.png)

General Pattern

- HOF `map` 
- Apply a transformation `f` to each element of a list

Specific Operations

- Transformations `toUpper` and `\x -> x * x`

<br>
<br>
<br>

`map` bottles the common pattern of iteratively applying a transformation to each element of a list:

![Fairy In a Bottle](/static/img/fairy.png)


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


## EXERCISE: refactor with map

With `map` defined as:

```haskell
map f []     = []
map f (x:xs) = f x : map f xs
```

refactor `shout` and `squares` to use `map`:

```haskell
shout   xs = map ...

squares xs = map ...
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


## The Case of the Missing Parameter 

The following definitions of `shout` are equivalent:

```haskell
shout :: [Char] -> [Char]
shout xs = map (\x -> toUpper x) xs
```

and

```haskell
shout :: [Char] -> [Char]
shout = map toUpper
```

Where did `xs` and `x` go???

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

## The Case of the Missing Parameter 

Recall lambda calculus:

The expressions `f` and `\x -> f x` are in some sense "equivalent"

  - as long as `x not in FV(f)`

because they behave the same way when applied to any argument `e`:

```haskell
(\x -> f x) e
=b> f e
```

Transforming `\x -> f x` into `f` is called **eta contraction**

  - and the reverse is called **eta expansion**

<br>
<br>

In Haskell we have:

```haskell
shout xs = map (\x -> toUpper x) xs
```

is syntactic sugar for:

```haskell
shout 
  =d> 
\xs -> map (\x -> toUpper x) xs
  =e> -- eta-contract outer lambda
map (\x -> toUpper x) xs
  =e> -- eta-contract inner lambda
map toUpper xs  
```

<br>
<br>
<br>
<br>

More generally, whenever you want to define a function:

```haskell
f x y z = e x y z
```

you can save some typing, and *omit* the parameters:

  - as long as `x`, `y`, and `z` are not free in `e`

```haskell
f = e
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

## QUIZ

What is the type of `map`?

```haskell
map f []     = []
map f (x:xs) = f x : map f xs
```

**(A)** `(Char -> Char) -> [Char] -> [Char]`

**(B)** `(Int -> Int) -> [Int] -> [Int]`

**(C)** `(a -> a) -> [a] -> [a]`

**(D)** `(a -> b) -> [a] -> [b]`

**(E)** `(a -> b) -> [c] -> [d]`

    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## The type of "map"

```haskell
-- For any types `a` and `b`
--   if you give me a transformation from `a` to `b`
--   and a list of `a`s,
--   I'll give you back a list of `b`s 
map :: (a -> b) -> [a] -> [b]
```

<br>

**Type says it all!**

Can you think of a function that:

* has this type
* builds the output list *not* by applying the transformation to the input list? 


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Recall: length of a list

```haskell
-- len []      ==> 0
-- len ["carne","asada"] ==> 2
len :: [a] -> Int
len []     = 0
len (x:xs) = 1 + len xs
```

<br>
<br>
<br>
<br>
<br>


## Recall: summing a list

```haskell
-- sum []      ==> 0
-- sum [1,2,3] ==> 6
sum :: [Int] -> Int
sum []     = 0
sum (x:xs) = x + sum xs
```

<br>
<br>
<br>
<br>
<br>

## Example: string concatenation

Let's write a function `cat`:

```haskell
-- cat [] ==> ""
-- cat ["carne","asada","torta"] ==> "carneasadatorta"
cat :: [String] -> String
cat []     = ...
cat (x:xs) = ...
```

<br>
<br>
<br>
<br>
<br>
<br>


## Can you spot the pattern?

```haskell
-- len
foo []     = 0
foo (x:xs) = 1 + foo xs

-- sum
foo []     = 0
foo (x:xs) = x + foo xs

-- cat
foo []     = ""
foo (x:xs) = x ++ foo xs
```

<br>

```haskell
pattern = ...
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

## The "fold-right" pattern

![The `foldr` Pattern](/static/img/foldr-pattern.png)

General Pattern

- Recurse on tail
- Combine result with the head using some binary operation

<br>
<br>
<br>
<br>

```haskell
foldr f b []     = b
foldr f b (x:xs) = f x (foldr f b xs)
```

<br>
<br>

Let's refactor `sum`, `len` and `cat`: 

```haskell
sum = foldr ...  ...

cat = foldr ...  ...

len = foldr ...  ...
```

Factor the recursion out!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


![`foldr` instances](/static/img/foldr-pattern-instance.png)

You can write it more clearly as

```haskell
sum = foldr (+) 0

cat = foldr (++) ""
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

## QUIZ

What does this evaluate to?

```haskell
foldr f b []     = b
foldr f b (x:xs) = f x (foldr f b xs)

quiz = foldr (:) [] [1,2,3]
```


**(A)** Type error

**(B)** `[1,2,3]`

**(C)** `[3,2,1]`

**(D)** `[[3],[2],[1]]`

**(E)** `[[1],[2],[3]]`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

```haskell
foldr f b []     = b
foldr f b (x:xs) = f x (foldr f b xs)

foldr (:) [] [1,2,3]
  ==> (:) 1 (foldr (:) [] [2, 3])
  ==> (:) 1 ((:) 2 (foldr (:) [] [3]))
  ==> (:) 1 ((:) 2 ((:) 3 (foldr (:) [] [])))
  ==> (:) 1 ((:) 2 ((:) 3 []))
  ==  1 : (2 : (3 : []))
  ==  [1,2,3]
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


## The "fold-right" pattern

```haskell
foldr f b [x1, x2, x3, x4]
  ==> f x1 (foldr f b [x2, x3, x4])
  ==> f x1 (f x2 (foldr f b [x3, x4]))
  ==> f x1 (f x2 (f x3 (foldr f b [x4])))
  ==> f x1 (f x2 (f x3 (f x4 (foldr f b []))))
  ==> f x1 (f x2 (f x3 (f x4 b)))
```

Accumulate the values from the **right**

For example:

```haskell
foldr (+) 0 [1, 2, 3, 4]
  ==> 1 + (foldr (+) 0 [2, 3, 4])
  ==> 1 + (2 + (foldr (+) 0 [3, 4]))
  ==> 1 + (2 + (3 + (foldr (+) 0 [4])))
  ==> 1 + (2 + (3 + (4 + (foldr (+) 0 []))))
  ==> 1 + (2 + (3 + (4 + 0)))
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

## QUIZ

What is the most general type of `foldr`?

```haskell
foldr f b []     = b
foldr f b (x:xs) = f x (foldr f b xs)
```


**(A)** `(a -> a -> a) -> a -> [a] -> a`

**(B)** `(a -> a -> b) -> a -> [a] -> b`

**(C)** `(a -> b -> a) -> b -> [a] -> b`

**(D)** `(a -> b -> b) -> b -> [a] -> b`

**(E)** `(b -> a -> b) -> b -> [a] -> b`

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


Is `foldr` **tail recursive**?

(I) final
    
    *Answer:* No! It calls the binary operations on the results of the recursive call

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## What about tail-recursive versions?

Let's write tail-recursive `sum`!

```haskell
sumTR :: [Int] -> Int
sumTR = ...
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

Lets run `sumTR` to see how it works

```haskell
sumTR [1,2,3]
  ==> helper 0 [1,2,3]
  ==> helper 1   [2,3]    -- 0 + 1 ==> 1
  ==> helper 3     [3]    -- 1 + 2 ==> 3
  ==> helper 6      []    -- 3 + 3 ==> 6 
  ==> 6
```

**Note:** `helper` directly returns the result of recursive call! 

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

Let's write tail-recursive `cat`!

```haskell
catTR :: [String] -> String 
catTR = ...
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

Lets run `catTR` to see how it works

```haskell
catTR                 ["carne", "asada", "torta"]

  ==> helper ""       ["carne", "asada", "torta"]

  ==> helper "carne"           ["asada", "torta"]

  ==> helper "carneasada"               ["torta"]

  ==> helper "carneasadatorta"                 []

  ==> "carneasadatorta"
```

**Note:** `helper` directly returns the result of recursive call! 

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Can you spot the pattern?

```haskell
-- sumTR
foo xs                = helper 0 xs
  where
    helper acc []     = acc
    helper acc (x:xs) = helper (acc + x) xs


-- catTR
foo xs                = helper "" xs
  where
    helper acc []     = acc
    helper acc (x:xs) = helper (acc ++ x) xs
```

<br>

```haskell
pattern = ...
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

## The "fold-left" pattern

![The `foldl` Pattern](/static/img/foldl-pattern.png)

General Pattern

- Use a helper function with an extra accumulator argument
- To compute new accumulator, combine current accumulator with the head using some binary operation

<br>
<br>
<br>
<br>

Also, since `foldl` already has the `b` argument, which can serve as accumulator,
the `helper` is redundant!
Can be rewritten as:

```haskell
foldl f b []     = b
foldl f b (x:xs) = foldl f (f b x) xs
```

<br>
<br>

Let's refactor `sumTR` and `catTR`: 

```haskell
sumTR = foldl ...  ...

catTR = foldl ...  ...
```

Factor the tail-recursion out!


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

What does this evaluate to?

```haskell
foldl f b []     = b
foldl f b (x:xs) = foldl f (f b x) xs

quiz = foldl (:) [] [1,2,3]
```

<br>

**(A)** Type error

**(B)** `[1,2,3]`

**(C)** `[3,2,1]`

**(D)** `[[3],[2],[1]]`

**(E)** `[[1],[2],[3]]`

<br>

(I) final
    
    *Answer:* A

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

What does this evaluate to?

```haskell
foldl f b []     = b
foldl f b (x:xs) = foldl f (f b x) xs

quiz = foldl (\xs x -> x : xs) [] [1,2,3]
```

<br>

**(A)** Type error

**(B)** `[1,2,3]`

**(C)** `[3,2,1]`

**(D)** `[[3],[2],[1]]`

**(E)** `[[1],[2],[3]]`

<br>

(I) final
    
    *Answer:* C

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

```haskell
foldl f b []     = b
foldl f b (x:xs) = foldl f (f b x) xs

f = \xs x -> x : xs

foldl f []                         [1,2,3]
  ==> foldl f (1 : [])               [2,3]
  ==> foldl f (2 : (1 : []))           [3]
  ==> foldl f (3 : (2 : (1 : [])))      []
  ==> 3 : (2 : (1 : []))
  = [3,2,1]
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## The "fold-left" pattern

```haskell
foldl f b                     [x1, x2, x3, x4]
  ==> foldl f (f b x1)             [x2, x3, x4]
  ==> foldl f (f (f b x1) x2)          [x3, x4]
  ==> foldl f (f (f (f b x1) x2) x3)       [x4]
  ==> foldl f (f (f (f (f b x1) x2) x3) x4)  []
  ==> (f (f (f (f b x1) x2) x3) x4)
```

Accumulate the values from the **left**

For example:

```haskell
foldl (+) 0                   [1, 2, 3, 4]
  ==> foldl (+) (0 + 1)             [2, 3, 4]
  ==> foldl (+) ((0 + 1) + 2)          [3, 4]
  ==> foldl (+) (((0 + 1) + 2) + 3)       [4]
  ==> foldl (+) ((((0 + 1) + 2) + 3) + 4)  []
  ==> ((((0 + 1) + 2) + 3) + 4)
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

## Left vs. Right

```haskell
foldl f b [x1, x2, x3]  ==> f (f (f b x1) x2) x3  -- Left

foldr f b [x1, x2, x3]  ==> f x1 (f x2 (f x3 b))  -- Right
```

For example:

```haskell
foldl (+) 0 [1, 2, 3]  ==> ((0 + 1) + 2) + 3  -- Left

foldr (+) 0 [1, 2, 3]  ==> 1 + (2 + (3 + 0))  -- Right
```

Different types!

```haskell
foldl :: (b -> a -> b) -> b -> [a] -> b  -- Left

foldr :: (a -> b -> b) -> b -> [a] -> b  -- Right
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

### Useful HOF: flip

```haskell
-- you can write
foldl (\xs x -> x : xs) [] [1,2,3]

-- more concisely like so:
foldl (flip (:))        [] [1,2,3]
```

What is the type of `flip`?

<br>

(I) lecture
    
    ```haskell
    flip :: ???
    ```
    
(I) final
    
    ```haskell
    flip :: (a -> b -> c) -> b -> a -> c
    ```
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

### Useful HOF: compose

```haskell
-- you can write
map (\x -> f (g x)) ys

-- more concisely like so:
map (f . g) ys
```

What is the type of `(.)`?

<br>

(I) lecture
    
    ```haskell
    (.) :: ???
    ```
    
(I) final
    
    ```haskell
    (.) :: (b -> c) -> (a -> b) -> a -> c
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


## Higher Order Functions

Iteration patterns over collections:

- **Filter** values in a collection given a *predicate*
- **Map** (iterate) a given *transformation* over a collection
- **Fold** (reduce) a collection into a value, given a *binary operation* to combine results

Useful helper HOFs:

- **Flip** the order of function's (first two) arguments 
- **Compose** two functions

<br>

HOFs can be put into libraries to enable modularity

- Data structure **library** implements `map`, `filter`, `fold` for its collections

    - generic efficient implementation
    
    - generic optimizations: `map f (map g xs) --> map (f.g) xs`
    

- Data structure **clients** use HOFs with specific operations
    
    - no need to know the implementation of the collection 
    
Enabled the "big data" revolution e.g. _MapReduce_, _Spark_

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
