---
title: "Advent of code 2021: Day 16"
date: 2021-12-20T00:14:32Z
tags: ["functional","haskell","advent-of-code"]
featured_image: "/images/advent_of_code_title.jpg"
---

For day 16 [Advent of Code 2021](https://adventofcode.com/2021), it was all about
monadic parser combinators (whatever they are)!  Just like [Day 15]({{< ref "advent_of_code_2021_day_15.md" >}}), 
this one took me a lot longer to complete than I had wanted, this time it was all about learning
Haskell's approach to [parsing text](https://hackage.haskell.org/package/parsec). 

According to the [story](https://adventofcode.com/2021/day/16), we had to decode a message in a custom
binary format, the Buoyancy Interchange Transmission System (BITS).  BITS is a message format that
encodes both literal values and operations.

# Building the parser

The first part of the problem was to decode the hexadecimal input to a stream of bits. I felt I cheated a bit 
here, because I just went for simplicity:

```haskell
hex2bin :: String -> String
hex2bin s = s >>= hex2bin'

hex2bin' :: Char -> String
hex2bin' '0' = "0000"
hex2bin' '1' = "0001"
hex2bin' '2' = "0010"
hex2bin' '3' = "0011"
hex2bin' '4' = "0100"
hex2bin' '5' = "0101"
hex2bin' '6' = "0110"
hex2bin' '7' = "0111"
hex2bin' '8' = "1000"
hex2bin' '9' = "1001"
hex2bin' 'A' = "1010"
hex2bin' 'B' = "1011"
hex2bin' 'C' = "1100"
hex2bin' 'D' = "1101"
hex2bin' 'E' = "1110"
hex2bin' 'F' = "1111"
```

So this would just take a list of characters (`0 - F`) with the nibbles they represent.  The bind operator (think 
`flatMap` in scala) is just the ticket there. 

Now that I had to parse the input, I turned to monadic parser combinators.  That sounds complicated, but is actually
making parsing very easy (once I had understood the concept).  Essentially, it allows you to build little parsing
building blocks and because these parsing blocks are monads, they can be combined with monadic operators.  I'm going
to try to explain by building up the blocks.

First of all, we have the basic element of the parser, we're looking at parsing 0s and 1s:

```haskell
p_bin :: Parsec String () Char
p_bin = oneOf "01"
```

This will parse either a `0` or a `1`. To test it:

```haskell
*Main> parse p_bin "error msg" "0101"
Right '0'
*Main> parse p_bin "error msg" "23"
Left "error msg" (line 1, column 1):
unexpected "2"
```

The output of the parser is an `Either` - which is `Left` for an error condition or `Right` for a result.

This basic building block can then be used to build up more complex parsers:

```haskell
p_ver :: Parsec String () String
p_ver = count 3 p_bin

p_lit :: Parsec String () Packet
p_lit = do
  ver <- p_ver
  string "100"
  gs <- many $ char '1' >> count 4 p_bin
  lg <- char '0' >> count 4 p_bin
  return (Lit (bin2dec ver) (bin2dec ((concat gs) ++ lg))) 
```

To define the first type of packet - we've got a 3-bit field for the "version", and another 3-bit field which is always
set to 4 ("100").  Then a series of 5-bit fields.  First there are many of those fields starting with `'1'` followed
by a payload of a 4-bit nibble.  Finally, there's 5-bit field starting with `'0'` and another 4-bit nibble.  All these
payload fields are combined and then turn into a numeric literal.

As a way of an example, when parsing the string

```
0011001110001010
VVVTTTAAAAABBBBB
```

We've got the version (V), type (T) and two groups (A and B):

```haskell
*Main> parse p_lit "error msg" "0011001110001010"
Right (Lit 1 202)
```

Before I continue on the other packets, I'd just like to take a detour on how I've modelled the packets in Haskell:

```haskell
data Packet = Op Int Int [Packet] | Lit Int Int 
  deriving (Eq, Show)
```

Unpacking this:

- This defines a `Packet` data structure which can either be
- a `Lit` literal, which has a version and a value (both integers)
- an `Op` operator, which has a version, a type and a list of packets nested within it
- the `deriving (Eq, Show)` implements the ability to print or compare the data

Now we just need to parse the operator packets - but it is not as simple as that.  Operator packets
can have other subpackets inside them.  To determine how many packets there are, they can be specified either by the
number of subpackets, or the length (in bits) of all the subpackets.  To check which is which, there is a bit that
indicates this.  If the first bit is `'1'`, then the following 11 bits specify the number of packets:

```haskell
p_op_by_num :: Parsec String () [Packet]
p_op_by_num = do
  len <- try (char '1') >> count 11 p_bin
  ps <- count (bin2dec len) p_packet
  return ps
```

The Haskell do notation is really useful here (this is the equivalent of the scala for-comprehension), and essentially
makes monadic binds easier to read.  To explain the above a bit further:

- the parser tries to read the character `1` - if that is not present, then `try` ensure that the character is not 
  consumed.
- following that (by using the `>>` combinator), it parses 11 binary characters and puts the output into `len`
- then it parses `len` packets using the `p_packet` parser (see further down)

For the other type of operator packet parsing - where the first bit is `'0'`, then the following 15 bits specify the
length of the payload.

```haskell
p_position :: Int -> Parsec String () [a]
p_position n = do
  pos <- getPosition
  guard $ (sourceColumn pos) >= n
  return []

p_op_by_len :: Parsec String () [Packet]
p_op_by_len = do
  len <- try (char '0') >> count 15 p_bin
  pos <- getPosition
  let nextN = (sourceColumn pos) + (bin2dec len)
  ps <- manyTill p_packet $ p_position nextN
  return ps
```

This does the following:

- the `p_position` parser takes a position and returns a parse failure if the position is less than `n`
- the `p_op_by_len` parser first reads in the character `0` - if it doesn't match it fails - and then 15 binary
  characters.  That makes up the `len`.
- then we get the current position and add the column value to `len`
- then we use the `manyTill` parser combinator to parse `p_packet` until the `p_position` parser gets a match.

Finally, we parse a whole `Op` packet:

```haskell
p_op :: Parsec String () Packet
p_op = do 
  ver <- p_ver
  typeId <- p_type
  ps <- p_op_by_len <|> p_op_by_num
  return (Op (bin2dec ver) (bin2dec typeId) ps)
```

We get the version and the type id - followed by the packet payload.  Now this can be either `p_op_by_len` or 
`p_op_by_num` - and the `<|>` operator allows to choose between them.  If the first one succeeds, the second one is not 
used.

Lastly, we define how to parse a packet:

```haskell
p_packet :: Parsec String () Packet
p_packet = try p_lit <|> p_op
```

A packet can either be a literal or an operand.

# Part One

For part one, all that was asked was to add up the version ids in each of the packets:

```haskell
sumVersions' :: Packet -> Int
sumVersions' (Lit v _) = v
sumVersions' (Op v _ ps) = v + (sum $ map sumVersions' ps)
```

Again, the Haskell pattern matching shines through here.  If we have a `Lit` packet, then we just return the version
integer, if we have an `Op` packet, then we return the version and add up the results of recursively calling the 
function itself.

# Part Two

For part two, we had to evaluate the operators.  Depending on the operator, the result would be a different arithmetic
operation.  As it turned out, with the parsing completed, the hard part was already done.  To evaluate, I just needed
something slightly more complicated than the `sumVersions` from part one:

```haskell
evaluate :: Packet -> Int
evaluate (Lit _ n) = n
evaluate (Op _ 0 ps) = evaluateAll sum ps
evaluate (Op _ 1 ps) = evaluateAll product ps
evaluate (Op _ 2 ps) = evaluateAll minimum ps
evaluate (Op _ 3 ps) = evaluateAll maximum ps
evaluate (Op _ 5 ps) = evaluateAll (boolOp (>)) ps
evaluate (Op _ 6 ps) = evaluateAll (boolOp (<)) ps
evaluate (Op _ 7 ps) = evaluateAll (boolOp (==)) ps

evaluateAll :: ([Int] -> Int) -> [Packet] -> Int
evaluateAll f ps = f $ map evaluate ps
```

So to evaluate an operation, we need to evaluate each of the types of packet.  

- For a `Lit` literal, the value was just whatever was parsed in.
- For an `Op` operator, the result would depend on the type id.  Using pattern matching and a helper function, I am 
  able to express this quite succinctly.  
- The `evaluateAll` takes a function that takes a list of integers as input and a single integer as output, it also
  takes a list of packets.  It then evaluates each packet and feeds that to the function.
- So operation `1` sums up all the value using the built in function `sum`
- Same with `product`, `minimum` and `maximum`

For the last remaining operators - they are boolean operators that only work on lists of size two:

```haskell
boolOp :: (Int -> Int -> Bool) -> [Int] -> Int
boolOp f [a, b] = bool2num $ f a b

bool2num :: Bool -> Int
bool2num True = 1
bool2num False = 0
```

So `boolOp` is a function that takes two parameters.  First, a function that compares two integers.  Then a list of
integers and it returns an integer.  By using currying, we turn `boolOp (<)` into a function that is `[Int] -> Int`
with the first parameter filled in by `<` (operators in Haskell are just functions - calling `a < b` is just syntactic
sugar for the function call `(<) a b`).  That way I can express any boolean operation easily.  For this puzzle, I just 
needed `<`, `>` and `==`.

To get the result of this puzzle, I just combined the parsing with the evaluating:

```haskell
binStr = hex2bin input
packet = parse p_packet "" binStr
evaluate' packet
```

# Gotchas

When I initially implemented the first part, I was a little unfocused when reading about the subpackets in the operator.
I skimmed over the fact that the two different types were talking about two different lengths.  Number of packets
or number of bits in the payload.

My first implementation of the `Op` parser was

```haskell
p_op :: Parsec String () Packet
p_op = do 
  ver <- p_ver
  typeId <- p_type
  len <- (p_len_t_15 >> p_len_15) <|> (p_len_t_11 >> p_len_11)
  ps <- many (try p_packet)
  return (Op (bin2dec ver) (bin2dec typeId) ps)
```

Basically, I would read in the version and the type, the would read in the length - which can be defined as either a
11 bit length field or a 15 bit length field - then completely ignore the length and read in as many sub packets as I 
could.

Now this worked for part 1.  But it was a fluke.  When it was reading in subpackets, it did not stop reading until
it couldn't read anymore packets.  That meant that when operator packets were inside other operator packets, some that 
packets that belonged to other operators would be associated with the wrong "parent".  But because the answer for 
part 1 was just the sum of all the version values it did not matter whether a packet was associated with the right
operator.

For part 2, that mattered a lot.  It was only then that I came up with the idea of using the `manyTill` parser
combinator.

Another fun gotcha was that I initially forgot to add the `return` to the parsers when I was using do notation.  This 
meant that the parsers just kept giving me compile errors.  `return` is a bit of a funny one when you initially encounter
it coming from imperative programming.  It is NOT a return statement.  It is a function that wraps the value in the 
monad being used in the do notation.

The type signature of return is:

```haskell
return :: Monad a => b -> a b
```

But missing that off meant that instead of dealing with a `Parser String`, the function was returning a `String` - and
that understandably didn't make the compiler happy.

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day16) 

# Conclusion

I found this introduction to parsing in Haskell absolutely fascinating.  Before doing this exercise I probably would
have reached for regular expressions or writing functions to operate on the source string, but I found that the
code is more succinct and clear than the specification of the issue.  Furthermore, non of these constructs are
extensions to the language or parsing specific, all is achieved by using monads.  And this is what is making me 
appreciate the simplicity and beauty of Haskell even more.

And no, I'm not going to do a post trying to explain what monads are! Not yet anyway.
