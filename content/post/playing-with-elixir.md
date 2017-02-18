+++
title = "def Elixir"
date = "2015-05-02"
comments = true
tags = ["elixir"]
+++

# The Penguins
Seems like another *hot* language is trying to catch-up mainstream and
I'm playing with it for a while now. Was checking some functions
defined in Elixir's standard lib modules. I Just hit on this:

```.elixir
iex(1)> h Stream.cycle

def cycle(enumerable)    

Creates a stream that cycles through the given enumerable, infinitely.  

Examples

    ┃ iex> stream = Stream.cycle([1,2,3])
    ┃ iex> Enum.take(stream, 5)
    ┃ [1,2,3,1,2]
```

# Not sure if certain morphisms were dumb or its just me
That reminds me of the moment I first got introduced to *cycle*
function in *Haskell*.

Back then I was like who needs a function that `just` *creates a
stream that cycles given numbers, infinitely*.

Whats so worse... I even have a function called *id* in *Haskell* that
gets *something* and gives it back without doing anything.  Why on the
earth do someone needs such a function ?? For proving laws in Category
Theory ?? `meh`

For simplistic reasons, examples for these functions are like that
everywhere which didn't made any sense for me for a long time :D

# The Moment of Awe
I once wanted to write a function that takes a list of integers and
returns a list with integers at even indexes doubled in given list.

{C,C++,Java,..(others)} : Write a for loop from 0 to list.length-1 and
use a if conditional to check if index is even, if so assign doubled
value to that index. Finally return the list.

{Elixir, Haskell,...(FPs)} : Cool story bro! But we can't
mutate. Period.

`Stream.cycle` here to rescue!

# Higher Order Functions

`Functions` are *first class citizens* in Elixir which means they
deserve the same rights that 1,2,3,. *you* and *me* own in `Elixir
Realm`.

If 1,2,3.. can fit into a list then a list of `Functions` is also
possible.  `Functions *ARE* values`!

Functions that takes/returns functions are said to be *Higher Order
Functions*. `Enum.map` is one such commonly used *HOF*.

```.elixir
 iex> Enum.map([a: 1, b: 2], fn({k, v}) -> {k, -v} end)
 [a: -1, b: -2]
```

`Stream.cycle` needs an *enumerable*. A list is a *enumerable* and
its elements can be *functions*.

Now I need two functions in that list to solve the problem. One is
that under-estimated `id` function and other is a function that
doubles the given value.  I can't find a built-in *id* function in
*Elixir*, so lemme create one :

```.elixir
iex> id = &(&1)

```

```.elixir
iex> id = &(&1)
#Function<6.90072148/1 in :erl_eval.expr/5>
iex> id.(42)
42
iex> id.(id)
#Function<6.90072148/1 in :erl_eval.expr/5>
iex> id.(id).(42)
42
iex> id.(id).(id).(42)
42
iex>
```

Yay! it does only one thing and does it well :D

Now the doubling function :

```.elixir
iex> double = &(&1*2)
#Function<6.90072148/1 in :erl_eval.expr/5>
iex> double.(42)
84
iex>
```

Thus I now have a list of functions :

```.elixir
iex> [id,double]
[#Function<6.90072148/1 in :erl_eval.expr/5>,
#Function<6.90072148/1 in :erl_eval.expr/5>]
iex>
```

And what happens when I pass this list to `Stream.cycle` ?

```.elixir
iex> Stream.cycle([id,double])                
#Function<47.29647706/2 in Stream.unfold/2>
```

Seems like Elixir is as lazy as me.. It just gave me a `Promise`
that *when* you need it, it *will* be available.

How do I tell Elixir that I need *x* elements from that *lazy*
sequence now ?  - A cousin of *UNIX*'s *Pipe* operator is here ..

```.elixir
iex> Stream.cycle([id,double]) |> Enum.take 5
[#Function<6.90072148/1 in :erl_eval.expr/5>,
#Function<6.90072148/1 in :erl_eval.expr/5>,
#Function<6.90072148/1 in :erl_eval.expr/5>,
#Function<6.90072148/1 in :erl_eval.expr/5>,
#Function<6.90072148/1 in :erl_eval.expr/5>]
iex>
```

Imagine that like [id, double, id, double, id].

Now, there is a function `Enum.zip` in *Elixir* that zips two lists
into a list of *Tuples*.

```.elixir
iex> Enum.zip 1..5, 6..10
[{1, 6}, {2, 7}, {3, 8}, {4, 9}, {5, 10}]
iex>
```

But what if I can pass values to a binary function and store back the
result of that function rather than creating *Tuples* while *zipping*
two lists ?

There is a `zipWith` function in *Haskell* for this :

```.haskell
λ zipWith (+) [1..5] [6..10]
[7,9,11,13,15]
λ
```

I can't find one in *Elixir* stdlib and *THAT'S OKAY!*

lemme write one..  
To write a `zipWith`, I need `apply/2` from
`Kernel` *module*.  Here is what *doc* says :

```.elixir
iex> h Kernel.apply

def apply(fun, args)

Invokes the given fun with the array of arguments args.

Inlined by the compiler.

Examples

┃ iex> apply(fn x -> x * 2 end, [2])
┃ 4

```

Function and a list of args :

```.elixir
iex> apply &+/2, [20,22]
42
iex>
```

Now the `zipWith` :

```.elixir
iex> zipWith = &(Stream.zip(&2, &3)
             |> Enum.map fn {a,b} -> apply &1, [a,b] end)
#Function<18.90072148/3 in :erl_eval.expr/5>
iex> zipWith.(&+/2, 1..5, 6..10)                                                
[7, 9, 11, 13, 15]
iex>   
```

*Cool!*

Now, I need one last function that is very similar to `id`
function. That's a Identity function specially made for
functions. `($)` function in *Haskell*.

This is how it is used in *Haskell*:

```.haskell
λ zipWith ($) (cycle [id,(\n -> n*2)]) [1..10]
[1,4,3,8,5,12,7,16,9,20]
λ
```

($) function creates some kind of *delay* in function application.

lemme write the `fap` (`f`unction `ap`plication) function...

```.elixir
iex> fap = &(apply &1, [&2])                                      
#Function<12.90072148/2 in :erl_eval.expr/5>
iex> apply fap, [&(&1*2), 42]
84
iex>
```

Finally....

```.elixir
iex> zipWith.(fap, (Stream.cycle [id, double]), 1..10)             
[1, 4, 3, 8, 5, 12, 7, 16, 9, 20]
iex>
```

Indeed *id* and *cycle* are not *dumb*, It was *me*!!
