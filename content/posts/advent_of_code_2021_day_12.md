---
title: "Advent of code 2021: Day 12"
date: 2021-12-13T00:11:32Z
tags: ["functional","haskell","advent-of-code"]
featured_image: "/images/advent_of_code_title.jpg"
---

For [Advent of Code 2021](https://adventofcode.com/2021) day 12 it took me a while to get going
though for reasons unrelated to coding, Norovirus is not pleasant!  So when I finally did
the puzzle, I didn't find it as difficult as I thought it would be based on some of the messages
on the `#adventofcode` channel.  The puzzle started with

> With your submarine's subterranean subsystems subsisting suboptimally

This deserves a medal in itself. In contrast the link to my previous efforts today is a bit lacking,
so here's [Day 11]({{< ref "advent_of_code_2021_day_11.md" >}}). 

The problem this time was all about charting a way through a complex cave system.  The caves are
specified using the following kind of input:

```
start-A
start-b
A-c
A-b
b-d
A-end
b-end
```

There are two types of caves - big caves are noted in uppercase, small caves in lowercase.  The aim was
to find out how many different paths could be taken through the cave system from `start` to `end`.  The only
wrinkle was that small caves could only be visited once.

# First task

My first reaction here was to just create a map of caves and every cave that it can visit.  Then I'd be able to recursively
call a function until I found the end. Ok, so let's build that map of lists.  Initially, I thought I'd just use
a standard Haskell `Map` with the key the name of the cave, and the elements lists of cave names, I'd just have to
write a bit of code to convert a simple pair structure into lists.  But then I found `MultiMap` which does the job for me:

```haskell
parseline :: String -> (String, String)
parseline s = (\[a, b] -> (a, b)) $ split '-' s
```

First, I'm turning the input strings into tuples of `(from, to)` - I found tuples to be more intuitive than arrays which
my `split` returns.  I borrowed `split` from [Day 6]({{< ref "advent_of_code_2021_day_6.md" >}}). 


Now, I had a list of tuples, I could turn it into a multimap:

```haskell
type NodeMap = M.MultiMap String String

makeMap :: [(String, String)] -> NodeMap
makeMap xs = M.fromList $ filter onlyendto 
                        $ filter onlystartfrom 
                        $ xs ++ map swap xs
```

This does the following:

- create a list of tuples and the tuples in reverse (so I could have `(from, to)` and `(to, from)`) and 
- I filtered out tuples where `"start"` is a destination or `"end"` is an origin, both are invalid moves.

Now I had my `MultiMap`, I could use it to find all the paths recursively:

```haskell
findPaths :: NodeMap -> [String] -> String -> Int
findPaths _ _ "end" = 1
findPaths nm prev from 
  | isSmallCave from && 
    any (from==) prev   = 0
  | otherwise           = 
    sum $ map (findPaths nm (from:prev)) 
        $ M.lookup from nm

isSmallCave :: String -> Bool
isSmallCave (x:xs) = isLower x
```

To unpack this:

- the function is called with the Map, a list of previously visited caves and the current cave
- the function returns the number of paths it can take to the "end"
- if we call this with the "end" cave, the return values is 1.
- if we call this with a small cave that was previously visited, then we return 0, as it is an invalid route
- otherwise (i.e. it's a large cave or a small cave we hadn't previously visited) we lookup the list of
  caves we can connect to, and recursively call `findPaths` on it and then just sum up the return values.

To get the number of differents routes, we just have to call:

```haskell
findPaths nm [] "start"
```

(when I first tried this I got a stack overflow as I had my logic backwards and `isSmallCave` was checking for 
uppercase)

# Second part

For the second part, it was the same problem, but a single small cave can be visited twice.  Initially, I misread this
and thought it said that all small caves can be visited twice, and I thought that's easy.  Instead of checking whether
a small cave is in the list of previously visited caves, I just count how often it occurs and if it's more than once, 
I return `0`.

But as that did not give me the right output on the example input, I went back, re-read the requirements and then came up 
with this:

```haskell
findPaths :: NodeMap -> [String] -> String -> Int
findPaths _ _ "end" = 1
findPaths nm prev from 
  | isSmallCave from && 
    visitedCave prev from > 1 - countDuplicateSmallCaves prev = 0
  | otherwise = sum $ map (findPaths nm (from:prev)) 
                    $ M.lookup from nm

visitedCave :: [String] -> String -> Int
visitedCave prev cave = length $ filter (cave==) prev

countDuplicateSmallCaves :: [String] -> Int
countDuplicateSmallCaves prev = length $ filter (>1) 
                                       $ map length 
                                       $ group 
                                       $ sort 
                                       $ filter isSmallCave prev
```

To unpack that:

- I created a function to get the number of times a cave was previously visited
- I also created a function to count if there are duplicate small caves in the list of previously visited
  caves. 
- Then I could just check if the number of visited caves is greater than `1 - dupes` and there for it would only 
  be possible to visit a single small cave once.
- As the code recursively visits each cave, it would automatically pick out all routes.

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day12) 

# Conclusion

In this puzzle, I used the `MultiMap` for the first time.  That doesn't come out of the box, but it just needed
`stack install multimap` to make it available.  I also wasn't terribly happy with the part 2 solution, with the
counting of the duplicate caves every time, that turned out to need 14 seconds to run.  I have a suspicion that there's
some maths that would make this a bit more performant, but it was too late at night to worry about it.