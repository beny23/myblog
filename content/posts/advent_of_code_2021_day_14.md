---
title: "Advent of code 2021: Day 14"
date: 2021-12-15T01:28:32Z
tags: ["functional","haskell","advent-of-code"]
featured_image: "/images/advent_of_code_title.jpg"
---

After [Advent of Code 2021](https://adventofcode.com/2021) transparent origami on
[Day 13]({{< ref "advent_of_code_2021_day_13.md" >}}) with more output than just a number, for Day 14 it was back 
to more standard processing with exponential complexity.  Interestingly in part 1 I initially started going down
the route of not trying to brute forcing it, got almost there but tangled myself in some logic, so I decided to 
"sod it, I'll just brute force it" only to then throw the brute force solution away again as soon as I got to part 2.

The puzzle this time was as follows:

Given an input like:

```
NNCB

CB -> H
NN -> C
NC -> B
```

On each iteration modify the template (`NNCB` in the first round), but adding a letter between each pair of letters.
So in the above example, `NNCB` becomes `NCNBCHB`, then more rules would be applied, etc.

# First part

For the first part, we had to iterate 10 times, so not an unreasonable strain on CPU and memory.

Let's start by parsing the input.  My intuition was to use a `Map` to get the pairs mapped to the `Char` that would be
inserted into each pair:

```haskell
type RuleMap = M.Map String Char

parseInsertionRule :: String -> (String, Char)
parseInsertionRule (a : b : ' ' : '-' : '>' : ' ' : c : []) = 
  ([a, b], c)

parseInsertionRules :: [String] -> RuleMap
parseInsertionRules = M.fromList . map parseInsertionRule
```

To unpack this:

- the type alias is for a `Map` that maps a `String` (pair) to a `Char` - letter to insert
- the `parseInsertionRule` function makes use of the fact that all the input uses a fixed length string, so 
  I did not have to worry about variations in the string length.  So while it is not the most elegant to
  rely on pattern match six chars individually it was uncomplicated and just worked.  Again it makes
  use of the fact that a Haskell `String` is also `[Char]` list.
- the `parseInsertionRules` function, just combines mapping each string to an insertion rule with calling `fromList`
  which creates a `Map` from a list.

Then I could define a function which turned a template into the next generation by applying the various rules:

```haskell
nextGen :: RuleMap -> String -> String
nextGen rm [b] = [b]
nextGen rm (a : b : ss) = a : (rm ! [a, b]) : (nextGen rm (b : ss))
```

`nextGen` just uses pattern matching to get the first two characters, insert the character as per `RuleMap` - 
`rm ! [a, b]` just looks up the new character in the map and then recursively appends the rest of the string.

For running the iterations 10 times, I used a neat pattern I found on google:

```haskell
polymer = iterate (nextGen ruleMap) template !! 10
```

The `iterate` function calls the specified function `(nextGen ruleMap)` on the input `template` (which is that initial 
string `NNCB` in the example) and then calls it on the result.  It keeps going infinitely and then I want the
10th call.  (Haskell's lazy evaluation ensures that no more than necessary is actually called).

This also shows off how neat Haskell's currying works.  If I have a function `x :: Int -> Int -> Int` that takes two `Int`
and returns an `Int`, I can create a new function by "currying the first parameter":  `(x 5)` is a new function 
`x :: Int -> Int` that takes a single `Int` and produces an `Int`.  This is allows for function composition and really
concise code.  In the above example, I could have written:

```haskell
polymer = iterate (\x -> nextGen ruleMap x) template !! 10
```

(where `\` is the lambda notation in Haskell), but it doesn't add to the readability here.

To get the solution, we had to subtract the number of occurrences of the most common letter with the number of occurrences 
of the least common letter. 

```haskell
freq = sort $ map length $ group $ sort polymer
(head $ reverse freq) - (head freq)
```

I'd get the frequency list of the characters in the final `polymer` - and then sort that list.  Then I can get the
first and the last occurrence for the solution.

# Second part

For the second part, it was just the same as part 1, but doing 40 iterations.

Easier said than done.  Considering we start with 4 letters, and after each iteration we roughly double the size of the
string, we'd end up with a string length of `2^42` - assuming a character length of 1 byte, that's more than 2 PB (Petabytes).
Don't think we're going to brute force this.  I used `Ctrl-C` to stop my attempt after about 20 seconds of foolish hope
against hope.

Back to plan A.  My initial plan was to create a Map of pairs and the number of occurrences.  And have the rules be
a map of `String` to `[String]` - i.e. return the new pairs.  So if `NN -> B` that means that for `NN`, we would create
`['NB', 'BN']`.  If we then maintain a separate map that contains the occurrences of each of these pairs, then when creating
new pairs, we can just take the number of pairs with us.  Which at later iterations comes in handy, when we change 100,000
`NN` pairs to 100,000 each of `NB` and `BN`.

I adjusted the types a bit and the `RuleMap` parsing:

```haskell
type RuleMap = M.Map String [String]
type PairMap = M.Map String Int

parseInsertionRule :: String -> (String, [String])
parseInsertionRule (a : b : ' ' : '-' : '>' : ' ' : c : []) = ([a, b], [[a, c], [c, b]])
```

So instead if a `Map` that stored the character that would go in between a pair, I created a mapping of how
each pair would be transformed.

Then for the processing, it was:

```haskell
nextGen' :: RuleMap -> [(String, Int)] -> [(String, Int)]
nextGen' rules pairs = do
  (pair, count) <- pairs
  nextPair      <- rules ! pair
  [(nextPair, count)]
```

To unpack this:

- Starting with the `nextGen'` helper function, I am basically transforming a list of pairs and occurrences
  into another list of pairs and occurrences.  I find that the `do` notation is very neat here.  What this does:
- For every element in the `pairs` list, it deconstructs the tuple into the components of the `pair` name and `count`
- Then it looks up the corresponding list of pairs from the `rules` map
- And then it builds a new list of tuples with the `nextPair` and `count`.

To make this clear, I'll try walking through an example.  Assuming that we've got a rule map like the one above

```
CB -> H
NN -> C
NC -> B
```

We've turned this into a map like this

```
CB -> [CH, HB]
NN -> [NC, CN]
NC -> [NB, BC]
```

Then, if we had an input like this `[(CB, 1), (NN, 2)]`, then we the above code would create a new list
`[(CH, 1), (HB, 1), (NC, 2), (CN, 2)]`.  Probably the best analogy to the Haskell do notation (which uses Monads but
I won't go into this here), is a Scala for comprehension.  Same principles apply.

Then I can use the `nextGen'` helper to build:

```haskell
nextGen :: RuleMap -> PairMap -> PairMap
nextGen rules pairs =
  foldl (M.unionWith (+)) M.empty $ map (uncurry M.singleton)
                                  $ nextGen' rules 
                                  $ M.toList pairs
```

Instead of like `nextGen'` which works on lists of pairs, this works on a `Map String Int`.  The idea here is that
if the processing brings out duplicates, we would be using `unionWith` to combine maps but adding their values together.
So if I had `fromList [(CB, 1), (NN, 2)]` and `fromList [(CB, 3), (NC, 1)]`, the `union` would create
`fromList [(CB, 4), (NN, 2), (NC, 1)]`.

So now that we've got pairs and their occurrences, we can combine them to count the letters.  The idea was that now
we've got the pairs, I would look at the first letter of each pair and count that.

To take this onto the small scale, if we look at `NNCB`, there are three pairs (`NN`, `NC`, `CB`) and if we look at the
first letter of each pair AND add the very last letter, then we get back to `NNCB`.  So similarly, when we count up
all the pairs and occurrences, we can use this logic (first letter of each pair, plus 1 occurrence of the last letter)
to create a count without needed exponential computing power and memory.

To do the counting:

```haskell
lastLetter = M.singleton (head $ reverse template) 1
freq = sort $ map snd 
            $ M.toList
            $ foldl (M.unionWith (+)) lastLetter 
            $ map (\(a, b) -> M.singleton (head a) b)
            $ M.toList polymer
(head $ reverse freq) - (head freq)
```

Unpacking this again:

- starting from the right of the `$`
- we turn the map back into a list
- we translate every pair into a singleton map with just the first letter
- then we can add them all together with (`unionWith (+)`)
- turn that into a list
- only look at the number of occurrences
- sort it
- take the top and bottom
- subtract them

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day14) 

# Conclusion

This was a really interesting one to try to avoid doing exponential work.  I was quite pleased that I was on the
right track even in part 1, before I abandoned that approach and went for brute force.  I was even pleased with the
brute force as it turned out very concise.  I was also pleased with using the `do` notation to good effect, though
it still feels a bit like it's too simple.