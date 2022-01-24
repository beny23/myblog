---
title: "Advent of code 2021: Day 8"
date: 2021-12-08T13:00:32Z
tags: ["functional","haskell","advent-of-code"]
featured_image: "/images/advent_of_code_title.jpg"
---

On day 8 [Advent of Code 2021](https://adventofcode.com/2021) adventure, part 1 lulled me into
a false sense of security.  It took longer to read the instructions than implementing the
code.  But then part 2 was back with a vengeance.  Admittedly it would have been so much
easier if I slowed down a bit and actually counted carefully.  As it happened it was a good reminder
that "more haste, less speed" applies to programming very much.  All in all, I very much enjoyed
the challenge and I reminded myself of the importance of taking things slow as it did take much
longer than [Day 7]({{< ref "advent_of_code_2021_day_7.md" >}}).

# The problem

In this puzzle it was all about methodically decoding numbers. The numbers were all specified by
the segments that light up on a digital clock:

```
  0:      1:      2:      3:      4:
 aaaa    ....    aaaa    aaaa    ....
b    c  .    c  .    c  .    c  b    c
b    c  .    c  .    c  .    c  b    c
 ....    ....    dddd    dddd    dddd
e    f  .    f  e    .  .    f  .    f
e    f  .    f  e    .  .    f  .    f
 gggg    ....    gggg    gggg    ....

  5:      6:      7:      8:      9:
 aaaa    aaaa    aaaa    aaaa    aaaa
b    .  b    .  .    c  b    c  b    c
b    .  b    .  .    c  b    c  b    c
 dddd    dddd    ....    dddd    dddd
.    f  e    f  .    f  e    f  .    f
.    f  e    f  .    f  e    f  .    f
 gggg    gggg    ....    gggg    gggg
```

Unfortunately, the wires are crossed, so it is a matter of figuring out which segment is wired to 
which, in the input (`a`, `b`, `c` above could be linked to a completely different segment) and the
input looked like this:

```
be cfbegad cbdgef fgaecd cgeb fdcge agebfd fecdb fabcd edb |
fdgacbe cefdb cefbgd gcbe
```

Each of the letters refer to a segment that would light up.  The first part of the problem was
simple:  "Identify how many times the numbers 1, 4, 7 and 8 occur in the ouput".  The output refers to the 
numbers after the pipe symbol (`|`).  That's easy because for 1, 4, 7 and 8 there's a unique number of segments
that light up (2, 4, 3 and 7):

```haskell
parseline :: String -> Int
parseline s = length $ filter (\len -> any (==len) [2,4,3,7]) 
                     $ map length 
                     $ dropWhile (/="|") 
                     $ words s
```

To unpack this:

- `words s` splits the input into a list of strings
- `dropWhile (/="|")` then drops any string that isn't a pipe symbol
- `map length` turns the strings into their lengths
- `filter (\len -> any (==len) [2,4,3,7])` filters out any lengths that aren't 2, 4, 3, 7
- `length` calculates the length of the resulting list, i.e. the count of the numbers 1, 4, 7 and 8.

Nice and easy. N.B. the `$` operator in Haskell is just a way of making the result of one function
the input of another, the above could just as easily have been written as

```haskell
parseline :: String -> Int
parseline s = length (filter (\len -> any (==len) [2,4,3,7]) 
                        (map length 
                           (dropWhile (/="|") 
                              (words s))))

```

but I find the former way much more readable. And I'm planning to do Lisp another year...

# This is where it gets tricky

For the next part of the puzzle, we needed to determine the rest of the numbers. Which took
me a while to figure out.

Eventually, I settled on the following approach:

- Find the digit with the unique lengths first
- Determine the rest of the numbers by their length AND how many segments they match in already known numbers

So, I would be able to find 1, 4, 7 and 8 straight away, then

- the digit 3 has 5 segments and overlaps the digit 7 in 3 of those
- the digit 2 has 5 segments and overlaps the digit 4 in 2 of those
- the digit 5 has 5 segments and overlaps the digit 4 in 3 of those
- the digit 9 has 6 segments and overlaps the digit 3 in 5 of those
- the digit 0 has 6 segments and overlaps the digit 1 in 2 of those
- the digit 6 is left over

I struggled a bit with those rules until I figured out that I had to determine the numbers in a 
particular order.  For quite a while I was looking at how I can determine 0, 6 and 9 just based on
previous digits and wasn't able to.

# Implementation

First I figured out how to get the unique numbers:

```haskell
-- create mapping for numbers with unique length (1, 4, 7, 8)
parseNumbersWithUniqLen :: [String] -> [(Int, String)]
parseNumbersWithUniqLen xs = 
  xs >>= (\x -> maybeToList $ fmap (\n -> (n, x)) 
                            $ lookup (length x) [(2, 1), (4, 4), (3, 7), (7, 8)])
```

Let's have a look at what this does:

- The function takes a list of strings (these would be in the format `["be","abcdefg","bcdefg","acdefg"]`)
  and uses the bind `>>=` operator to operate on each element.  
- For each element it looks up the number based on the length.  This returns a `Maybe` (similar to an `Option` in Scala
  or an `Optional` in Java).
- `fmap` the turns this into a tuple of the string and the associated number (iff the `Maybe` contains something)
- `maybeToList` turns the `Maybe` into a list. 

```haskell
Prelude Data.Maybe> maybeToList $ Just ("be", 1)
[("be",1)]
Prelude Data.Maybe> maybeToList $ Nothing
[]
```
 
- and that list is the used by the bind operator (similar to `flatMap` in Scala/Java) to replace the list.  That means
  if the `lookup` returns `Nothing` (which gets turned into an empty list `[]` by `maybeToList`) then there is nothing returned
  for that particular element

Once we've got the "unique" digits worked out, we can figure out the others, first let's define few helpers:

```haskell
overlapsNumber :: [(Int, String)] -> Int -> Int -> String -> Bool
overlapsNumber numbers num overlaps s = 
  (==overlaps) $ length 
               $ intersect s 
               $ fromJust 
               $ lookup num numbers
```

This first one takes our mapping of numbers to letters (at some point I probably ought to use a proper Haskell `Map` rather
than a list of tuples, but it works for me now), and tries to check how many segments overlap.  To do the overlapping count
I just use `intersect`:

```haskell
Prelude Data.List> intersect "abcd" "bde"
"bd"
```

This gives me the number of segments that match.  Subsequently this method checks whether the length is as expected. This
helper is then used to parse the existing numbers (here called `xs`) check them against the existing mappings (`numbers`):

```haskell
parseNumber :: Int -> Int -> Int -> Int -> [String] -> [(Int, String)] -> [(Int, String)]
parseNumber num srcNum overlaps len xs numbers =
   map (\y -> (num, y)) $ filter (overlapsNumber numbers srcNum overlaps) 
                        $ filter (isLen len) 
                        $ filter (exists numbers) xs
```

The above does the following:

- First, it filters out any digits that already exist in the matching - this helps us to prevent matching the same digit
  twice
- Then we check that the length matches the input `len`
- Then we use the `overlapsNumber` helper to check that it matches `overlaps` segments of the `srcNum`
- If all that matches, we return the new number in a list.

That's then used like so:

```haskell
parseNumbers :: [String] -> [(Int, String)] -> [(Int, String)]
parseNumbers xs uniqNums = 
  foldl (\numbers f -> numbers ++ f xs numbers) uniqNums [
    parseNumber 3 7 3 5,
    parseNumber 2 4 2 5,
    parseNumber 5 4 3 5,
    parseNumber 9 3 5 6,
    parseNumber 0 1 2 6,
    parseNumber 6 0 5 6]
```

This starts with the numbers worked out by `parseNumbersWithUniqLen` and then uses a fold and the `parseNumber` helper to
build up the rest of the numbers.  Here a list of curried functions is passed to the fold and it applies them.

That just leaves us to put it all together:

```haskell
parseLine :: String -> [Int]
parseLine s = 
  let strings = parseAllStrings s
      numbers = map (\(a,b) -> (b,a)) $ parseNumbers strings 
                                      $ parseNumbersWithUniqLen strings 
      outputs = parseOutputs s
  in map (\x -> fromJust $ lookup x numbers) outputs
```

What happens here:

- `parseAllStrings` gets all the digit segment strings, feeds that into
- `parseNumbersWithUniqLen` which gets the easy digits (1, 4, 7, 8)
- that then gets fed into `parseNumbers` to get the other digits, then it
- swaps around the mapping, so that we can lookup digits from their segment strings
- and then looks up the output strings

Finally, the `todec` function is used to turn a list of digits like `[5, 3, 5, 3]` into a decimal number `5353` 

```haskell
todec :: [Int] -> Int
todec ns = foldl (\s n -> 10 * s + n) 0 ns
```

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day8) 

# Conclusion

This was hard! But again, my suspicion is that Haskell and functional helped me there.  One thing that is really useful
in Haskell is that idea of specifying the type signature first, and then providing the implementation.