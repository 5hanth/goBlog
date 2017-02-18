+++
title = "Category Of Functions"
date = "2014-10-18"
tags = ["haskell"]
+++

Monad Class defines basically two functions or interfaces or whatever
you call it :

```.haskell
class  Monad m  where
   (>>=)       :: forall a b. m a -> (a -> m b) -> m b
   return      :: a -> m a
```

Apart from *bind (>>=)* and *identity (return)* functions, it also
defines *(>>)* and the *fail* function.

And to treat The Category of functions as monad, Monad Instance for
functions as defined in haskell standard library looks like this:

```.haskell
instance Monad ((->) r) where
    return = const
    f >>= k = \ r -> k (f r) r
```

*(->)* means Function Type. For functions, *identity* function takes a
*value* ( can be a function ) and takes *another value* ( Can be a
function again ) and gives back the *former value*.

Nothing special. Now the fun part is with the *bind (>>=)* function.
It takes two functions ( Elements of Category (->) ) and gives a
function ( Element belonging to the same Category ). So what ?

```.haskell
> ( reverse >>= (==) ) "GoDDoG"
True
>
```

I just wrote the epic Palindrome Function. That bind can be expanded
like this :

```.haskell
>   (\xs -> (==) (reverse xs) xs )  "GoDDoG"
--  (\ r ->   k  (f        r) r  )  "GoDDoG"   
-- Aries lined up as in (>>=) definition of (->) Monad Instance
```

This is so significant in writing point-free code :)

But with *Arrows*, this is not so nice involving some plumbing works
of uncurrying.

```.haskell
> ( uncurry (==) . ( reverse &&& id ) ) "GoddoG"
True
```
