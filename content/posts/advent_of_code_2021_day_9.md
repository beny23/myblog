---
title: "Advent of code 2021: Day 9"
date: 2021-12-09T14:00:32Z
category: ["functional","haskell","advent-of-code"]
---

![](/images/advent_of_code_title.jpg)

On day 9 of [Advent of Code 2021](https://adventofcode.com/2021) I arrived at two different solutions
for parts one and two.  That is to say the approach I took in part one turned out to be useless for
part two, though I'm pleased with both solutions.  Unlike yesterday's which got a bit convoluted 
([Day 8]({{< ref "advent_of_code_2021_day_8.md" >}})).

# The problem

This puzzle was all about looking at a height map and finding the lowest points. The input
would look like so:

|
|-|-|-|-|-|-|-|-|-|
|2|*1*|9|9|9|4|3|2|1|*0*|
|3|9|8|7|8|9|4|9|2|1|
|9|8|*5*|6|7|8|9|8|9|2|
|8|7|7|8|9|6|7|8|9|
|9|8|9|9|9|6|*5*|6|7|8|

- each of the rows and columns would refer to a height (0-9)
- the aim was to find the lowest points (here they are at coordinates (2, 1), (10, 1), (3, 3) and (7, 5))
- the solution would be to increase each of the lowest point's height's by one and then add them together

More on the problem [here](https://adventofcode.com/2021/day/9)

# Initial approach

My initial thought was around how to implement this by just going through the list, so I didn't have 
to look up neighbours looking up coordinates.  As I was going to be looking up the lowest points, and
I didn't want to deal with the edges, my first job was to add a border around the grid.  If I added
`10` around the map, then I would be able to find low points without having to consider whether I'm
at a top/bottom/left/right edge:

```haskell
makeBorder :: Int -> [Int]
makeBorder n = replicate n 10

addMargin :: [Int] -> [Int]
addMargin xs = 10 : xs ++ [10]

border = makeBorder $ length $ head heights
heightsWithBorder = border : heights ++ [border]
heightsWithBorderAndMargin = map addMargin heightsWithBorder
```

This would give me a list of lists, with some extra rows and columns.  This then allows me to parse
the map by looking at 3 rows and 3 columns at the same time:

```haskell
processlines :: [[Int]] -> [Int]
processlines (prev : curr : next : lines) = 
  (processline prev curr next) ++ processlines (curr : next : lines)
processlines _ = []

processline :: [Int] -> [Int] -> [Int] -> [Int]
processline (_ : top : top' : tops) 
            (left : curr : right : currs) 
            (_ : bottom : bottom' : bottoms) =
  (processline' curr (top : left : right : bottom : [])) ++ 
    processline (top : top' : tops) 
                (curr : right : currs) 
                (bottom : bottom' : bottoms)
processline _ _ _ = []

processline' :: Int -> [Int] -> [Int]
processline' curr others 
  | curr < minimum others = [curr]
  | otherwise             = []
```

To unpack the above:

- The `processlines` function looks at 3 lines at a time (previous, current and next), processes 
  the lines using `processline` and then recursively looks at the next set of lines.  The Haskell
  pattern matching ensures that when there are less than 3 lines left, it will not process them.
- The `processline` function does the same thing, but with columns - this way I can get the
  current cell, as well as its neighbours (top, bottom, left, right).
- The `processline'` helper method just checks whether the current position is less than all others. 
- This would return a single list of all of the minimum heights

Now that I've got the lowest heights, getting the solution is quite trivial:

```haskell
minHeights = processlines heightsWithBorderAndMargin
sum $ map (1+) minHeights
```

The `map (1+)` adds 1 to each element of the list and then I'm using `sum` to add up all the list.

# Throw it away for the next part

The second part of the problem was not just looking at the lowest points, but at "basins".  A basin
is defined as all the adjacent points of a lowest point that are higher (and in turn their adjacent
points).

This meant that my approach of processing a 3x3 moving window across the height map was not going 
to work anymore.  But at the same time, I felt that just trying to navigate the height map in "list of lists" form
was not going to be efficient (Haskell lists are linked lists, so random access was going to be expensive).  Furthermore,
I'd have to deal with how to efficiently check whether a coordinate was at the edge of a grid or not.

But then I had an idea, rather than looking at the height map as a 2-dimensional matrix, why don't I look at it as
a `Map` mapping a coordinate `(x, y)` to the height.  So first created some type aliases to make things a bit more 
readable:

```haskell
type Point = (Int, Int)
type HeightMap = Map Point Int
```

Then I converted my list of lists into a map:

```haskell
tomap :: [[Int]] -> HeightMap
tomap xs = fromList $ concat 
                    $ map maprows 
                    $ zip xs [1..]

maprows (row, y) = map (mapcols y) $ zip row [1..]
mapcols y (height, x) = ((x, y), height)
```

I used `zip` to combine the lists with their coordinates, so now I had a map of coordinates and heights:

```haskell
*Main> tomap [[3,4],[5,6]]
fromList [((1,1),3),((1,2),5),((2,1),4),((2,2),6)]
```

Next, I wanted to find the lowest points (essentially rewriting the part 1 solution).  The idea is to check
each point and see it is lower than any of it's neighbours:

First, I defined a helper function to get all the neighbours of a point, the fact that this produces invalid coordinates
does not matter, because we'll just use it to lookup entries in the map, if the point is not in the map, then it won't 
return anything.  I feel this to be an elegant way around working at the edges of the height map.

```haskell
neighbours :: Point -> [Point]
neighbours (x, y) = [(x, y-1), (x, y+1), (x-1, y), (x+1, y)]
```

Then I define a function to check whether an individual point is lower than any of it's neighbours:

```haskell
isLowestPoint :: HeightMap -> Point -> Bool
isLowestPoint hm p =
  let maybeNeighbours = map (\p -> M.lookup p hm) (neighbours p)
  in (hm ! p) < (minimum [p | Just p <- maybeNeighbours])
```

We use the `Map` `lookup` method to get a point from the map.  `lookup` returns a `Maybe` - so if we don't find a point,
it would return `Nothing`.  We then use the `[p <- Just p | maybeNeighbours]` list comprehension to get only the heights
that were returned from the map, i.e. in the middle we'd get 4 values at the edges, we'd get 2 or 3 values.  Then we
compare if the height at the point (`hm ! p` looks up the value without returning a `Maybe` but we are sure that the point
exists) is lower than the minimum of any of its neighbours.

Then all that remains is to apply this across all the points (keys) in the height map:

```haskell
findLowestPoints :: HeightMap -> [Point]
findLowestPoints hm = filter (isLowestPoint hm) $ keys hm
```

Next, we want to check for the higher points around a point in the map, the "basin", for example in the sample map above,
the basin around coordinate (3, 3) are all the points that are higher (14 of them)

|
|-|-|-|-|-|-|-|-|-|-|
|2|1|9|9|9|4|3|2|1|0|
|3|9|*8*|*7*|*8*|9|4|9|2|1|
|9|*8*|*5*|*6*|*7*|*8*|9|8|9|2|
|*8*|*7*|*6*|*7*|*8*|9|6|7|8|9|
|9|*8*|9|9|9|6|5|6|7|8|

To find the higher points:

```haskell
isHigher :: Int -> Maybe Int -> Bool
isHigher h (Just h') = h < h' && h' < 9
isHigher _ _ = False

findHigherPoints :: HeightMap -> Point -> [Point]
findHigherPoints hm p =
  let height = hm ! p
      higherNeighbours = 
        filter (\p -> isHigher height (M.lookup p hm)) 
               (neighbours p)
  in p : (concat $ map (findHigherPoints hm) higherNeighbours)
```

The `findHigherPoints` function takes the height map and a point and finds all points that are higher - and
recursively checks the higher points of those neighbours.

Now that we've got a function to find the lowest points in the height map and all higher points from a particular
origin, we can put it all together.  The solution requires to multiply the sizes of the 3 biggest basins together.
Using Haskell, that's now quite straightforward:

```haskell
lowestPoints = findLowestPoints heightMap
product $ take 3 
        $ reverse 
        $ sort 
        $ map length 
        $ map nub 
        $ map (findHigherPoints heightMap) lowestPoints
```

Unpacking this here - reading right to left:

- find all the higher points of all the lowest points
- use `nub` to remove any duplicated points
- get the length of each of the basins
- reverse sort the array
- take the first 3 items
- multiply them together using `product`

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day9) 

# Conclusion

This was very interesting as I think I was trying to be too clever in part 1, which left me with a solution that
I couldn't reuse for the second part.  Again, if I put my imperative programming hat on, I would have done this quite
differently, probably with a 2-dimensional array, but forcing myself to think functionally