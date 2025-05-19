---
title: Monads
date: 2019-06-5
headerImg: books.jpg
---

## The Mantra

**Don't Repeat Yourself**

In this lecture we will see advanced ways to *abstract code patterns*

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

## Outline

1. **Functors**
2. A Monad for Error Handling
3. A Monad for Mutable State
4. a monad for Non-Determinism
5. Writing apps with monads

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


## Recall: HOF

Recall how we used higher-order functions to abstract code patters


### Iterating Through Lists

```haskell
data List a
  = []
  | (:) a (List a)
```

<br>
<br>
<br>

**Rendering the Values of a List**

```haskell
-- >>> showList [1, 2, 3]
-- ["1", "2", "3"]

showList        :: List Int -> List String
showList []     =  []
showList (n:ns) =  show n : showList ns
```

<br>
<br>
<br>

**Squaring the values of a list**

```haskell
-- >>> sqrList [1, 2, 3]
-- 1, 4, 9

sqrList        :: List Int -> List Int
sqrList []     =  []
sqrList (n:ns) =  n^2 : sqrList ns
```

<br>
<br>
<br>

**Common Pattern:** `map` over a list

Refactor iteration into `mapList`

```haskell
mapList :: (a -> b) -> List a -> List b
mapList f []     = []
mapList f (x:xs) = f x : mapList f xs
```

<br>
<br>

Reuse `mapList` to implement `showList` and `sqrList`

```haskell
showList xs = mapList (\n -> show n) xs

sqrList  xs = mapList (\n -> n ^ 2)  xs
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>

### Iterating Through Trees

```haskell
data Tree a
  = Leaf
  | Node a (Tree a) (Tree a)
```

<br>
<br>
<br>

**Rendering the Values of a Tree**

```haskell
-- >>> showTree (Node 2 (Node 1 Leaf Leaf) (Node 3 Leaf Leaf))
-- (Node "2" (Node "1" Leaf Leaf) (Node "3" Leaf Leaf))

showTree :: Tree Int -> Tree String
showTree Leaf         = ???
showTree (Node v l r) = ???
```

<br>

(I) final

    ```
    showTree :: Tree Int -> Tree String
    showTree Leaf         = Leaf
    showTree (Node v l r) = Node (show v) (showTree l) (showTree r)
    ```


<br>
<br>
<br>

**Squaring the values of a Tree**

```haskell
-- >>> sqrTree (Node 2 (Node 1 Leaf Leaf) (Node 3 Leaf Leaf))
-- (Node 4 (Node 1 Leaf Leaf) (Node 9 Leaf Leaf))

sqrTree :: Tree Int -> Tree Int
sqrTree Leaf         = ???
sqrTree (Node v l r) = ???
```

<br>

(I) final

    ```
    sqrTree :: Tree Int -> Tree Int
    sqrTree Leaf         = Leaf
    sqrTree (Node v l r) = Node (v^2) (sqrTree l) (sqrTree r)
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


### QUIZ: Mapping over a Tree

If we refactor iteration into `mapTree`,
what should its type be?

```haskell
mapTree :: ???

showTree t = mapTree show t
sqrTree  t = mapTree (^ 2)  t
```

**(A)** `(Int -> Int)    -> Tree Int -> Tree Int`

**(B)** `(Int -> String) -> Tree Int -> Tree String`

**(C)** `(Int -> a)      -> Tree Int -> Tree a`

**(D)** `(a -> a)        -> Tree a   -> Tree a`

**(E)** `(a -> b)        -> Tree a   -> Tree b`

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

### Mapping over Trees

Let's write `mapTree`:

```haskell
mapTree :: (a -> b) -> Tree a -> Tree b
mapTree f Leaf         = ???
mapTree f (Node v l r) = ???
```

<br>

(I) final

    ```
    mapTree :: (a -> b) -> Tree a -> Tree b
    mapTree f Leaf         = Leaf
    mapTree f (Node v l r) = Node (f v) (mapTree f l) (mapTree f r)
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

### Abstracting across Datatypes

Wait, this looks familiar...

```haskell
mapList :: (a -> b) -> List a -> List b    -- List
mapTree :: (a -> b) -> Tree a -> Tree b    -- Tree
```

<br>

Can we provide a generic `map` function that works for `List`, `Tree`, and other datatypes?

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

### A Class for Mapping

Not all datatypes support mapping over, only *some* of them do.

So let's make a typeclass for it!

```haskell
class Functor t where
  fmap :: ???
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


### QUIZ

What type should we give to `fmap`?

```haskell
class Functor t where
  fmap :: ???
```

**(A)** `(a -> b) -> t      -> t`

**(B)** `(a -> b) -> [a]    -> [b]`

**(C)** `(a -> b) -> t a    -> t b`

**(D)** `(a -> b) -> Tree a -> Tree b`

**(E)** None of the above

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

```haskell
class Functor t where
  fmap :: (a -> b) -> t a -> t b
```

Unlike other typeclasses we have seen before, here `t`:

  - does not stand for a *type*
  - but for a *type constructor*
  - it is called a **higher-kinded type variable**


<br>
<br>
<br>
<br>
<br>
<br>

### Reuse Iteration Across Types

```haskell
instance Functor [] where
  fmap = mapList

instance Functor Tree where
  fmap = mapTree
```

And now we can do

```haskell
-- >>> fmap show [1,2,3]
-- ["1", "2", "3"]


-- >>> fmap (^2) (Node 2 (Node 1 Leaf Leaf) (Node 3 Leaf Leaf))
-- (Node 4 (Node 1 Leaf Leaf) (Node 9 Leaf Leaf))

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


### EXERCISE: the Maybe Functor

Write a `Functor` instance for `Maybe`:

```haskell
data Maybe a = Nothing | Just a

instance Functor Maybe where
  fmap = ???
```

When you're done you should see

```haskell
-- >>> fmap (^ 2) (Just 3)
Just 9

-- >>> fmap (^ 2) Nothing
Nothing
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

## Outline

1. Functors [done]
2. **A Monad for Error Handling**
3. A Monad for Mutable State
4. a monad for Non-Determinism
5. Writing apps with monads

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

## Next: A Class for Sequencing

Consider a simplified `Expr` datatype

```haskell
data Expr
  = Num    Int
  | Plus   Expr Expr
  | Div    Expr Expr
  deriving (Show)

eval :: Expr -> Int
eval (Num n)      = n
eval (Plus e1 e2) = eval e1   +   eval e2
eval (Div  e1 e2) = eval e1 `div` eval e2

-- >>> eval (Div (Num 6) (Num 2))
-- 3
```

<br>
<br>
<br>
<br>

But what is the result of

```haskell
-- >>> eval (Div (Num 6) (Num 0))
-- *** Exception: divide by zero
```

<br>
<br>

My interpreter crashes!

  - What if I'm implementing GHCi?
  - I don't want GHCi to *crash* every time you enter `div 5 0`
  - I want it to process the error and move on with its life

How can we achieve this behavior?

<br>
<br>
<br>
<br>
<br>
<br>
<br>

### Error Handling: Take One

Let's introduce a new type for evaluation results:

```haskell
data Result  a
  = Error String
  | Value a
```

Our `eval` will now return `Result Int` instead of `Int`

  - If a _sub-expression_ had a divide by zero, return `Error "..."`
  - If all sub-expressions were safe, then return the actual `Value v`

<br>
<br>
<br>
<br>
<br>

```haskell
eval :: Expr -> Result Int
eval (Num n)      = ???
eval (Plus e1 e2) = ???
eval (Div e1 e2)  = ???
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

```haskell
eval :: Expr -> Result Int
eval (Num n)      = Value n
eval (Plus e1 e2) =
  case eval e1 of
    Error err1 -> Error err1
    Value v1   -> case eval e2 of
                    Error err2 -> Error err2
                    Value v1   -> Value (v1 + v2)
eval (Div e1 e2)  =
  case eval e1 of
    Error err1 -> Error err1
    Value v1   -> case eval e2 of
                    Error err2 -> Error err2
                    Value v2   -> if v2 == 0
                                    then Error ("DBZ: " ++ show e2)
                                    else Value (v1 `div` v2)
```

<br>
<br>

The **good news**: interpreter doesn't crash, just returns `Error msg`:

```haskell
λ> eval (Div (Num 6) (Num 2))
Value 3
λ> eval (Div (Num 6) (Num 0))
Error "DBZ: Num 0"
λ> eval (Div (Num 6) (Plus (Num 2) (Num (-2))))
Error "DBZ: Plus (Num 2) (Num (-2))"
```

<br>
<br>

The **bad news**: the code is super duper **gross**

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

## Lets spot a Pattern

The code is gross because we have these cascading blocks

```haskell
case eval e1 of
  Error err1 -> Error err1
  Value v1   -> case eval e2 of
                  Error err2 -> Error err2
                  Value v2   -> Value (v1 + v2)
```

<br>
<br>

But these blocks have a **common pattern**:

- First do `eval e` and get result `res`
- If `res` is an `Error`, just return that error
- If `res` is a `Value v` then _do further processing_ on `v`

<br>
<br>

```haskell
case res of
  Error err -> Error err
  Value v   -> process v -- do more stuff with v
```

<br>
<br>

![Bottling a Magic Pattern](/static/img/fairy.png){#fig:types .align-center}

Lets **bottle** that common structure in a function `>>=` (pronounced _bind_):

```haskell
(>>=) :: Result a -> (a -> Result b) -> Result b
(Error err) >>= _       = Error err
(Value v)   >>= process = process v
```

<br>
<br>

Notice the `>>=` takes *two* inputs:

- `Result a`: result of the first evaluation
- `a -> Result b`: in case the first evaluation produced a *value*, what to do *next* with that value

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


### QUIZ: Bind 1

With `>>=` defined as before:

```haskell
(>>=) :: Result a -> (a -> Result b) -> Result b
(Error msg) >>= _       = Error msg
(Value v)   >>= process = process v

```

What does the following evaluate to?

```haskell
λ> eval (Num 5)   >>=   \v -> Value (v + 1)
```

**(A)** Type Error

**(B)** `5`

**(C)** `Value 5`

**(D)** `Value 6`

**(E)** `Error msg`

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


### QUIZ: Bind 2

With `>>=` defined as before:

```haskell
(>>=) :: Result a -> (a -> Result b) -> Result b
(Error msg) >>= _       = Error msg
(Value v)   >>= process = process v

```

What does the following evaluate to?

```haskell
λ> Error "nope"   >>=   \v -> Value (v + 1)
```

**(A)** Type Error

**(B)** `5`

**(C)** `Value 5`

**(D)** `Value 6`

**(E)** `Error "nope"`

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

## A Cleaned up Evaluator

The magic bottle lets us clean up our `eval`:

```haskell
eval :: Expr -> Result Int
eval (Num n)      = Value n
eval (Plus e1 e2) = eval e1 >>= \v1 ->
                    eval e2 >>= \v2 ->
                    Value (v1 + v2)
eval (Div e1 e2)  = eval e1 >>= \v1 ->
                    eval e2 >>= \v2 ->
                    if v2 == 0
                      then Error ("DBZ: " ++ show e2)
                      else Value (v1 `div` v2)
```

The gross _pattern matching_ is all hidden inside `>>=`!

<br>
<br>

**NOTE:** It is _crucial_ that you understand what the code above
is doing, and why it is actually just a "shorter" version of the
(gross) nested-case-of `eval`.


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## A Class for bind

Like `fmap` or `show` or `==`,
the `>>=` operator turns out to be useful across many types
(not just `Result`)

<br>
<br>

Let's create a typeclass for it!

```haskell
class Monad m where
  (>>=)  :: m a -> (a -> m b) -> m b -- bind
  return :: a -> m a                 -- return
```

<br>
<br>

`return` tells you how to wrap an `a` value in the monad

  - Useful for writing code that works across multiple monads

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

### Monad instance for Result

Let's make `Result` an instance of `Monad`!

```haskell
class Monad m where
  (>>=)  :: m a -> (a -> m b) -> m b
  return :: a -> m a

instance Monad Result where
  (>>=) :: Result a -> (a -> Result b) -> Result b
  (Error msg) >>= _       = Error msg
  (Value v)   >>= process = process v

  return :: a -> Result a
  return v = ??? -- How do we make a `Result a` from an `a`?
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

```haskell
instance Monad Result where
  (>>=) :: Result a -> (a -> Result b) -> Result b
  (Error msg) >>= _       = Error msg
  (Value v)   >>= process = process v

  return :: a -> Result a
  return v = Value v
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Syntactic Sugar

In fact `>>=` is *so* useful there is special syntax for it!

  - It's called *the `do` notation*

<br>
<br>

Instead of writing

```haskell
e1 >>= \v1 ->
e2 >>= \v2 ->
e3 >>= \v3 ->
e
```

you can write

```haskell
do v1 <- e1
   v2 <- e2
   v3 <- e3
   e
```

<br>
<br>
<br>
<br>

Thus, we can further simplify our `eval` to:

```haskell
eval :: Expr -> Result Int
eval (Num n)      = return n
eval (Plus e1 e2) = do v1 <- eval e1
                       v2 <- eval e2
                       return (v1 + v2)
eval (Div e1 e2)  = do v1 <- eval e1
                       v2 <- eval e2
                       if v2 == 0
                         then Error ("DBZ: " ++ show e2)
                         else return (v1 `div` v2)
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## The Either Monad

Error handling is a very common task!

Instead of defining your own type `Result`,
you can use `Either` from the Haskell standard library:

```haskell
data Either a b =
    Left  a  -- something has gone wrong
  | Right b  -- everything has gone RIGHT
```

`Either` is already an instance of `Monad`,
so no need to define your own `>>=`!

<br>
<br>

Now we can simply define

```haskell
type Result a = Either String a
```

and the `eval` above will just work out of the box!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Outline

1. Functors [done]
2. A Monad for Error Handling [done]
3. **A Monad for Mutable State**
4. a monad for Non-Determinism
5. Writing apps with monads

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


## Expressions with a Counter

Consider implementing expressions with a counter:

```haskell
data Expr
  = Num    Int
  | Plus   Expr Expr
  | Next   -- counter value
  deriving (Show)
```

Behavior we want:

- `eval` is given the initial counter value
- every time we evaluate `Next` (within the call to `eval`), the value of the counter increases:

```
--        0
λ> eval 0 Next
0

--              0    1
λ> eval 0 (Plus Next Next)
1

--              ?          ?    ?
λ> eval 0 (Plus Next (Plus Next Next))
???
```

<br>
<br>

How should we implement `eval`?

```
eval (Num n)      cnt = ???
eval Next         cnt = ???
eval (Plus e1 e2) cnt = ???
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


### QUIZ: State: Take 1

If we implement `eval` like this:

```haskell
eval (Num n)      cnt = n
eval Next         cnt = cnt
eval (Plus e1 e2) cnt = eval e1 cnt + eval e2 cnt
```
What would be the result of the following?

```haskell
λ> eval (Plus Next Next) 0
```

**(A)** Type error

**(B)** `0`

**(C)** `1`

**(D)** `2`

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

It's going to be `0` because we never increment the counter!

   - We need to increment it every time we do `eval Next`
   - So `eval` needs to return the new counter

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

## Evaluating Expressions with Counter

```haskell
type Cnt = Int

eval :: Expr -> Cnt -> (Cnt, Int)
eval (Num n)      cnt = (cnt, n)
eval Next         cnt = (cnt + 1, cnt)
eval (Plus e1 e2) cnt = let (cnt1, v1) = eval e1 cnt
                        in
                          let (cnt2, v2) = eval e2 cnt1
                          in
                            (cnt2, v1 + v2)

topEval :: Expr -> Int
topEval e = snd (eval e 0)
```

<br>
<br>

The **good news**: we get the right result:

```haskell
λ> topEval (Plus Next Next)
1

λ> topEval (Plus Next (Plus Next Next))
3
```

<br>
<br>

The **bad news**: the code is super duper **gross**.

The `Plus` case has to "thread" the counter through the recursive calls:

```haskell
let (cnt1, v1) = eval e1 cnt
  in
    let (cnt2, v2) = eval e2 cnt1
    in
      (cnt2, v1 + v2)
```

  1. Easy to make a mistake, e.g. pass `cnt` instead of `cnt1` into the second `eval`!
  2. The logic of addition is obscured by all the counter-passing

So unfair, since `Plus` doesn't even care about the counter!

<br>
<br>
<br>
<br>

Is it *too much to ask* that `eval` looks like this?

```haskell
eval (Num n)      = return n
eval (Plus e1 e2) = do v1 <- eval e1
                       v2 <- eval e2
                       return (v1 + v2)
...
```

  - Cases that don't care about the counter (`Num`, `Plus`), don't even have to mention it!
  - The counter is somehow threaded through automatically behind the scenes
  - Looks *just like* in the error handing evaluator

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

## Lets spot a Pattern

```haskell
let (cnt1, v1) = eval e1 cnt
  in
    let (cnt2, v2) = eval e2 cnt1
    in
      (cnt2, v1 + v2)
```

These blocks have a **common pattern**:

- Perform first step (`eval e`) using initial counter `cnt`
- Get a result `(cnt', v)`
- Then do further processing on `v` using the new counter `cnt'`

```haskell
let (cnt', v) = step cnt
in process v cnt' -- do things with v and cnt'
```

<br>
<br>

Can we **bottle** this common pattern as a `>>=`?

```haskell
(>>=) step process cnt = let (cnt', v) = step cnt
                         in process v cnt'
```
<br>
<br>

But what is the type of this `>>=`?

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
(>>=) :: (Cnt -> (Cnt, a))
         -> (a -> Cnt -> (Cnt, b))
         -> Cnt
         -> (Cnt, b)
(>>=) step process cnt = let (cnt', v) = step cnt
                         in process v cnt'
```

Wait, but this type signature looks nothing like the `Monad`'s bind!

```haskell
(>>=)  :: m a -> (a -> m b) -> m b
```

<br>
<br>

... or does it???

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


### QUIZ: Type of bind for Counting

What should I replace `m t` with to make the general type of monadic bind:

```haskell
(>>=)  :: m a -> (a -> m b) -> m b
```

look like the type of bind we just defined:

```haskell
(>>=) :: (Cnt -> (Cnt, a))
         -> (a -> Cnt -> (Cnt, b))
         -> Cnt
         -> (Cnt, b)
```

**(A)** It's impossible

**(B)** `m t  =  Result t`

**(C)** `m t  =  (Cnt, t)`

**(D)** `m t  =  Cnt -> (Cnt , t)`

**(E)** `m t  =  t -> (Cnt , t)`

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


```haskell
type Counting a = Cnt -> (Cnt, a)

(>>=) :: Counting a
         -> (a -> Counting b)
         -> Counting b
(>>=) step process = \cnt -> let (cnt', v) = step cnt
                             in process v cnt'
```

Mind blown.

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


### QUIZ: Return for Counting

How should we define `return` for `Counting`?

```haskell
type Counting a = Cnt -> (Cnt, a)

-- | Represent value x as a counting computation,
-- don't actually touch the counter
return :: a -> Counting a
return x = ???
```

**(A)** `x`

**(B)** `(0, x)`

**(C)** `\c -> (0, x)`

**(D)** `\c -> (c, x)`

**(E)** `\c -> (c + 1, x)`

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

### Cleaned-up evaluator

```haskell
eval :: Expr -> Counting Int
eval (Num n)      = return n
eval (Plus e1 e2) = eval e1 >>= \v1 ->
                    eval e2 >>= \v2 ->
                    return (v1 + v2)
eval Next         = \cnt -> (cnt + 1, cnt)
```

Hooray! We rid the poor `Num` and `Plus` from the pesky counters!

<br>
<br>

The `Next` case has to deal with counters

  - but can we somehow hide the representation of `Counting a`?
  - and make it look more like we just have mutable state that we can `get` and `put`?
  - i.e. write:

```haskell
eval Next         = get         >>= \c ->
                    put (c + 1) >>= \_ ->
                    return c
```

<br>
<br>
<br>
<br>
<br>
<br>


### EXERCISE: get and put

Implement functions `get` and `put`, which would allow us to implement `Next` as

```haskell
eval Next = get         >>= \c ->
            put (c + 1) >>= \_ ->
            return c
```

```haskell
-- | Computation whose return value is the current counter value
get :: Counting Cnt
get = ???

-- | Computation that updates the counter value to `newCnt`
put :: Cnt -> Counting ()
put newCnt = ???
```


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


```haskell
-- | Computation whose return value is the current counter value
get :: Counting Cnt
get = \cnt -> (cnt, cnt)

-- | Computation that updates the counter value to `newCnt`
put :: Cnt -> Counting ()
put newCnt = \_ -> (newCnt, ())
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

### Monad instance for Counting

Let's make `Counting` an instance of `Monad`!

  - To do that, we need to make it a new datatype

```haskell
data Counting a = C (Cnt -> (Cnt, a))

instance Monad Counting where
  (>>=) :: Counting a -> (a -> Counting b) -> Counting b
  (>>=) (C step) process = C final
    where
      final cnt = let
                    (cnt', v) = step cnt
                    C nextStep = process v
                  in nextStep cnt'

  return :: a -> Result a
  return v = C (\cnt -> (cnt, v))
```

We also need to update `get` and `put` slightly:

```haskell
-- | Computation whose return value is the current counter value
get :: Counting Cnt
get = C (\cnt -> (cnt, cnt))

-- | Computation that updates the counter value to `newCnt`
put :: Cnt -> Counting ()
put newCnt = C (\_ -> (newCnt, ()))
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


## Cleaned-up Evaluator

Now we can use the `do` notation!

```haskell
eval :: Expr -> Counting Int
eval (Num n)      = return n
eval (Plus e1 e2) = do v1 <- eval e1
                       v2 <- eval e2
                       return (v1 + v2)
eval Next         = do
                      cnt <- get
                      _ <- put (cnt + 1)
                      return (cnt)
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


## The State Monad

Threading state is a very common task!

Instead of defining your own type `Counting a`,
you can use `State s a` from the Haskell standard library:

```haskell
data State s a = State (s -> (s, a))
```

`State` is already an instance of `Monad`,
so no need to define your own `>>=`!

<br>
<br>

Now we can simply define

```haskell
type Counting a = State Cnt a
```

and the `eval` above will just work out of the box!

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

## Outline

1. Functors [done]
2. A Monad for Error Handling [done]
3. A Monad for Mutable State [done]
4. **A monad for Non-Determinism**
5. Writing apps with monads

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

TODO

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Outline

1. Functors [done]
2. A Monad for Error Handling [done]
3. A Monad for Mutable State [done]
4. A monad for Non-Determinism [done]
5. **Writing apps with monads**


## Writing Applications

In most programming classes, they _start_ with a "Hello world!" program.

In 130, we will _end_ with it.

<br>
<br>

Why is it hard to write a program that prints "Hello world!" in Haskell?


<br>
<br>
<br>
<br>
<br>
<br>

### Haskell is pure

Haskell programs don't **do** things!

A program is an expression that evaluates to a value (and *nothing else happens*)

  - A function of type `Int -> Int`
    computes a *single integer output* from a *single integer input*
    and does **nothing else**

  - Moreover, it always returns the same output given the same input
    (*referential transparency*)


Specifically, evaluation must not have any **side effects**

- _change_ a global variable or

- _print_ to screen or

- _read_ a file or

- _send_ an email or

- _launch_ a missile.


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## But... how to write "Hello, world!"

But, we _want_ to ...

- print to screen
- read a file
- send an email

A language that only lets you write `factorial` and `fibonacci` is ... _not very useful_!

Thankfully, you _can_ do all the above via a very clever idea: `Recipe`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Recipes

[This analogy is due to Joachim Brietner][brietner]

Haskell has a special type called `IO` -- which you can think of as `Recipe`

```haskell
type Recipe a = IO a
```

<br>
<br>

A _value_ of type `Recipe a` is

- a **description** of a computation

- that **when executed** (possibly) performs side effects and

- **produces** a value of type `a`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Recipes are Pure

Baking a cake can have side effects:

- make your oven _hot_

- make your floor _dirty_

- set off your fire alarm

![Cake vs. Recipe](/static/img/cake.png){#fig:types .align-center width=80%}

*But:* merely writing down a cake recipe does not cause any side effects


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Executing Recipes

When executing a program, Haskell looks for a special value:

```haskell
main :: Recipe ()
```

This is a recipe for everything a program should do

  - that returns a unit `()`
  - i.e. does not return any useful value

<br>
<br>
<br>

The value of `main` is handed to the runtime system and *executed*

![Baker Aker](/static/img/baker-aker.jpg){#fig:types .align-center width=70%}

The Haskell runtime is a _master chef_ who is the only one allowed to produce effects!

<br>
<br>

Importantly:

  - A function of type `Int -> Int`
    **still** computes a *single integer output* from a *single integer input*
    and does **nothing else**

  - A function of type `Int -> Recipe Int`
    computes an `Int`-recipe from a single integer input
    and does **nothing else**

  - Only if I hand this recipe to `main` will any effects be produced

<br>
<br>
<br>

## Writing Apps

To write an app in Haskell, you define your own recipe `main`!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Hello World

```haskell
main :: Recipe ()
main = putStrLn "Hello, world!"
```

<br>
<br>

```haskell
putStrLn :: String -> Recipe ()
```

The function `putStrLn`

- takes as input a `String`
- returns a `Recipe ()` for printing things to screen

<br>
<br>

... and we can compile and run it

```sh
$ ghc hello.hs
$ ./hello
Hello, world!
```

<br>
<br>

This was a one-step recipe

Most interesting recipes

  - have multiple steps
  - pass intermediate results between steps

How do I write those?


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Multi-Step Recipes

Next, lets write a program that

1. **Asks** for the user's `name` using

```haskell
    getLine :: Recipe String
```

2. **Prints** out a greeting with that `name` using

```haskell
    putStrLn :: String -> Recipe ()
```

**Problem:** How to pass the **output** of _first_ recipe into the _second_ recipe?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## QUIZ: Multi-Step Recipes

If I had a function `andThen` for sequencing recipes, e.g.:

```haskell
getLine  :: Recipe String
putStrLn :: String -> Recipe ()

main :: Recipe ()
main = getLine `andThen` putStrLn
```

What should the type of `andThen` be?

**(A)** `String        -> ()                   -> ()`

**(B)** `Recipe String -> Recipe ()            -> Recipe ()`

**(C)** `Recipe String -> (String -> Recipe ()) -> Recipe ()`

**(D)** `Recipe a      -> (a      -> Recipe a ) -> Recipe a`

**(E)** `Recipe a      -> (a      -> Recipe b ) -> Recipe b`

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

## Recipes are Monads

Wait a second, the signature:

```haskell
andThen :: Recipe a -> (a -> Recipe b) -> Recipe b
```

looks just like:

```haskell
(>>=)   :: m a      -> (a -> m b)      -> m b
```

<br>
<br>

In fact, in the standard library `Recipe` is an instance of `Monad`!

<br>
<br>

So we can put this together with `putStrLn` to get:

```haskell
main :: Recipe ()
main = getLine >>= \name -> putStrLn ("Hello, " ++ name ++ "!")
```

or, using `do` notation the above becomes

```haskell
main :: Recipe ()
main = do name <- getLine
          putStrLn ("Hello, " ++ name ++ "!")
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## EXERCISE

Modify the above code so that the program _repeatedly_
asks for the users's name _until_ they provide a _non-empty_ string.

When you are done you should get the following behavior

```sh
$ ghc hello.hs

$ ./hello
What is your name?
# user hits return
What is your name?
# user hits return
What is your name?
# user hits return
What is your name?
Nadia  # user enters
Hello, Nadia!
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Recipes with Results

Let's write a function that asks the user maximum `n` times,
and if they fail to provide a non-empty string, it returns a default value `d`:

```haskell
main :: Recipe ()
main = do name <- askMany 3 "dummy"
          putStrLn $ printf "Hello %s!" name

askMany :: Int -> String -> Recipe String
askMany = ???
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

To return a result from a recipe, use the `return` function of `Monad`!

```haskell
askMany :: Int -> String -> Recipe String
askMany 0 d = return d
askMany n d = do putStrLn "What is your name?"
                 name <- getLine
                 if null name
                   then askMany (n-1) d
                   else return name
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Monads are Amazing

This code stays *the same*:

```haskell
eval :: Expr -> Interpreter Int
eval (Num n)      = return n
eval (Plus e1 e2) = do v1 <- eval e1
                       v2 <- eval e2
                       return (v1 + v2)
...
```

We can change the type `Interpreter` to implement different **effects**:

  - `type Interpreter a = Except String a` if we want to handle errors
  - `type Interpreter a = State Int a` if we want to have a counter
  - `type Interpreter a = ExceptT String (State Int) a` if we want *both*
  - `type Interpreter a = [a]` if we want to return multiple results
  - ...

<br>
<br>

Monads let us *decouple* two things:

  1. Application logic: the sequence of actions (implemented in `eval`)
  2. Effects: how actions are sequenced (implemented in `>>=`)

<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Monads are Influential

Monads have had a _revolutionary_ influence in PL, well beyond Haskell, some recent examples

- **Error handling** in `go` e.g. [1](https://speakerdeck.com/rebeccaskinner/monadic-error-handling-in-go)
and [2](https://www.innoq.com/en/blog/golang-errors-monads/)

- **Asynchrony** in JavaScript e.g. [1](https://gist.github.com/MaiaVictor/bc0c02b6d1fbc7e3dbae838fb1376c80)
and [2](https://medium.com/@dtipson/building-a-better-promise-3dd366f80c16)

- **Big data** pipelines e.g. [LinQ](https://www.microsoft.com/en-us/research/project/dryadlinq/)
and [TensorFlow](https://www.tensorflow.org/)

<br>
<br>
<br>
<br>
<br>
<br>
<br>

Thats all, folks!



[brietner]: https://www.seas.upenn.edu/~cis194/fall16/lectures/06-io-and-monads.html