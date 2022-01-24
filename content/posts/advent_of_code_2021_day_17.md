---
title: "Advent of code 2021: Day 17"
date: 2021-12-20T17:14:32Z
tags: ["functional","haskell","advent-of-code"]
featured_image: "/images/advent_of_code_title.jpg"
---

On day 17 of [Advent of Code 2021](https://adventofcode.com/2021), I did something that I should have really done
from the start:  Write tests first.  My excuse so far has been that I wasn't familiar with Haskell yet and other
such lame excuses.  As a software engineer, I should know better.  TDD doesn't slow me down, it makes things faster.
The last straw - if you like - was when I was writing up my exploits from [Day 16]({{< ref "advent_of_code_2021_day_16.md" >}})
and I thought there really is no excuse for not testing these parser combinators individually.  So I took Pete Anning's
[excellent notes](https://gist.github.com/peteanning/e3cb8c9aef4318cbaf46d5e1abf3d06a) and built upon it.

# TDD

Rather than GHCUP, I was using [stack](https://docs.haskellstack.org/), but all I needed to do was to `stack install hspec`
and then I was ready for testing (more on details later).  I also recommend using

```shell
$ fswatch -or *.hs | xargs -n1 -I{} stack runhaskell *Spec.hs
```

To keep the tests running in the background.  As Haskell is rather fast (compared to something like sbt), it made
red green developing a breeze.

# Artillery Game

Back to the puzzle.  The [problem on this day](https://adventofcode.com/2021/day/17) was that we're shooting a prob and 
have to hit a target area.  We assume that the prob is at coordinates `(0, 0)` and that we've got a rectangular 
target area.  The aim of the puzzle is to determine the best vector of firing the probe so that the arc of the probe 
is highest while still scoring a direct hit.

There were the following rules:

- The probe's x position increases by its x velocity.
- The probe's y position increases by its y velocity.
- The x velocity decreases by one on each step, down to a minimum of 0.
- The y velocity decreases by one on each step, with no minimum.

My first instinct was to create some types:

```haskell
type Point = (Int, Int)
type Rect = (Point, Point)
```

Then I'd start with a function to determine whether we've hit the target area:

```haskell
main :: IO ()
main = hspec $ do
  describe "isHit" $ do
    it "registers a hit" $ do
      isHit (2, 2) ((0,0), (5, 5)) `shouldBe` True

    it "registers a miss above" $ do
      isHit (2, -2) ((0,0), (5, 5)) `shouldBe` False

    it "registers a miss right" $ do
      isHit (7, 2) ((0,0), (5, 5)) `shouldBe` False

    it "registers a hit on the edge" $ do
      isHit (0, 2) ((0,0), (5, 5)) `shouldBe` True
```

Tests! Yay!  The haskell function was actually quite trivial:

```haskell
isHit :: Point -> Rect -> Bool
isHit (x, y) ((x1, y1), (x2, y2))
  | x < x1    = False
  | x > x2    = False
  | y < y1    = False
  | y > y2    = False
  | otherwise = True
```

I do like this expressiveness that comes from pattern match and destructuring!

Then I made a function to check whether a particular shot can hit the target area (this was done using tests, but
I won't show them here):

```haskell
canHit :: Rect -> Point -> Point -> Bool
canHit target@((_, y'), (x', _)) p@(x, y) (vx, vy)
  | x > x'    = False
  | y < y'    = False
  | otherwise = isHit p target || canHit target (x+vx, y+vy) (max (vx-1) 0, vy-1)
```

Now, my reasoning here was as follows:

- If the x position of the probe in flight is greater than the right edge of the target area, then the probe has passed
  the target area and is not going to turn around and fly backwards.
- Similarly, if the probe is below the target rectangle, it is no longer possible to hit it
- Otherwise, I can use `isHit` to check whether the current point is a hit, and if not, I recursively apply the
  velocity and velocity dampening.

Now, that allowed me to easily check whether a particular arc would hit.  Now, for the next step it was about 
calculating all the possible permutations of initial (x, y) speed.  Now, I'm sure there is some mathematics there
that would let us solve this without brute force, but it was late in the day and my brain wasn't working properly 
anymore to think about it too much.

```haskell
findHits :: Rect -> Point -> [Point]
findHits target start =
  let vs = [(x, y) | x <- [          1 .. maxX target],
                     y <- [minY target .. 20 * (maxX target)]]
  in filter (canHit target start) vs
```

I tried to use some common sense to restrict the search space:

- the maximum X velocity would have to be the maximum X coordinate of the target - if the velocity was higher  
  than that, the probe would pass the target area on the first step (and would never come back)
- the minimum Y velocity would have to be minimum Y coordinate. As Y always decreases, if the probe is lower than
  the target area, it would not be able to come back up.
- the minimum X and maximum Y are very suboptimal - but as the search did not take that long computationally, I figured
  the fastest way to a solution was brute force.

# Part One

For the first puzzle we had to figure out what the maximum height (y coordinate) the probe would achieve.  I figured 
that if the cannon went off with a velocity of y, and y would decrease each step, then the maximum height of a shot
would be `[y + (y-1) + ... 3 + 2 + 1]`.  We did this calculation using Gauss on [Day 7]({{< ref "advent_of_code_2021_day_7.md" >}}),
so we use it again here:

```haskell
findHighest :: [Point] -> Int
findHighest ps = 
  (\y -> (y * (y + 1)) `div` 2) $ maximum $ map snd ps
```

We just check through all the points, get the y coordinate (the `snd` second value of the tuple), and use Gauss to
get the maximum height.

# Part Two

For part two, the problem was to find the number of different initial velocity values that would lead us to hit
the target.  But as we've already done that, it was just a matter of counting the hits:

```haskell
let target = ((14, -267), (50, -225))
let start  = (0, 0)
print $ length $ findHits target start
```

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day17) 

# Conclusion

I had been postponing the introduction of tests for far too long.  I've long been a fan of TDD and felt a little
embarrassed by not having done it previously.  Especially now as I realise how straightforward it actually is in
Haskell.
