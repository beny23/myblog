---
title: "Advent of code 2021: Day 18"
date: 2021-12-29T02:14:32Z
category: ["functional","haskell","advent-of-code"]
---

![](/images/advent_of_code_title.jpg)

Day 18 of [Advent of Code 2021](https://adventofcode.com/2021) took rather a long time to get right.  Half of
the problem was that there were a couple of gotchas in the requirements and the choice of data structure hugely
impacted how easy it was to come up with a solution.  Just like [Day 17]({{< ref "advent_of_code_2021_day_17.md" >}})
I was using TDD and this helped me root out some blind alleys nicely.

# The problem

[The story](https://adventofcode.com/2021/day/18) was roughly as follows: In order to continue on the Advent of Code
submarine journey, we had to solve snailfish number problems.  The issue with snailfish numbers is that they're unlike
other numbers, and are built using number pairs:

```
[1,2]
[[1,2],3]
[9,[8,7]]
[[1,9],[8,5]]
```

Pairs can have pairs nested inside them.  To add snailfish numbers, the following rules had to be followed:

- If any single number is greater than or equal to 10, it is replaced by a pair of numbers. This is called a *split*.
- If any pair is nested more than 4 levels deep, then the pair is added to neighbouring numbers and replaced with a 
  single `0`. This is called *exploding*.

# First thoughts

My initial thought was that having pairs and nested pairs sounds exactly like a tree.  So I started building a parser
to read in the numbers and build a tree structure.

First, the data type:

```haskell
data Elem = Lit Int | Pair Elem Elem deriving (Eq, Show)
```

So an element is either a `Lit` literal with an integer value or a `Pair` of elements.  Building on my experience
with parsing on [Day 16]({{< ref "advent_of_code_2021_day_16.md" >}}), turning the raw strings into a tree felt easy:

```haskell
p_lit :: Parsec String () Elem
p_lit = do
  num <- read <$> many1 digit
  return (Lit num)

p_elem :: Parsec String () Elem
p_elem = p_pair <|> p_lit

p_pair :: Parsec String () Elem
p_pair = do
  char '['
  e1 <- p_elem
  char ','
  e2 <- p_elem
  char ']'
  return (Pair e1 e2)
```

This would give me a tree as this test demonstrates:

```haskell
it "parses a nested pair" $ do
  parse p_pair "" "[1,[2,3]]" 
    `shouldBe` Right (Pair (Lit 1) (Pair (Lit 2) (Lit 3)))
```

My next step was going to be to implement the split and explode functions.  And that's where I hit my first issue.
Split was going to be straightforward enough, I would traverse my tree and just replace any `Lit` that has a value
greater than or equal to 10 and replace it with a pair.  But on the exploding function, it wouldn't be so 
straightforward.  In order to explode, we had to:

- Replace the pair with a `Lit 0`
- Add the left element of the pair to the first occurring `Lit` that is to the right in the tree (can be on the
  same or a different level)
- Same with the right element, that gets pushed right.

The tree structure wasn't going to work for me there.  In an imperative language where we'd have references to different
objects, it would have been possible to go backwards and forwards in the tree and mutate the values in-place.  But 
in Haskell this wouldn't be so simple as the tree is immutable.

Eventually - after attempting some solutions with keeping state and pushing values left and right, I had a solution
that was not very elegant and frankly smelled a bit.  I was prepared to let that go to get my answers in, but as it
turned out my unit tests uncovered that the exploding wasn't working like the examples.

# Back to the drawing board

Eventually I figured that the tree was not the right data structure.  In order to push the exploding numbers left and
right, something like a list felt more natural.  Eventually - after a lot of trying different approaches - I settled on 
a data structure where I'd use the "path" from the root of the tree down to the leaf.

So if I had a (crudely drawn) tree as such

```
      Pair
    /      \
  Lit 1   Pair
         /   \
      Lit 2  Lit 3 
```

Then for each `Pair`, I would record whether I take the left or right branch by a 0 or 1.  So the above tree
would be represented by a list

```haskell
[("0", 1), ("10", 2), ("11", 3)]
```

I'm only listing the leaves of the tree, not the internal nodes.  To convert from my tree to this list, I used
the following

```haskell
tree2list :: String -> Elem -> ElemList
tree2list path (Lit x) = [(path, x)]
tree2list path (Pair l r) = 
  (tree2list (path ++ "0") l) ++ 
  (tree2list (path ++ "1") r)
```

Now I had my data structure right, the split operation was quite straightforward:

```haskell
split :: ElemList -> ElemList
split [] = []
split (e@(p,x):es)
  | x >= 10   = (p ++ "0", x `div` 2) : 
                (p ++ "1", (x+1) `div` 2) : es
  | otherwise = e : split es
```

This operation recursively goes through each of the elements in the list until it finds the first node where
the value is greater or equal 10.  If that's the case, it replaces that single element by a pair (by adding 0 and 1
to the "path").

The explode function was a little more complicated, first I built a helper to check whether two paths are for
elements that would explode:

```haskell
isExplodingPair :: String -> String -> Bool
isExplodingPair p1 p2
  | length p1 /= length p2 = False
  | length p1 < 5          = False
  | otherwise              = (stem p1) == (stem p2)

stem :: String -> String
stem p = take ((length p) - 1) p
```

That can then be plugged into:

```haskell
explode :: ElemList -> ElemList
explode es = explode' [] es

explode' :: ElemList -> ElemList -> ElemList
explode' es' [e'] = reverse $ e':es'
explode' es' (e'@(p1, x1) : e@(p2, x2) : es)
  | isExplodingPair p1 p2 = 
       (reverse $ addToFirst x1 $ es') ++ 
       [(stem p1, 0)] ++ 
       (addToFirst x2 es)
  | otherwise = 
       explode' (e':es') (e:es)

addToFirst :: Int -> ElemList -> ElemList
addToFirst _ [] = []
addToFirst x' ((p, x):es) = (p, x+x'):es
```

The main logic is in the `explode'` function.  It works as follows:

- It has two parameters: a list of elements that have already been visited and a list that are still to be visited.
- It then recursively goes through the list, picking out the first two elements.
- If those two elements are to explode, it uses the `addToFirst` helper to add the left element of the pair (`x1`) to
  the last element that was visited (as it is more efficient to add to lists at the front than append at the back,
  I am reversing the list of already visited nodes) 
- The right element (`x2`) is added to the first element of the list of not yet processed nodes
- The two elements are replaced by a single value.

Initially, when I was writing this I was playing with a "findExplodingPair" function that would get me the position
of the first exploding pair, then I was going to have a second pass to add the left value to the first element whose
path was smaller than the found path, and a third pass... I didn't like it, so I was quite pleased when I managed to
turn that into a single block.

# One more gotcha

I had implemented the above and still my tests were not passing.  Eventually, I figured out that I needed to read the
instructions again:

> To reduce a snailfish number, you must repeatedly do the first action in this list that applies to the snailfish number:
> - If any pair is nested inside four pairs, the leftmost such pair explodes.
> - If any regular number is 10 or greater, the leftmost such regular number splits.

My initial reading of this was to implement:

```haskell
repeatUntil :: Eq a => (a -> a) -> a -> a
repeatUntil f e = repeatUntil' f e $ f e

repeatUntil' :: Eq a => (a -> a) -> a -> a -> a
repeatUntil' f e1 e2
  | e1 == e2  = e1
  | otherwise = repeatUntil f e2

reduce :: ElemList -> ElemList
reduce el = repeatUntil (repeatUntil split . repeatUntil explode) el
```

The `repeatUntil` helper takes two things of the same type and a function.  If the two inputs are the same, then
we're finished and return it.  If they are not the same, it would call the function and then itself.  In Java
this would be a while loop.  I did like the function composition there that allowed me to call `explode` until 
the input didn't change anymore and then `split`.

That's where I had a subtle bug though.  I was calling `split` until I couldn't split anymore.  That resulted in
failing tests.  When I changed it to

```haskell
reduce el = repeatUntil (split . repeatUntil explode) el
```

it started working!

# Magnitude

Finally, to be able to enter the solution into the AOC website, we had to calculate the magnitude of a snailfish number.
The magnitude was defined as 

> The magnitude of a pair is 3 times the magnitude of its left element plus 2 times the magnitude of its 
> right element. The magnitude of a regular number is just that number.

Now this would be easy if we were using our tree structure:

```haskell
magnitude :: Elem -> Int
magnitude (Lit x) = x
magnitude (Pair l r) = (3 * magnitude l) + (2 * magnitude r)
```

So I wrote a further function to convert from my element list, back to my tree:

```haskell
list2tree :: String -> ElemList -> Elem
list2tree p es = 
  maybe (Pair (list2tree (p ++ "0") es) 
              (list2tree (p ++ "1") es)) 
        Lit 
        (lookup p es)
```

The `list2tree` function is:
- called with the empty string initially, 
- then it tries to lookup the value of that path
- if that exists it is turned into a `Lit` node
- if it does not exist, then we must be at a `Pair` node and 
- list2tree gets called recursively to fill in either branch

# Part One

Finally, I was able to find the magnitude of adding all the numbers together!

# Part Two

For part two, we needed to find the highest magnitude when adding each pair of numbers together.
To get all the permutations of numbers I use a list comprehension:

```haskell
pairs = [(x1, x2) | x1 <- numbers, x2 <- numbers, x1 < x2]
```

That worked by combining all the numbers and only picking out the ones where the first number is smaller than the
second.  Note, this is working on my element list - as Haskell lists are automatically comparable. Neat!

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day18) 

# Conclusion

This was a very interesting challenge as it demonstrated something I picked up a long time ago, if something does
not feel elegant, then there's a better solution somewhere.  Call it intuition or experience, but it is often
very useful to keep tinkering if something is not as simple as it can be.  Note also, I'm sure there's lots of ways 
this could still be simplified further, I just don't know Haskell well enough yet.