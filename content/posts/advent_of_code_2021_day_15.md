---
title: "Advent of code 2021: Day 15"
date: 2021-12-19T17:28:32Z
tags: ["functional","haskell","advent-of-code"]
featured_image: "/images/advent_of_code_title.jpg"
---

[Advent of Code 2021](https://adventofcode.com/2021) day 15 took us back to the 2-dimensional
maps we first encountered on [Day 11]({{< ref "advent_of_code_2021_day_11.md" >}}) and
[Day 9]({{< ref "advent_of_code_2021_day_9.md" >}}).  We had to parse a map again, and this
time we had to find the shortest path from the top left to the bottom right.  This was the first
time when a homespun algorithm just did not cut the mustard, and I had to wrack my brain
(i.e. use Google) to do a bit of algorithm research.  Though I made sure not to peek at any
haskell implementations. That would've been cheating.

# First Try - Epic Fail

So the backstory for this puzzle was in order for the Santa submarine to escape a cave, we had
to navigate a map and find the least risky path.  The map would be represented as such:

```
116
138
213
```

To get from the top left to the bottom right, we would want to traverse the map with the least risk
where risk is the summed value up of every field we pass.  The lowest risk of this would be along the
left and bottom edge: 1+1+2+1+3=9.

So I figured how hard can it be and turned the list of strings into a map of (x, y) coordinates mapped
to the risk value.  Then add the risk value of this point with the minimum of the neighbours, and just
recursively go through the list (taking care of not visiting points I'd visited before).

And then I tried it on the example output.  And waited. And waited.  That wasn't going to work.  I was
not even finished with part one.  Thinking about the algorithm because I was going to check the neighbours and the
neighbours were going to check their neighbours, there's going to be a lot of duplicate effort.  Exponentially more
effort.  And as soon as exponential enters the frame, it's time to rethink!

# Second Try - Somewhat Fail

So I did a bit of research (basically entered "Shortest Path" and "Algorithm" into Google) and up popped 
good old [Dijkstra](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm).

The pseudo code for Dijkstra's algorithm is:

```
Set all distances to Infinity
Except the first point (which is 0)
Start at first point
Loop
  Update the distances of all neighbours
  Find the nearest node not yet visited 
End Loop
```

So that's what I looked at doing:

First, let's define some types:

```haskell
type Point = (Int, Int)
type RiskMap = M.Map Point Int

type PQueue a = [(a, Int)]
type RiskQueue = PQueue Point

type DistMap = M.Map Point Int
```

The idea was that I'd make a priority queue which would hold the coordinate and combined distance from the source.
By implementing a priority queue, the item with the lowest distance would always be at the top.

Here was my stab at a priority queue:

```haskell
pqPush :: (a, Int) -> PQueue a -> PQueue a
pqPush e [] = [e]
pqPush e@(_, pri) qs@(q@(_, pri') : qs') 
  | pri < pri' = e : qs
  | otherwise  = q : pqPush e qs'
```

The "pqPush" operation would start at the beginning, and move the the next part of the list until it finds
an item with higher priority.  At which point this would insert the record.  I find the pattern matching with the
`@` particularly helpful there.  To look at the following in detail:

```haskell
pqPush e@(_, pri) qs@(q@(_, pri') : qs')
```

That looks a bit of a handful!  But essentially it's just two parameters:

- `(a, Int)` is a tuple of a generic type `a` and an integer.
- `PQueue a` is our priority queue with generic type a.  Looking at the type definition of `PQueue` shows
  that this is just `[(a, Int)]` - a list of the same tuple.

So looking at the first parameter, we're destructuring the tuple `(a, Int)` into `e@(_, pri)`, which means

- `e` specifies which contains the whole tuple
- `_` means we don't care about the first item in the tuple
- `pri` holds the value of the priority.

For the second parameter, it gets a little more involved, but that's what makes this destructuring notation so powerful
(I got to know this in Scala and now wince a little every time I use a language where that's not available).  So 
for `qs@(q@(_, pri') : qs')` we have

- a list `qs` which is the whole parameter
- the first element of the list (the head of the list) `q`
- the priority `pri'`
- and the tail of the list `qs'`

Using the destructured parameters, adding the element `e` to the list in priority order is easy:

```haskell
  | pri < pri' = e : qs
  | otherwise  = q : pqPush e qs'
```

- If the priority value `pri` is less than the priority at the head of the list (`pri'`), then we insert the element
  before the whole list.
- Otherwise, we call `pqPush` recursively with the tail of the list and insert the element that way.  This feels
  inefficient coming from an imperative programming background, but as no new objects are created for the tail, the
  recursive processing of each element is quite performant (especially as Haskell tends to optimise this using tail
  recursion automatically)

In addition, I implemented `pqRemove` and `pqUpdate` to update priority of an item and to remove an item from the head
of the list.

With that priority queue, I was able to implement Djikstra's shortest path algorithm:

```haskell
neighbours :: RiskMap -> Point -> [Point]
neighbours risks (x, y) = 
  filter (\p -> M.member p risks) [(x-1, y), (x, y-1), (x+1, y), (x, y+1)]

distances :: RiskMap -> DistMap -> Int -> [Point] -> [(Point, Int)]
distances risks dists d ns = 
  map (\p -> (p, min (dists ! p) (d + (risks ! p)))) ns

step :: RiskMap -> DistMap -> RiskQueue -> DistMap
step risks dists [] = dists
step risks dists ((p, d):qs) = 
  let ns = neighbours risks p
      ds = distances risks dists d ns
      dists' = M.union (M.fromList ds) dists
      qs' = foldl pqUpdate qs ds
      risks' = M.delete p risks
  in step risks' dists' qs'
```

Unpacking what's going on there:

- `neighbours` returns any neighbours of a point `(x, y)` but it has to be on the risk map.  This automatically  
  ensures that points at the edge of the map are not returned.
- the `distances` function would take a list of points and returns updated distances based on the existing distances
  or the distances based on the risk map
- both of those return the new distances for the neighbours
- that is then used to update the distance map - I used `M.union`) to merge the new distances with the existing ones 
  creating `dists'`
- then we update the priority list to update the new distances too
- then we remove the existing point from the risk map - this prevents them from being visited again
- finally, we call `step` recursively until all elements on the queue are done

Once all the points have been calculated, I can just lookup the distance on the final element and I'd have my answer for
part one.

So far so good!

# Third Try - Success

For the second part, it turns out that my algorithm would have to be more efficient again.  It turns out the size
of our input was not big enough.  Really the input was 25 times larger, whereby the original input would be repeated
(and increased) into 5x5 tiles.

As I had already transformed the 2d array into a list of coordinate tuples, making the tiles would just be a matter
of transforming the original list 25 times.  First my function for transforming:

```haskell
transform :: [(Point, Int)] -> Point -> Point -> [(Point, Int)]
transform [] _ _ = []
transform (((x,y),r):ps) p@(x',y') d@(w, h) 
  = ((x+x'*w,y+y'*h), ((r+x'+y'-1) `mod` 9) + 1) : transform ps p d
```

Unpacking this function:

- It takes a list of points and their associated values
- It also takes a transformation point (i.e. where on the 5x5 grid should new point by - from `(0, 0)` to `(4, 4)`)
- And I pass in the dimensions (`d`), the width (`w`) and height (`h`) of the original grid.
- To "move", the position is adjusted as follows `(x+x'*w, y+y'*h)`
- and the risk value as such `((r+x'+y'-1) ``mod`` 9) + 1` - this ensures that the value is increased but rolls over
  if it passes 9 as per requirements.

Then I can use that to transform the whole input:

```haskell
makeTransforms = [(x,y) | x <- [0..4], y <- [0..4]]

makeGrid :: [(Point, Int)] -> [(Point, Int)]
makeGrid points = 
  let dim = maximum $ map fst $ points
      ts = makeTransforms
  in concat $ map (\t -> transform points t dim) ts
```

The `makeTransforms` just creates a list like so:

```haskell
[(0,0),(0,1),(0,2),(0,3),(0,4),(1,0),(1,1),...,(4,4)] 
```

The original list is then transformed 25 times (the transform at `(0, 0)` does not change the original list), and the
resulting list of lists is then turned into a single list containing all the points.

As it turns out the list is a bit big.  And my previous algorithm takes a long long time to run (I interrupted it 
after 10 minutes, so I don't actually know how long!)

But I figured as I was using an algorithm that should be known to every Computer Science degree, it was unlikely that
the problem was with the algorithm, probably my implementation was a bit shoddy.

My first thought of where the problem could be was here:

```haskell
pqUpdate :: Eq a => PQueue a -> (a, Int) -> PQueue a
pqUpdate qs q@(e, pri) = pqPush q $ pqRemove e qs
```

In order to update all the items in my priority queue, I'd traverse it to remove the item, only then to traverse it
again to push the item back in with the new priority.  Considering that I would do this for every neighbour on ever
point I'm processing I put it down as something to fix.

Here's what I changed:

- Instead of initialising my priority queue with all the elements, so I'd have to update items again and again, I'd
  just start with the items being processed.  That way it keeps the list small. I'd also use a set to keep track
  of the points already visited.

```haskell
step :: Point -> RiskMap -> DistMap -> VisitedSet -> RiskQueue -> DistMap
step dim risks dists visited [] = dists
step dim risks dists visited ((p, d):qs) = 
  let ns = neighbours visited dim p
      ds = distances risks dists d ns
      dists' = M.union (M.fromList ds) dists
      qs' = foldl (\qs q -> pqPush q qs) qs ds
      visited' = S.insert p visited
  in step dim risks dists' visited' qs'
```

- Instead of relying on the risk map to check whether a point is on or off the grid, I'd pass the dimensions to
  the `neighbours` function and use that to filter off-grid coordinates.

```haskell
neighbours :: VisitedSet -> Point -> Point -> [Point]
neighbours visited d (x, y) = filter (\p -> S.notMember p visited) $ filter (onBoard d) [(x-1, y), (x, y-1), (x+1, y), (x, y+1)]

onBoard :: Point -> Point -> Bool
onBoard (w, h) (x, y)
  | x < 1     = False
  | y < 1     = False
  | x > w     = False
  | y > h     = False
  | otherwise = True
```

- Instead of updating any point regardless of whether it had changed, I'd only update points where the distance had 
  changed 

```
distances :: RiskMap -> DistMap -> Int -> [Point] -> [(Point, Int)]
distances risks dists d ns = ns >>= (distance risks dists d)

distance :: RiskMap -> DistMap -> Int -> Point -> [(Point, Int)]
distance risks dists d p = distance' p (dists ! p) (d + (risks ! p))

distance' :: Point -> Int -> Int -> [(Point, Int)]
distance' p d d' 
  | d' < d    = [(p, d')]
  | otherwise = []
```

What I did there was to use the bind operator `>>=` to change the list, so that if the distance does not change, 
`distance'` returns an empty list, which means the item currently being processed is removed from the list.  This
is the behaviour of the monadic bind.  Previously I've grown to really like it as the `flatMap` function in Scala.

Once all those change were in, my algorithm ran on the extended size part two input in about 90 seconds.  At that point
I stopped trying to make further optimisations.  If I had, I might have looked into binary heaps or tree data structures
for the priority map.

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day15) 

# Conclusion

I loved this puzzle, as it meant having to reach further into the algorithm toolbox and pure intuition was not
good enough.  I found it really interesting optimising the second part to ensure that my algorithm implementation
was as good as the algorithm itself...
