---
title: "Advent of code 2021: Day 11"
date: 2021-12-11T15:00:32Z
category: ["functional","haskell","advent-of-code"]
---

![](/images/advent_of_code_title.jpg)

[Advent of Code 2021](https://adventofcode.com/2021) day 11 felt a little bit like a repeat 
of [Day 9]({{< ref "advent_of_code_2021_day_9.md" >}}).  In both cases, we got a 2-dimensional
map with single digit values.  In case of day 9 it was a height map, this time round we've got
a 10x10 grid of bioluminescent Octopi.  Each of those critters has an energy level that increases
each round.  Once that level goes past 9, it lets of a flash, which then imparts extra energy 
into the surrounding 8-legged creatures.  In a departure from Day 9, this time the diagonals
were classed as adjacent.

# First task

For the first task, we had to count how many flashes go off after 100 rounds.  Again, rather than
using a matrix, I opted for a Map that would map a point to the energy level:

```haskell
type Point = (Int, Int)
type FlashMap = Map Point Int
```

Then I looked at implementing a function that would increase every point on the `FlashMap` by 1 
energy level.  First, a helper to define all the neighbours of a given point:

```haskell
neighbours :: Point -> [Point]
neighbours (x, y) = [(x-1, y-1), 
                     (x, y-1), 
                     (x+1, y-1), 
                     (x-1, y), 
                     (x+1, y), 
                     (x-1, y+1), 
                     (x, y+1), 
                     (x+1, y+1)]
```

Pretty straightforward! Then I figured that I should split the "energy increase" and the "count 
how many flashes and reset the counters" into two stages.  That would prevent me from double counting
flashes, if the energy level increased over 9 multiple times if more than one neighbour flashes.

To start with, I looked at increasing the energy level for a single point:

```haskell
incOne :: FlashMap -> Point -> FlashMap
incOne fm p = triggerNeighbours p $ 
              updateLookupWithKey (\p f -> Just (f+1)) p fm

triggerNeighbours :: Point -> (Maybe Int, FlashMap) -> FlashMap
triggerNeighbours p (Just f, fm)
  | f == 10   = foldl incOne fm $ neighbours p
  | otherwise = fm
triggerNeighbours _ (_, fm) = fm
```

There's a lot in two functions (which recursively call each other).  So first looking at `incOne`:

- The `updateLookupWithKey` method on the haskell `Map` looks up the value for they key `p` (the coordinates of
  the current point), if it finds something it runs the `\p f -> Just (f+1)` lambda, which returns an increased value
  which is updated in the map, and the looked up value AND the new map (remember, everything is immutable) are returned 
  as a tuple.
- That tuple (looked up value and new map) is then fed to `triggerNeighbours` as well as the point `p`. Using pattern
  matching, we then check whether the new value is `10` - and therefore a flash is triggered.  If that's the case we
  call `incOne` for each of the `neighbours`.  I find that `foldl` is very useful here, because it passes the `fm` 
  Flash Map along to deal with all of the neighbours.  Note, that by using `updateLookupWithKey` we automatically deal
  with coordinates at the edge of the map.  The lookup will not find anything, which will just return a `Nothing` and 
  that in turn is covered by the `triggerNeighbours _ (_, fm) = fm` pattern.  That just returns the `fm` unchanged.  

Now that we've increased the energy levels for a single point, we can use that function to increase the energy levels
in every point:

```haskell
incAll :: FlashMap -> FlashMap
incAll fm = foldl incOne fm $ keys fm
```

Again, the `foldl` (read as "fold left") will process each of the points in the map (all the `keys`).

Next, we deal with counting the number of flashes and resetting the counter after the all the energy levels have been 
increased.  The flashes can be counted by any points in the map that have a level greater than 9.  And then we want to
change those points back to 0 at the same time.

Initially I thought, maybe a state monad will be helpful there (and I was looking forward to learning about them!) but 
then I discovered the `mapAccum` function, which both changes each value of the map as well as allowing to get an 
accumulated value.  Ideal, for the flash counting and energy level resetting:

```haskell
countFlashAndReset :: FlashMap -> (Int, FlashMap)
countFlashAndReset fm = mapAccum countFlashAndReset' 0 fm

countFlashAndReset' :: Int -> Int -> (Int, Int)
countFlashAndReset' acc f 
  | f > 9     = (acc + 1, 0)
  | otherwise = (acc, f)
```

The `countFlashAndReset'` function (it still feels strange to have `'` as part of a symbol, but I kind of like that
mathematical feel of the function notation as it makes it clear that the `'` function is just related to the non-`'` 
function - but I digress) uses the `mapAccum` function and starts with an `acc` accumulator value of `0` and then for
every flash, it increases it by one (first value in the tuple) and resets the map value (second value of the tuple).

Now all that was left to do was to bring it all together.  First I defined a function for a single iteration:

```haskell
iter :: (Int, FlashMap) -> (Int, FlashMap)
iter (acc, fm) = addCount acc $ countFlashAndReset $ incAll fm

addCount :: Int -> (Int, FlashMap) -> (Int, FlashMap)
addCount acc (acc', fm) = (acc + acc', fm)
```

It combines `incAll`, then feeds that into `countFlashAndReset` and then just adds the output together. This then allows 
us to iterate for `n` iterations recursively:

```haskell
iterN :: Int -> (Int, FlashMap) -> (Int, FlashMap)
iterN 0 fm = fm
iterN n fm = iterN (n-1) $ iter fm
```

# Second part

For the second part of the puzzle, we needed to find out how many iterations need to pass before all flashes go off
at the same time.  This meant we're no longer interested in counting the number of flashes, which means `countFlashAndReset`
could be replaced with:

```haskell
resetAfterFlash :: FlashMap -> FlashMap
resetAfterFlash fm = M.map resetAfterFlash' fm

resetAfterFlash' :: Int -> Int
resetAfterFlash' f 
  | f > 9     = 0
  | otherwise = f
```

And we needed a way of checking whether all the flashes had gone off.  This could be done by checking whether all the 
values in the `FlashMap` were zero:

```haskell
allZero :: FlashMap -> Bool
allZero fm = all (==0) $ M.elems fm
```

That can then be called recursively to keep iterating until `allZero` was true:

```haskell
iterN :: FlashMap -> Int
iterN fm 
  | allZero fm = 0
  | otherwise  = 1 + (iterN $ iter fm)
```

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day11) 

# Conclusion

This puzzle taught me something about the wealth of functions available in haskell and that [Maps](https://hackage.haskell.org/package/containers-0.6.5.1/docs/Data-Map-Strict.html)
and Hackage were very very useful resources.  I guess I had to wait for my first foray into Monads for another time.
After all, how hard can it be, monads are only monoids in the category of endofunctors after all!