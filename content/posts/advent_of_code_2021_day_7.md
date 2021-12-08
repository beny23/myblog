---
title: "Advent of code 2021: Day 7"
date: 2021-12-07T09:00:32Z
category: ["functional","haskell","advent-of-code"]
---

![](/images/advent_of_code_title.jpg)

On the 7th day of the submarine [Advent of Code 2021](https://adventofcode.com/2021) adventure, I 
felt like the problem was getting a bit easier than it had been of late.  If anything I felt a touch
suspicious on whether I had been missing anything.  In previous days I had to reframe the problem
statement a bit to make it efficient ([Day 4]({{< ref "advent_of_code_2021_day_4.md" >}}), 
[Day 5]({{< ref "advent_of_code_2021_day_5.md" >}}) and [Day 5]({{< ref "advent_of_code_2021_day_5.md" >}})). 
Either that or I'm starting to feel more comfortable with Haskell.  In which case the Advent of Code
is a success for me already as I'm learning lots.

# The problem

This puzzle was all about lining up crab submarines!

- The input consists of a list of the crabs' horizontal positions
- The aim is to move all the crabs into the same position with the minimum amount of cost (moving 
  1 position costs 1 fuel)

More on the problem [here](https://adventofcode.com/2021/day/7)

# Initial thought

My first thought on this problem was whether this could be solved by using the mean or average of all
positions, but I discounted that after checking whether this would work for the example, so I
thought I'd just calculate the cost of moving each crab to each of the positions and then pick 
the minimum.

# Implementing it

There was one optimisation that I could think of straight away, I'd use my trusty frequency list to
check if any crabs were in the same position and then the cost can just be multiplied by the number
of crabs at that position.

```haskell
grouppos :: [Int] -> [(Int, Int)]
grouppos xs = map (\x -> (head x, length x)) $ group $ sort xs
```

The `grouppos` function would turn the list of positions into a list of tuples where it is
`(position, number of occurrences)`. 

I also created a function to calculate the cost of moving a single crab submarine:

```haskell
distance :: Int -> Int -> Int
distance x y = abs (x - y)
```

Then I can put both together to create the total distance covered:

```haskell
distances :: [(Int, Int)] -> Int -> Int
distances xs n = sum $ map (\(p, c) -> c * distance n p) xs
```

Essentially, just the distances multiplied by the occurrences and then summing it up.

Now I had all the building blocks to answer the question:

```haskell
let start = minimum pos
let end = maximum pos
print $ minimum $ map (distances posfreq) [start..end]
```

So I get the minimum and maximum position, then I try every position inbetween (calculating the
distances of moving everyone there) - then I just need the minimum from this list.

Part two of the puzzle changed the cost of movement, by stating that each move is more expensive than
the last.  So if a crab moves 3 positions, it will cost 1+2+3 fuel.  That could easily be achieved
by adapting the `distance` function to use a bit of Gauss:

```haskell
distance :: Int -> Int -> Int
distance x y = n * (n + 1) `div` 2 where n = abs (x - y)
```

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day7) 

# Conclusion

This puzzle almost felt like a little bit of a letdown as it didn't feel as complex.  Either that or
my mathematics is letting me down to come up with a better approach.