---
title: "Advent of code 2021: Day 4"
date: 2021-12-05T08:48:32+01:00
category: ["functional","haskell","advent-of-code"]
---

![](/images/advent_of_code_title.jpg)

For [Advent of Code 2021](https://adventofcode.com/2021) rather than just thinking
"Hey, what a good idea - I should give that a go" and then promptly forgetting about it, I thought
it would be a good opportunity to explore [Haskell](https://www.haskell.org) a bit more.

On day 4 - when I started scratching my head for a bit - I discovered one interesting aspect about
a functionally pure approach "forced" on me by Haskell.

But let's go back a step.

# The problem

The exercise was that an input consisted of a set of 5x5 bingo cards and a set of numbers that
were going to be called, and the outcome of our program should tell us which bingo card was going
to win. A win was to be achieved by completing a row or a column (no diagonals).  More
on the problem [here](https://adventofcode.com/2021/day/4)

# Initial thought

So my first thought on how to solve this was going to be

- make a list of bingo cards
- iterate over the called numbers and cross them off
- check each row and column of each card
- stop when we find a winner

Only one problem. This sounds like a mutable data structure.  And if there's one thing that
Haskell doesn't like (feel free to correct me - I'm only a beginner) - it's mutability.

Instead of blindly hacking away with a solution that involved my first thought, I sat back and
thought for a bit.

My first thought was to create a matrix by using a list (`[]` in Haskell) of lists 
(I'll illustrate using a 3x3 board for the sake of brevity).

```haskell
[[11, 32, 13],
 [45, 35,  6],
 [17, 68, 29]]
```

But if I wanted to replace an element inside an element of a list, it just felt very inefficient.
I read about "lenses" to do some of it, but I'm still only beginning and thought maybe there's another
way.

So my second thought was to just use a single list for a board:

```haskell
[11, 32, 13,
 45, 35,  6,
 17, 68, 29]
```

Then I could try to write some code that would get the nth 3 elements to check for rows
and get every 3th element with an offset... and then I could replace an element in the list...
and then somehow iterate over the called bingo numbers... well, then I realised that this 
wasn't much better than the first idea.

# Brainwave

Rather than going down the rabbit hole of trying to modify an immutable list again, I looked back at
the problem statement.  Which board would be the first to call "Bingo".  And "Bingo" would be when
the first line had all it's numbers called.  If I knew the positions of each number, I could just
do a maximum on a row.

So if the bingo caller were to say:

```haskell
called = [11, 14, 68, 35, 29, 12, 6, 17, 32, 13, 45]
```

I could use the `zip` function to annotate numbers with their position (`zip` merges two lists
into tuples, and `[1..]` is the list of 1 to infinity - Haskell's lazy evaluation means it doesn't
actually create an infinite list):

```haskell
Prelude> zip called [1..]
[(11,1),(14,2),(68,3),(35,4),(29,5),(12,6),(6,7),(17,8),(32,9),(13,10),(45,11)]
```

I can then use the `lookup` function to get the position from that list.

```haskell
Prelude> lookup 68 calledWithPosition
Just 3
```

This then allows me to create a new version of my matrix (`mat`):

```haskell
[[(11, 1), (32, 9), (13,10)],
 [(45,11), (35, 4), ( 6, 7)],
 [(17, 8), (68, 3), (29, 5)]]
```

Now, if I turn each row into the second element of the tuple, then map each of the lists
onto their maximum, then I would get:

```haskell
Prelude> map (maximum . map snd) mat
[10,11,8]
```

Quick explanation of what's happening there.

- the first map `map` maps over each of the "inner lists" and applies
- `map snd` which takes the second value of each tuple tuple of the inner list and combines it with
- `maximum` which returns the maximum of a list.

If I then take that result and get the minimum of that list:

```haskell
Prelude> minimum $ map (maximum . map snd) mat
8
```

I can determine that the earliest that this particular bingo card wins is position 8.

## What about columns?

That's actually really easy.  A matrix or list of list can be transposed using `transpose`

So:

```haskell
Prelude Data.List> transpose mat
[[(11,1),(45,11),(17,8)],
 [(32,9),(35,4),(68,3)],
 [(13,10),(6,7),(29,5)]]
Prelude Data.List> minimum $ map (maximum . map snd) $ transpose mat
9
```

putting it together (`++` concatenates lists)

```haskell
Prelude Data.List> minimum $ map (maximum . map snd) $ mat ++ transpose mat
8
```

Now finding the bingo card that will win is as easy as calculating the above for each card and finding
the minimum.  Incidentally, part 2 of the problem was finding the worst card, which was even easier
by changing minimum to maximum.

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day4) 

# Conclusion

I found that the solution that I arrived at felt more elegant and performant than iterating over
the called numbers and "mutating the card" - admittedly I didn't do any performance testing, but
intuitively, there's fewer times that each number needs to be evaluated.

It was really interesting to think about the problem statement and that examining what the
ask was really helped to arrive at a solution.  I can't help but think that there's a lesson
in there for software designs that solutioneer too early...