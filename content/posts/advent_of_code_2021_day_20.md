---
title: "Advent of code 2021: Day 20"
date: 2022-01-06T22:14:32Z
category: ["functional","haskell","advent-of-code"]
---

![](/images/advent_of_code_title.jpg)

Hmpf.  It's now January and I'm still doing Advent.  Still, I was determine to push ahead.  Then I got stuck good and
proper on Day 19 - though to be fair the whole Log4shell dumpster fire was taking up a lot of time, so I decided to
skip it for now.
Day 20 of [Advent of Code 2021](https://adventofcode.com/2021) was all about transforming images.  What made it
slightly more complicated was the fact that the images were nominally of infinite size.

# The problem

According to [the story](https://adventofcode.com/2021/day/20), there is an image that needs to be enhanced.  Each
pixel can either be a light pixel (`#`) or a dark (`.`).  For the image transformation process, each of the neighbours
of a pixel is transformed into bits. For example, in the following image:

```
# . . # .
#[. . .].
#[# . .]#
.[. # .].
. . # # #
```

The central pixel is surrounded by 

```
[. . .]
[# . .]
[. # .]
```

These nine pixels are turned into the binary number 000100010 (34 in decimal).  That is then used as the index to look
up the new value.

# The gotcha

The complication with this problem statement is that the images are infinite, so there was an infinite number
of pixels to consider.  And that's clearly not going to scale...

The bit that makes this simpler is that those infinite pixels all start out as dark pixels:

```
[. . .]
[. . .]
[. . .]
```

This turns into 000000000 = 0.  On the example input, the lookup value for index 0 was another dark pixel, so a tile
of dark pixels stays unchanged.  On the puzzle input, the lookup value for index 0 was a light pixel though.  This meant
that the all the "infinite" pixels would change from dark to light.

```
[# # #]
[# # #]
[# # #]
```

Thankfully, the lookup value for index 111111111 = 511 was a dark pixel.  So for every odd number of iterations, the 
infinite pixels would all turn light, for every even number of iterations, the pixels would turn dark.

# Show me the codez - Part One

So first thing I did was to translate the input:

```haskell
parseLine :: String -> [Int]
parseLine = map parseChar

parseChar :: Char -> Int
parseChar '.' = 0
parseChar '#' = 1
```

This would turn `"...##.."` into `[0,0,0,1,1,0,0]`.  This I could use to decode the lookup table for the "image
enhancement" as well as parsing the initial image.  The data structure I chose for the image was a list of lists of ints 
`[[Int]]` - this served me pretty well in part 1 for [Day 8]({{< ref "advent_of_code_2021_day_8.md" >}}).

For part one of the problem, I had to determine how many
pixels (including the infinite ones) would be light after 2 iterations.  That was good, because it meant the infinite
pixels would all turn light and then dark again.  This meant I could ignore infinity.  I'd just have to add a border
around the input image to ensure that any infinite pixels that "touch" the image are actually processed.

Here's the code for adding a border:

```haskell
makeBorder :: Int -> [Int]
makeBorder n = replicate n 0

addMargin :: Int -> [Int] -> [Int]
addMargin n xs =
  let border = makeBorder n
  in border ++ xs ++ border

addBorder :: Int -> [[Int]] -> [[Int]]
addBorder n xs@(x:_) =
  let topBottom = replicate n $ makeBorder $ length x
      padded = topBottom ++ xs ++ topBottom
  in map (addMargin n) padded
```

This would add `n` rows and columns of 0s to the left, top, right and bottom of the input image.

Then I would process the image recursively in lines of threes.  First we'd have a function that picks out the top
three lines and then processes the columns:

```haskell
processLines :: [Int] -> [[Int]] -> [[Int]]
processLines algo (prev:curr:next:lines) = 
  (processLine algo prev curr next) 
    : (processLines algo (curr:next:lines))
processLines _ _ = []
```

The columns are processed be destructuring the input, so we've got the nine pixels in view, then using `algo !!` to 
lookup the transformed value (index-based lookup) and then recursively processing the next "window":

```haskell
processLine :: [Int] -> [Int] -> [Int] -> [Int] -> [Int]
processLine algo 
           (p1:p2:p3:prev)
           (c1:c2:c3:curr)
           (n1:n2:n3:next) =
  (algo !! bin2dec [p1, p2, p3, c1, c2, c3, n1, n2, n3]) 
   : processLine algo (p2:p3:prev) (c2:c3:curr) (n2:n3:next)
processLine _ _ _ _ = []
```

I think this is quite neat, because using immutable lists I don't need to worry about changing something that I need
in a later calculations.

# Part Two

For part 2 of the puzzle, instead of doing 2 iterations of the "image processing" we were to do 50.  As 50 was an
even number, we could again "ignore" the infinite pixels.

Iterating the same operation is pretty simple in Haskell:

```haskell
let images = iterate (processLines algo) image
print $ length $ filter (==1) $ concat $ images !! 50
```

To unpack this:

- `iterate` calls the (curried) function `processLines algo` on image, and then keeps calling it when the results
  are in a list.  This list is infinite, but as Haskell is lazy it only computes it when it is used.  And that's
  what happens in
- `images !! 50` picks the results of the 50th iteration.  
- Then it concatenates the list of lists into one long list
- Then it filters that to lists where the pixel is light (`filter (==1)`)
- And counts the pixels.

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day20) 

# Conclusion

I wasn't really happy with this solution, as I endes up doing a bit of trial and error.  How large would
the added border of "infinite pixels" have to be to be, so that I could still make my calculations.  All said
and done the calculations lasted 28 seconds, which feels rather inefficient.  I'm looking forward to checking
other people's solutions to see what could be improved on an algorithmic level.  And maybe in the future I have
to get back to these challenges to see how Haskell can be optimised here - sure that will be interesting.
