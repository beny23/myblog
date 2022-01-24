---
title: "Advent of code 2021: Day 5"
date: 2021-12-06T12:48:32Z
tags: ["functional","haskell","advent-of-code"]
featured_image: "/images/advent_of_code_title.jpg"
---

Following from my efforts on [Day 4]({{< ref "advent_of_code_2021_day_4.md" >}})
of [Advent of Code 2021](https://adventofcode.com/2021) I was encouraged to try writing up
more of my attempts to teach myself [Haskell](https://www.haskell.org) while having fun trying
to solve puzzles.

# The problem

The exercise was concerned with drawing lines on a grid and finding intersections:

> - An entry like 1,1 -> 1,3 covers points 1,1, 1,2, and 1,3.
> - An entry like 9,7 -> 7,7 covers points 9,7, 8,7, and 7,7.

The aim was to count the number of points where lines overlap. More
on the problem [here](https://adventofcode.com/2021/day/5)

# Initial thought

So my first thought on how to solve this was going to be

- make a grid
- find the points of each line (only vertical and horizontal lines to start with)
- mark them on the grid
- check the grid to find intersections

I encountered the same issue as the day before: This sounds like a mutable data structure.  And if there's one thing that
Haskell doesn't like (feel free to correct me - I'm only a beginner) - it's mutability.

# Brainwave

Rather than creating a grid, I only really needed to find out the points that get created by each line and then to
filter out all the points that only occur a single time.

If I represent each line as a tuple (start, end) of tuples (x, y):

```haskell
((Int, Int), (Int, Int))
```

then I can use the following function to create all the horizontal and vertical lines:

```haskell
genline :: ((Int, Int), (Int, Int)) -> [(Int, Int)]
genline ((x1, y1), (x2, y2))
  | x1 == x2 = [(x1, y) | y <- [(min y1 y2)..(max y1 y2)]]
  | y1 == y2 = [(x, y1) | x <- [(min x1 x2)..(max x1 x2)]]
  | otherwise = []
```

so I can create all points on a line:

```haskell
*Main> genline ((1,1), (1,3))
[(1,1),(1,2),(1,3)]
```

then finding duplicates becomes easy (assuming `coords` is a list of all the lines):

```haskell
points = concat $ map genline coords
summary = map length $ group $ sort points
length $ filter (>1) summary
```

Unpacking this a little bit:

- `map genline coords` creates a list of list of points
- `concat` merges this into a single long list
- `group $ sort points` groups all the same values into lists
- `map length` then turns this into a list of the lengths
- `filter (>1) summary` filters out all elements of the list that are not greater than one (i.e. all the points with no intersections)
- `length` gives me the number of elements - i.e. the number of intersections.

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day5) 

# Conclusion

I found that the solution that I arrived at felt more elegant than creating a grid and trying
to fill it in. Like the day before, the trick here was to step back and think about the problem
before charging in.  Incidentally, part two of the puzzle (which introduced 45 degree diagonals)
was easy to solve with this approach as I just needed to change the `genline` function to also
create a list of points for diagonals.
