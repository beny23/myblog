---
title: "Advent of code 2021: Day 10"
date: 2021-12-10T23:30:32Z
category: ["functional","haskell","advent-of-code"]
---

![](/images/advent_of_code_title.jpg)

Day 10 of [Advent of Code 2021](https://adventofcode.com/2021) was all about mismatched brackets.
And I think Haskell did rather well on this as I found the expressiveness arrived at a really
concise solution.  We're still in our Advent submarine and after mapping the floor on
[Day 9]({{< ref "advent_of_code_2021_day_9.md" >}}), we now discover lots of syntax errors
in the submarine navigation system.

# The problem

This puzzle was all about matching brackets, the input was like this:

```
[({(<(())[]>[[{[]{<()<>>
[(()[<>])]({[<{<<[]>>(
{([(<{}[<>[]}>{[]{[(<()>
(((({<>}<{<{<>}{[]{[]{}
```

And the task was to find mismatching brackets.  So `[]` is valid, as is `[<>{()}]`, but
`[(}` is not.

More on the problem [here](https://adventofcode.com/2021/day/10)

# First task

My first thought was that this wanted a stack to add open brackets onto, and then match and remove
them again for closing brackets and then processing each of the brackets.  Having done a few 
of the Advent puzzles in Haskell now, to me this meant a recursive call to process the string
of bracket characters individually.

But before I get to that, I'd like to take a detour about one of the neat things in Haskell, which 
is the idea of pattern matching functions.  For example, one of the tasks was to score the mismatching
brackets, and rather than an if-else or a switch-case statement, you can just pattern match like so:

```haskell
score :: Char -> Int
score ')' = 3
score ']' = 57
score '}' = 1197
score '>' = 25137
```

This means that when the `score` function is called with a `)` it returns `3`, ']' returns `57` etc.

Then I thought, I can use pattern matching to match the opening with closing brackets:

```haskell
parseline :: String -> Maybe Char
parseline xs = parseline' xs []

parseline' :: String -> String -> Maybe Char
parseline' ('(':cs) ss = parseline' cs ('(':ss)
parseline' ('<':cs) ss = parseline' cs ('<':ss)
parseline' ('[':cs) ss = parseline' cs ('[':ss)
parseline' ('{':cs) ss = parseline' cs ('{':ss)
parseline' (')':cs) ('(':ss) = parseline' cs ss
parseline' ('>':cs) ('<':ss) = parseline' cs ss
parseline' (']':cs) ('[':ss) = parseline' cs ss
parseline' ('}':cs) ('{':ss) = parseline' cs ss
parseline' (c:cs) _ = Just c
parseline' _ _  = Nothing
```

I'm using a `String` as a stack (in Haskell a `String` is just a list of characters) and use two lists.
The first list contains the input to process, the second list is the stack.  When there's an opening
bracket, it just puts it on the stack (cons (`'(':ss`) adds the element at the front of the list) and when
I encounter a closing bracket, I try to match it with the top of the stack.  If that fails (and there still is
input, then I return a `Just c` to indicate what's causing the error).  The last line refers to when the input
list is empty (i.e. all the input has processed).

Once I had that `Maybe Char` back, I could iterate over all the inputs and return the score:

```haskell
corruptChars = map parseline $ lines input
sum $ map score [c | Just c <- corruptChars]
```

The `[c | Just c <- corruptChar]` is a list comprehension that filters out all the `Nothing`.  I've
since discovered that this could be done a bit neater via:

```haskell
corruptChars = mapMaybe parseline $ lines input
sum $ map score corruptChars
```

# Second task

For the second part of the puzzle, instead of returning corrupt entries, the task was to finish the
incomplete ones.  As it turned out, having the stack of brackets just was what was missing, so after 
a slight adjustment to the scoring function and returning the remaining stack instead of a `Maybe Char`
made this easy:

```haskell
parseline' :: String -> String -> String
-- rest of parseline' same as above
parseline' (c:cs) _ = ""
parseline' _ ss  = ss
```

For getting the final score, the instructions were a bit convoluted:

- multiply each previous score by 5 before adding the next one
- sort all the score
- take the mean

I was surprised that there was no `mean` function in Haskell out of the box, but to be fair
it was easy enough to define:

```haskell
mean :: [a] -> a
mean xs = head $ drop (length xs `div` 2) xs
```

Doing the calculation was a simple fold:

```haskell
scoreline :: String -> Int
scoreline s = foldl (\acc c -> acc * 5 + score c) 0 s
```

And then it was just a matter of sorting and applying functions:

```haskell
incomplete = map parseline $ lines input
mean $ sort 
     $ filter (/=0) 
     $ map scoreline incomplete
```

(I also filtered out the invalid scores, which originated from an empty string `""`)

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day9) 

# Conclusion

I thought that applying pattern matching made for quite a neat and concise way of describing the
issue.  It's this expressiveness that I really appreciate!