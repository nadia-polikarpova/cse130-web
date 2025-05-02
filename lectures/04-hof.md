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

The expressions `F` and `\x -> F x` are in some sense "equivalent"

  - as long as `x not in FV(F)`

because they behave the same way when applied to any argument `E`:

```haskell
(\x -> F x) E
=b> F E
```

Transforming `\x -> F x` into `F` is called **eta contraction**

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
map (\x -> toUpper x)
  =e> -- eta-contract inner lambda
map toUpper
```

<br>
<br>
<br>
<br>

More generally, whenever you want to define a function:

```haskell
f x y z = E x y z
```

you can save some typing, and *omit* the parameters:

  - as long as `x`, `y`, and `z` are not free in `E`

```haskell
f = E
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

## Example: summing a list

```haskell
-- sum []      ==> 0
-- sum [1,2,3] ==> 6
sum :: [Int] -> Int
sum []     = ...
sum (x:xs) = ...
```

<br>
<br>
<br>
<br>
<br>

## Example: string concatenation

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

```haskell
foldr op base []     = base
foldr op base (x:xs) = op x (foldr op base xs)
```

General Pattern

- Recurse on tail
- Combine result with the head using some binary operation

`foldr` bottles the common pattern of combining/reducing a list into a single value:

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


## EXERCISE: refactor with fold

With `foldr` defined as:

```haskell
foldr op base []     = base
foldr op base (x:xs) = op x (foldr op base xs)
```

refactor `sum` and `cat` to use `foldr`:

```haskell
sum xs = foldr op base xs
  where
    base     = ...
    op x acc = ...

cat xs = foldr op base xs
  where
    base     = ...
    op x acc = ...
```

Now use eta-contraction to make your code more concise!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Solution

```haskell
sum xs = foldr op base xs
  where
    base     = 0
    op x acc = x + acc

cat xs = foldr op base xs
  where
    base     = ""
    op x acc = x ++ acc
```

or, more concisely:

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
<br>
<br>


## Executing "foldr"

To develop some intuition about `foldr` lets "run" it by hand.

```haskell
foldr op base []     = base
foldr op base (x:xs) = op x (foldr op base xs)

foldr op b (a1:a2:a3:[])
==>
  a1 `op` (foldr op b (a2:a3:[]))
==>
  a1 `op` (a2 `op` (foldr op b (a3:[])))
==>
  a1 `op` (a2 `op` (a3 `op` (foldr op b [])))
==>
  a1 `op` (a2 `op` (a3 `op` b))
```

Look how it *mirrors* the structure of lists!

- `(:)` is replaced by `op`
- `[]` is replaced by `base`

For example:

```haskell
foldr (+) 0 [1, 2, 3, 4]
  ==> 1 + (foldr (+) 0 [2, 3, 4])
  ==> 1 + (2 + (foldr (+) 0 [3, 4]))
  ==> 1 + (2 + (3 + (foldr (+) 0 [4])))
  ==> 1 + (2 + (3 + (4 + (foldr (+) 0 []))))
  ==> 1 + (2 + (3 + (4 + 0)))
```

Accumulate the values from the **right**!

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
foldr op base []     = base
foldr op base (x:xs) = op x (foldr op base xs)
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
<br>
<br>

## QUIZ

Recall the function to compute the `len` of a list

```haskell
len :: [a] -> Int
len []     = 0
len (x:xs) = 1 + len xs
```

Which of these is a valid implementation of `len`

**A.** `len = foldr (\n -> n + 1) 0`

**B.** `len = foldr (\n m -> n + m) 0`

**C.** `len = foldr (\_ n -> n + 1) 0`

**D.** `len = foldr (\x xs -> 1 + len xs) 0`

**E.** None of the above

**HINT**: remember that `foldr :: (a -> b -> b) -> b -> [a] -> b`!

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
process = ...
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
foldl op base []     = base
foldl op base (x:xs) = foldl op (base `op` x) xs
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


## The "fold-left" pattern

```haskell
foldl op b                                  [x1, x2, x3, x4]
  ==> foldl op (b `op` x1)                      [x2, x3, x4]
  ==> foldl op ((b `op` x1) `op` x2)                [x3, x4]
  ==> foldl op (((b `op` x1) `op` x2) `op` x3)          [x4]
  ==> foldl op ((((b `op` x1) `op` x2) `op` x3) `op` x4)  []
  ==> (((b `op` x1) `op` x2) `op` x3) `op` x4
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
foldl op b [x1, x2, x3]  ==> ((b `op` x1) `op` x2) `op` x3  -- Left

foldr op b [x1, x2, x3]  ==> x1 `op` (x2 `op` (x3 `op` b))  -- Right
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
