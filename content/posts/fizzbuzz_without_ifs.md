---
title: "Fizzbuzz without if clauses"
date: 2021-03-04T08:44:19Z
category: ["functional", "haskell"]
---

![](/images/fizzbuzz_without_ifs_title.png)

In this writing I aim to complete a Fizzbuzz without if statements, conditionals, pattern matching
or even using modulus calculations.  And if that isn't enough I thought I'd use the opportunity
to explore [Haskell](https://www.haskell.org).  

The idea originated in the Friday lunchtime "Curry Club" at HMRC Digital where a few like-minded software engineers
are getting together to teach themselves Haskell.  (For those not in on the joke, the language is named
after the logician Haskell Curry).  At one of those sessions, talking about ifs and conditionals the 
challenge was posited that a Fizzbuzz can be done without ifs.

A Fizzbuzz test is a fairly common programming challenge, often used to evaluate a developer's
skill level. The basic instructions are as follows

> Write a class that produces the following for any contiguous range of integers:
>
> the number
> - 'fizz' for numbers that are multiples of 3
> - 'buzz' for numbers that are multiples of 5
> - 'fizzbuzz' for numbers that are multiples of 15
>
> e.g. Running the program with a range from 1-20 should produce the following result:
>
> `1 2 fizz 4 buzz fizz 7 8 fizz buzz 11 fizz 13 14 fizzbuzz 16 17 fizz 19 buzz`

Now, I've marked a fair number of fizzbuzz challenges in my time and the most important piece of 
advice is: Keep it simple!

Under normal condition, the following pseudo code lends itself to the better solutions:

```
if (number is multiple of 15) print "fizzbuzz"
else if (number is multiple of 5) print "fizz"
else if (number is multiple of 3) print "buzz"
else print number
```

It does not try to be clever (many candidates fall down because they want to show off the latest
shiny technique, but technical interviewers wouldn't be looking to that) and does the job. Simple!

# Doing without ifs

So how could this be achieved without if clauses or using modulo calculations?  I would class 
pattern matching as cheating here, so the following scala snippet would also be disqualified:

```scala worksheet
scala> (1 to 20).map {
     | case n if n % 15 == 0 => "fizzbuzz"
     | case n if n % 5 == 0 => "buzz"
     | case n if n % 3 == 0 => "fizz"
     | case n => n.toString
     | }
res0 = Vector(1, 2, fizz, 4, buzz, fizz, 7, 8, fizz, buzz, 11, fizz, 13, 14, fizzbuzz, 16, 17, fizz, 19, buzz)
scala>
```

# Haskell complete solution

I'll just go ahead and show what I ended up coming up with:

```haskell
Prelude> replace n v xs = zipWith ($) (cycle ((replicate (n-1) (id)) ++ [\x -> v])) xs
Prelude> replace 15 "fizzbuzz" . replace 5 "buzz" . replace 3 "fizz" $ map show [1..20]
["1","2","fizz","4","buzz","fizz","7","8","fizz","buzz","11","fizz","13","14","fizzbuzz","16","17","fizz","19","buzz"]
```

Now I will break it down.  The intuition was that I would be able to use a method loosely (very loosely)
inspired by the Sieve of Eratosthenes, whereby I would create lists of functions that would replace
every 3rd element by "fizz", every 5th element by "buzz" and every 15th element by "fizzbuzz".

So essentially what I was after are lists like so

```
["1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", "12", "13", "14", "15", ...]
["", "", "fizz", "", "", "fizz", "", "", "fizz", "", "", "fizz", "", "", "fizz", ...]
["", "", "",     "", "buzz", "", "", "", "",     "buzz", "", "", "", "", "buzz", ...]
["", "", "",     "", "",     "", "", "", "",     "",     "", "", "", "", "fizzbuzz", ...]
```

where any non-empty values replace the values in the previous list.  To do so, I use a mixture
of `replicate` and `cycle`.

```haskell
(replicate (n-1) (id)) ++ [\x -> v]
```

This creates a list where the `id` function repeated `n-1` times followed by a lambda that ignores the
input and just replaces it with a value. So if a call this with `n=3` and `v="fizz"`, what I get is:

```
[id, id, \x -> v]
```

Then I use the `cycle` function to create an infinite repeating list.  This makes use of the fact that
Haskell is lazily evaluated, meaning that I list can be defined as infinite but its constituent elements
are not created until they're used, so:

```haskell
cycle [id, id, \x -> v] = 
  [id, id, \x -> v, id, id, \x -> v, id, id, \x -> v, id, id, \x -> v, ...]
```

This gives me my `replace` function:

```haskell
replace n v xs = zipWith ($) (cycle ((replicate (n-1) (id)) ++ [\x -> v])) xs
```

whereby `zipWith ($) listOfFunctions values` creates a new list that applies the first function to
the first value, second function to the second value, etc, so, if call this with `n=6` and `v="fizz"`
and `xs=[1..6]`, the following substitutions can be made:

```haskell
zipWith ($) (cycle ((replicate (n-1) (id)) ++ [\x -> v])) xs
zipWith ($) (cycle ((replicate 2 (id)) ++ [\x -> "fizz"])) ["1".."6"]
zipWith ($) (cycle [id, id, \x -> v]) ["1".."6"]
zipWith ($) [id, id, \x -> v, id, id, \x -> v] ["1".."6"]
[id $ "1", id $ "2", "3" -> "fizz", id $ "4", id $ "5", "6" -> "fizz"]
["1", "2", "fizz", "4", "5", "fizz"]
```

So to put it all together, we then want to replace all the "fizz", "buzz" and "fizzbuzz" entries in
our list, to do that I think the dot operator in Haskell makes for nice reading:

```haskell
(f . g) x = f (g x)
```

so the snippet

```haskell
replace 15 "fizzbuzz" . replace 5 "buzz" . replace 3 "fizz"
```

just creates a single function that first replaces all the "fizz", then "buzz", then "fizzbuzz"

Finally, we've got our list of numbers.  But in this case we don't actually want a list of numbers,
as our `replace` function is expecting strings, so the following creates our list of string
by using the `show` function

```haskell
Prelude> map show [1..20]
["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20"]
```

We just combine the function with our list and there's the Fizzbuzz answer:

```haskell
Prelude> replace n v xs = zipWith ($) (cycle ((replicate (n-1) (id)) ++ [\x -> v])) xs
Prelude> replace 15 "fizzbuzz" . replace 5 "buzz" . replace 3 "fizz" $ map show [1..20]
["1","2","fizz","4","buzz","fizz","7","8","fizz","buzz","11","fizz","13","14","fizzbuzz","16","17","fizz","19","buzz"]
```

In summary, I've tried to get an alternative view on how a Fizzbuzz can be implemented, without
using the traditional tools.  Now, if I'm honest, if I were to mark such a submission, I would be disappointed
that it doesn't allow me to use arbitrary ranges. My solution above would not provide the correct answer
for a range that doesn't start with 1.

This was a fun exercise and hope will give you some food for thought.