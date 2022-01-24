---
title: "Advent of code 2021: Day 21"
date: 2022-01-08T23:44:32Z
tags: ["functional","haskell","advent-of-code"]
featured_image: "/images/advent_of_code_title.jpg"
---

On Day 21 of [Advent of Code 2021](https://adventofcode.com/2021) we played Dirac Dice!  And part one felt way too
easy to solve, but I needed have worried because part two we were asked to solve a limited multiverse problem.
And without a quantum computer!

# The problem

[The problem](https://adventofcode.com/2021/day/21) was described as follows.

* Two players are playing a game with three dice
* Each starts on a different position of the board
* The board is circular and has 10 numbered positions
* Players take it in turn to roll three dice
* A player moves forward by the sum of those three dice
* Once the complete their move, they add as many points to their score as indicated by the board position
* First to 1000 points wins
* The dice are deterministic, meaning that they roll 1, 2, 3, ..., 100, 1, 2, ...

# Part One

To start with, I created some types:

```haskell
type PlayerStatus = (Int, Int)
type GameStatus = (PlayerStatus, PlayerStatus)
type DiceRolls = [Int]
```

- The player status was a tuple of two integers.  The position on the board and the score.
- The game status represented the fact that there are two players.
- The dice rolls were modelled as a list.

Then my thoughts went to how to model rolling the dice.  As Haskell is lazy, I just created the dice rolls as a list:

```haskell
diceRolls :: DiceRolls
diceRolls = cycle [1..100]
```

The `cycle` function just repeats the list forever.  So to roll my dice, I'd just have to take some elements off the top
of the list.

Then I defined a function that would move the player forward by a given number of steps (`p` is the current position and
`n` is how many fields forward we move):

```haskell
nextpos :: Int -> Int -> Int
nextpos p n = (p + n - 1) `mod` 10 + 1
```

The next step was to model the game itself.  I figured I would write a function that would check whether either
player had won, and if not updated the game status.  Well, not strictly updating, our data structures are immutable,
but I got so used to thinking recursively that that's what I ended up doing:

```haskell
play :: GameStatus -> Int -> DiceRolls -> Int
play ((_, score1), (_, score2)) num _
  | score2 >= 1000 = score1 * num
play ((pos, score), p2) num (r1:r2:r3:dice) =
  let pos' = nextpos pos (r1+r2+r3)
      score' = score + pos'
  in play (p2, (pos', score')) (num+3) dice
```

There's a few things to unpack:

- The `play` function takes a game status, the number of dice that have been rolled and the dice roll list
- First, it destructures the status and checks whether player two's score was greater than 1000 already (why only player
  two you ask, read on)
- If yes, it returns the answer (AOC was looking for the number of dice rolls times the score of the losing player)
- If no, it would do a turn
- It pulls out the first players status, uses destructuring to get three rolls from the dice list and moves to the
  next position
- Then it calls itself recursively swapping player one and two. This way I didn't need any special logic for having the
  players take turns.
- N.B. the code doesn't actually tell you who won the game (player 1 and 2 constantly swap) - but the puzzle did not
  need that information.

I was pretty chuffed with this approach!

# Part Two

For part two the rules slightly changed (but make a big difference)

- Instead of dice rolls that go from 1 to 100, deterministically repeating, each dice now only had 3 faces
- But every time the dice gets rolled, the universe would split in 3, with each of the outcomes taking place in
  one of the universes
- The games would only last until the winner had reached the score 21
- But the puzzle was to determine how many universes each of the players wins in

Considering the example stated that

> in the example above, player 1 wins in 444356092776315 universes, while player 2 merely wins in 
> 341960390180808 universes

It was immediately clear that brute forcing and simulating each universe separately was not an option

So I started thinking about the dice rolls.  Each player would still make three rolls, add them together and move
forward by that amount.  But if a player rolls 1+2+3 or 3+2+1 or 2+2+2, they would move forward 6 positions each.
And I would be able to calculate the next position and update the score once.

Let's write some code to work out the frequencies of the sums of the dice rolls:

```haskell
diceFreq :: [(Int, Int)]
diceFreq = map (\x -> (head x, length x))
             $ group
             $ sort
             $ [a+b+c | a <- [1..3],
                        b <- [1..3],
                        c <- [1..3]]
```

So instead of calculating `3^3=27` universes, we would look at 7 (and their frequencies) 

```haskell
*Part2> diceFreq
[(3,1),(4,3),(5,6),(6,7),(7,6),(8,3),(9,1)]
```

I tried running a simulation then and killed the process after 5 minutes.  Clearly, I was still doing too much work.

As a side note, I think these puzzles are great, because if something takes a long time, you know you're not on the
right path!

I was thinking I am probably still duplicating a lot of work, so I went about introducing some deduplication:

```haskell
dedupe :: [(GameStatus, Int)] -> [(GameStatus, Int)]
dedupe = map (\gs -> (fst $ head gs, sum $ map snd gs)) 
       . groupBy (\a b -> (fst a) == (fst b)) 
       . sort
```

To unpack:

- I've got a frequency list of game status
- We sort it
- Group it by game status
- And add up all the frequencies

The next step is to define how we're going to split the universes.  First we need a way to check whether a particular
game is finished:

```haskell
checkGameover :: (GameStatus, Int) -> Bool
checkGameover (((_, score1), (_, score2)), _)
  = max score1 score2 >= 21
```

The above just checks the scores of both players, and if either of them is above 21, the game is finished.

Then we turn 1 game status into multiple:

```haskell
turn :: Int -> (GameStatus, Int) -> [(GameStatus, Int)]
turn player x@(gs, num)
  | checkGameover x = [x]
  | otherwise       = 
      map (\(roll, freq) -> (playerTurn player gs roll, num * freq)) diceFreq
```  

To unpack:

- Look at the signature, there's the player (which could be `1` or `2`) to indicate which player is taking a turn
- And the game status (with frequency), and it returns a list of game states (with frequency)
- It then uses the `diceFreq` we've defined above and uses the `playerTurn` to play a turn
- The `num` number of universes is the multiplied with the `freq` from the dice rolls to determine the new number
  of universes that are the same for that particular state

Looking at `playerTurn`, that just checks the `player` parameter to see which player status should be updated.  Note,
unlike part one, we now don't want to keep swapping player positions as who wins (player 1 or 2) is pertinent.

```haskell
playerTurn :: Int -> GameStatus -> Int -> GameStatus
playerTurn 1 (p1, p2) roll = (turn' p1 roll, p2)
playerTurn 2 (p1, p2) roll = (p1, turn' p2 roll)
```  

The `turn'` function is pretty similar as before and just determines the next position and score.

```haskell
turn' :: PlayerStatus -> Int -> PlayerStatus
turn' (pos, score) roll =
  let pos' = nextpos pos roll
      score' = score + pos'
  in (pos', score')
```

One last helper function will help us to count the results:

```haskell
countWinner :: (GameStatus, Int) -> [Int]
countWinner (((_, score1), (_, score2)), num)
  | score1 > score2 = [num, 0]
  | otherwise       = [0, num]
```

The above checks the player scores and depending on whether player 1 or 2 is higher, creates a list were the first
or second element contains the number of universes this status represents.

Putting it all together, we can now define our main function:

```haskell
play :: Int -> [(GameStatus, Int)] -> [Int]
play player games =
  let games' = games >>= turn player
      deduped = dedupe games'
      finished = all checkGameover deduped
  in play' player deduped finished

play' :: Int -> [(GameStatus, Int)] -> Bool -> [Int]
play' _ gs True = foldl (zipWith (+)) [0,0] $ map countWinner gs
play' player gs False = play (nextPlayer player) gs
```

To unpack, we:

- Use the bind operator (`>>=` - think `flatMap` in scala) to replace each element in the list by using the `turn`
  method to play a turn.  This will increase the size of the list
- Then we try to dedupe the list.  This will decrease the size of the list.
- Then we check whether all of the games represented by the deduped list are finished.
- If we are finished, then `play'` will use `countWinners` and a fold to reduce all the results into a simple list of 
  universes where player 1 and 2 have won.
- If not finished, then we recursively call `play` with the next player, to take another turn.

Rest of the solution on [GitHub](https://github.com/beny23/advent-of-code/tree/main/2021/haskell/day21) 

# Conclusion

Although I've got the sneaky suspicion that a lot more in the puzzle could be done with pen, paper and mathematics,
I was quite pleased with the outcome.  Using the bind operator (`>>=`) meant I could keep the number of recursive calls
down to a minimum, and deduplicating kept the lists from growing too big. In the end, part two took about 3 seconds to 
run (that's including the build) on my 6 year old MacBook, which I was quite pleased about, though it was interesting
that I'm repeated check whether a game has finished yet but that was quicker than maintaining separate lists of 
completed games.
