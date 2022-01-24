---
title: "Advent of code 2021: Day 13"
date: 2021-12-14T01:15:32Z
tags: ["functional","haskell","advent-of-code"]
featured_image: "/images/advent_of_code_title.jpg"
---

On day 13 of [Advent of Code 2021](https://adventofcode.com/2021) we had to do the impossible.  We
were folding a transparent piece of paper 12 times.  Surely it isn't possible to fold paper
more than 7 times, but we also have to remember that we're in a Santa submarine!

The aim of the puzzle is to get a code by folding over a transparent sheet of paper which has got dots
dotted all over.  With the precise coordinates of the dots and fold instructions as the input, we were to
fold the paper and see which dots would remain and spell out a code.

# First part

The input for the puzzle looked like so:

```
6,10
0,14
9,10
0,3
10,4

fold along y=7
fold along x=5
```

So we'd have to parse the input in two sections.  The points and the folds.  Once we had that, I figured that the
problem would be fairly straightforward.  I figured to create a list of points `(x, y)` and then when the fold happens
I would transform each of the points based on the type of fold.  If I had a fold along the x-axis, I'd just modify the
y coordinate, etc.  At the end of each fold I'd dedupe the list of points to take care of the points that are now 
overlapping.

The main issue was around how to most efficiently parse the input.  In the end, I decided to cheat:

* If a line contained a `,` I would consider it a coordinate
* If a line started with "fold along y", it would be a y fold
* If a line started with "fold along x", it would be an x fold
* Every other line, I'd ignore

In Haskell this would look like this:

```haskell
type Point = (Int, Int)

parsePoints :: [String] -> [Point]
parsePoints = map (\[x, y] -> (x,y)) . 
              map (map read) . 
              filter (\xs -> length xs == 2) . 
              map (split ',') 
```

First, the points.  I would get my list of raw strings that I'd have parsed from the input and:

* Split them by `,`
* Filter out any lines that do not have two parts after the split (i.e. don't contain a `,`)
* Convert each of the strings into integers
* Convert them into tuples
* Now I've got my list of points

Onto parsing the folds.  I decide to model the folds as `Point` as well.  Essentially a fold along the x-axis would
be modelled as `(x, 0)` and along the y-axis as `(0, y)`.

```haskell
parseFolds :: [String] -> [Point]
parseFolds [] = []
parseFolds (x:xs)  
  | isPrefixOf "fold along y" x = (0, parseNum x) : parseFolds xs
  | isPrefixOf "fold along x" x = (parseNum x, 0) : parseFolds xs
  | otherwise                   = parseFolds xs

parseNum :: String -> Int
parseNum = read . tail . dropWhile (/='=')
```

I recursively parse all the lines and make a list of points (which now represent the folds).

Now we were almost finished for the first part of the puzzle.  We just needed to make the first fold:

```haskell
foldAll :: Point -> [Point] -> [Point]
foldAll f = nub . map (foldOne f)

foldOne :: Point -> Point -> Point
foldOne (0, f) (px, py) = (px, foldOne' f py)
foldOne (f, 0) (px, py) = (foldOne' f px, py)

foldOne' :: Int -> Int -> Int
foldOne' f p
  | f > p = p
  | f < p = f - (p - f)
```

To unpack the above:

- The `foldAll` functions folds all of the points in the points are around the `f` fold point.
- To do that it calls `foldOne` on each point and then deduplicates the list (using `nub`)
- The `foldOne` function uses pattern match to determine on which axis the fold happens and then
- uses the `foldOne'` helper to move the point.  
- If it is above the fold it stays the same
- If it is below the fold, it pivots

# Second part

For the second part of the puzzle, we just had to complete all the folds and then read out the code.  Reading
out the code meant translating the list of `(x, y)` coordinates into a grid like so:

```
".##..###..#..#.####.###...##..#..#.#..#"
"#..#.#..#.#..#....#.#..#.#..#.#..#.#..#"
"#..#.#..#.####...#..#..#.#....#..#.####"
"####.###..#..#..#...###..#....#..#.#..#"
"#..#.#.#..#..#.#....#....#..#.#..#.#..#"
"#..#.#..#.#..#.####.#.....##...##..#..#"
```

So how would I go about displaying my coordinates?

My intuition was to just travel each possible point, check the coordinates to see whether it is set
or not and then output it as such:

```haskell
displayAll :: [Point] -> [String]
displayAll ps = 
  let maxX = maximum $ map fst $ ps
      maxY = maximum $ map snd $ ps
      s    = S.fromList ps
  in [displayLine s maxX y | y <- [0..maxY]]
```

Starting with `displayAll`, first

* I get the maximum X and Y of the coordinates so that I didn't have to do this every time
* then I would turn my list of points into a `Set`, so I could efficiently check whether something
  is a member of it or not.
* Then I would call `displayLine` for each of the Y coordinates.

```haskell
displayLine :: S.Set Point -> Int -> Int -> String
displayLine s maxX y = [displayOne $ S.member (x, y) s | x <- [0..maxX]]
```

`displayLine` does more or less the same as `displayAll`, it uses a list comprehension to build up the
list of characters (remember in Haskell a list of `[Char]` is the same thing as a `String`).  To do this it would
call `displayOne` for each of the `(x, y)` coordinates.

```haskell
displayOne :: Bool -> Char
displayOne True  = '#'
displayOne False = '.' 
```

The only other thing remained to print each of the lines.  Of course in Haskell, we don't really have loops
and `IO` is monadic, so this looked a bit unusual:

```haskell
mapM_ print $ displayAll
            $ foldl (\ps f -> foldAll f ps) points folds
```

The `mapM_` evaluates each of a sequence and something something monads.  I googled it.  Will try to learn about it
in the future.

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day13) 

# Conclusion

In this puzzle, I had do more than display a number to get my results, it was interesting how to functionally
get a display going, though I have to admit I'm not sure whether I really get it yet.