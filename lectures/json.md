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
