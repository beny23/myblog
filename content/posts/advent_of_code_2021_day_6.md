---
title: "Advent of code 2021: Day 6"
date: 2021-12-06T13:46:32Z
tags: ["functional","haskell","advent-of-code"]
featured_image: "/images/advent_of_code_title.jpg"
---

On the next day of [Advent of Code 2021](https://adventofcode.com/2021) there were two approaches
to solving the problem.  Brute force and ignorance, and stopping to think about it.  After finding out that 
[Haskell](https://www.haskell.org) often forces me to having a think first on
[Day 4]({{< ref "advent_of_code_2021_day_4.md" >}}) and [Day 5]({{< ref "advent_of_code_2021_day_5.md" >}}),
I was looking forward to 

# The problem

This puzzle was all about lanternfish population - yes there was a submarine theme to this year!

- The input consists of a list of lanternfish and their age
- When a lanternfish is 6 days old, an offspring hatches
- An offspring takes 8 days to reproduce on the first cycle, every 6 days from thereon in
- The solution is the total population after 80 days

More on the problem [here](https://adventofcode.com/2021/day/6)

# Initial thought

My first thought on this problem was that this wasn't something to solve using brute force.
The puzzle text mentions "exponential", so there was a fair chance that even if part 1 could
be brute forced, part 2 was going to take a fair while using "ignorance".

# Brainwave

Looking at the beginning of the puzzle input

```
2,1,1,4,4,1,3,4,2,4,2,1,1,4,3,5,...
```

I quickly determined that there would be a lot of repetition. If I was going to calculate how many 
fish were 1,2,3,... days old, rather than calculating it for each and every fish, it would be a lot
more efficient. Thankfully, the previous puzzle on [Day 5]({{< ref "advent_of_code_2021_day_5.md" >}})
already taught me how to do a frequency list.

```haskell
groupages :: [Int] -> [Int]
groupages xs = map length $ group $ sort xs
```

Note, this only works if there are no gaps in the list - but as it happens, the input is complete and 
leaves no gaps, so I didn't worry about it.

Once I had my array, I made a function to "advance a day":

```haskell
nextgeneration' :: [Int] -> [Int]
nextgeneration' (p0 : p1 : p2 : p3 : p4 : p5 : p6 : p7 : p8 : []) 
  = p1 : p2 : p3 : p4 : p5 : p6 : p7+p0 : p8 : p0 : []
```

Note, this only works if the array is at least 9 elements long (but I wrote a function to ensure that the array is always
of length 9).

This then allowed me to recursively grow the lanternfish:

```haskell
grow :: Int -> [Int] -> [Int]
grow 0 xs = xs
grow n xs = grow (n-1) $ nextgeneration xs
```

One gotcha I had was, that because I started my list at index 0, I didn't have to do "grow" for the first generation.  So when I calculated
the solution for part 1:

```haskell
ages = groupages $ parseages input
sum $ grow 79 ages
```

and because the complexity was `O(n)` - doing part 2 - where 256 days of growth were to be calculated, it only took a
little longer.

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day6) 

# Conclusion

I'm really starting to enjoy trying to abstract away the problem and think the expressiveness in Haskell is helping
me here and guiding me to a simpler solution.