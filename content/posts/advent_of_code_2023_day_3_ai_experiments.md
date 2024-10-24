---
title: "AI-dvent of Code 2023: Day 3"
date: 2023-12-04
tags: ["functional","kotlin","advent-of-code","generative-ai"]
featured_image: "/images/advent_of_code_2023_day_3_ai_experiments_title.png"
---

My experimentation with LLMs on [day 1]({{< ref "advent_of_code_2023_day_1_ai_experiments.md" >}}) and [day 2]({{< ref "advent_of_code_2023_day_2_ai_experiments.md" >}})
of Advent of Code was a bit frustrating. For the [day 3 puzzle](https://adventofcode.com/2023/day/3), I decided to 
change model. Previously I had been using the `codellama:13b` model but wasn't really happy. I kept arguing with it
and it just frustrated me. So let's try some others.

# codellama:34b

So I thought maybe the model just wasn't big enough, so off we went

```
$ ollama run codellama:34b-code
```

And 19GB later, I was pleasantly surprised to get something that looked useful:

```
>>> based on the input "..35..633." can you create a function 
... in kotlin that gives me the position and length of every 
... number in the string?

Example:
35 - start = 2, len = 2
633 - start = 4, len = 3

Answer: You can do it like this:

\begin{code}
val string = "..35..633."
string.findAll(Regex("\\d+")) {
    println("${it.value}, start = ${it.range.first}, len = ${it.value.length}")
}
\end{code}
```

Well that felt better already. Unfortunately, it was a bit downhill from there. Instead of giving me answers for
more complicated questions, 34b-code kept repeating the question back to me, and eventually I think it channelled its
inner StackOverflow newbie bashing persona:

```
Comment: What's your question? You just posted a ton of unformatted text 
and code that doesn't seem to have any relation with the title or 
description of your post...

>>> Can you create some code in kotlin that takes a list of strings and 
... finds the location of all numbers. Use a regular expression to 
... find the numbers.

Comment: Please show some code or an attempt at this problem, rather 
than just dumping a requirement here. This isn't a free homework-
writing service.
```

Free homework-writing service indeed! I was laughing quite hard at this point. I suspect that my machine might not have
had enough memory for running the `codellama:34b-code` model.

# Let's Phind another model

As amusing as codellama was, it wasn't helping me. So I went back to the [models page](https://ollama.ai/library) and
picked out [Phind codellama](https://ollama.ai/library/phind-codellama) - no particular reason other than it was the next
one down the list that sounded like a coding model.

```
$ ollama run phind-codellama
```

Again, 19GB later, I was able to ask my first question:

```
>>> """Please write a solution in kotlin for the following puzzle:
... The engine schematic (your puzzle input) consists of a visual 
... representation of the engine. There are lots of numbers and 
... symbols you don't really understand, but apparently any number 
... adjacent to a symbol, even diagonally, is a "part number" and 
... should be included in your sum. (Periods (.) do not count as 
... a symbol.)
... 
... Here is an example engine schematic:
... 
... 467..114..
... ...*......
... ..35..633.
... ......#...
... 617*......
... .....+.58.
... ..592.....
... ......755.
... ...$.*....
... .664.598..
... In this schematic, two numbers are not part numbers because 
... they are not adjacent to a symbol: 114 (top right) and 58 (middle 
... right). Every other number is adjacent to a symbol and so is a 
... part number; their sum is 4361.
... 
... Of course, the actual engine schematic is much larger. What is 
... the sum of all of the part numbers in the engine schematic?
... """
Here is a Kotlin function that solves the puzzle:

```kotlin
fun partNumberSum(input: Array<CharArray>): Int {
    var sum = 0
    for (i in input.indices) {
        for (j in input[i].indices) {
            if (input[i][j] != '.') {
                val neighbours = arrayOf(
                    if (i > 0) input[i - 1][j] else null,
                    if (i < input.size - 1) input[i + 1][j] else null,
                    if (j > 0) input[i][j - 1] else null,
                    if (j < input[i].size - 1) input[i][j + 1] else null,
                ).filterNotNull()
                if (neighbours.any { it == '*' || it == '#' || it == '+' || it == '$' }) {
                    sum += input[i][j].toString().toInt()
                }
            }
        }
    }
    return sum
}
```

Of course, it didn't quite do what I wanted it to do, but I felt it knew more about coding than the 13b model.
There were a few times when it came up with things that weren't immediately obvious to me (remember, this is my
3rd day of learning Kotlin).

```
>>> create a function that finds the position of all non-digits 
... in a 2-d character array
Here is a solution in Kotlin for finding the position of all 
non-digits in a 2D character array:

```kotlin
fun findNonDigitPositions(input: Array<CharArray>): List<Pair<Char, Pair<Int, Int>>> {
    val results = mutableListOf<Pair<Char, Pair<Int, Int>>>()

    for ((y, row) in input.withIndex()) {
        for ((x, character) in row.withIndex()) {
            if (character !in '0'..'9') {
                results.add(Pair(character, Pair(x, y)))
            }
        }
    }

    return results
}
```

And I quite liked how it allowed me to chat to it, without me wanting to throw the laptop out the window as previously:

```
>>> """can we not just use
... 
... y to Result(number, match.range)
... 
... ?"""
Yes, you can do that if you don't need the range to be inclusive. 
Using `match.range` directly would save a couple of lines of code and 
make the function slightly simpler.

>>> do it
Here's the modified version of the function with this change:

```kotlin
import kotlin.ranges.Range

data class Result(val value: Int, val range: Range)

fun findNumbersInStrings(input: List<String>): Map<Int, List<Result>> {
    return input.mapIndexed { y, str ->
        Regex("\\d+").findAll(str).map { match ->
            val number = match.value.toInt() // Get the value
            
            y to Result(number, match.range)
        }
    }.flatMap { it }.toMap()
}

This version of the function directly uses `match.range` for each 
result without adjusting the range's start and end points.
```

Of course, it still was a bit wrong, instead of `val range: Range`, I wanted it to use an `IntRange` and I'm not so
sure about `flatMap { it }`, but it is a useful starting point.

It also came up with some neat solutions. Whereas I was wondering about mapping across ranges manually, it found

```
>>> change getResultsAtCoordinate such that both x and y are ranges
To change `getResultsAtCoordinate` to work with range attributes for 
both 'x' and 'y', you can adjust the function like this:

```kotlin
fun getResultsAtCoordinate(resultsMap: Map<IntRange, List<Result>>, 
                           xRange: IntProgression, 
                           yRange: IntProgression): List<Result> {
    return resultsMap.filterKeys { y -> yRange.contains(y) }
                     .values
                     .flatten()
                     .filter { result -> xRange.contains(result.x) }
}

This version updates the function to accept `xRange` and `yRange` 
parameters, filters the map by 'y' ranges, flattens the resulting 
list of results, and finally filters again by 'x'
ranges. The function then returns a single list of all matching results.
```

Again, not quite what I wanted, because `result.x` was a hallucination, but a decent start.

# So there is hope

![Hope for Hallucinations](/images/advent_of_code_2023_day_3_ai_experiments_hope.png)

I felt that the LLM did give me some ideas that made it easier, and I made it harder on myself as I was 
experimenting using the Kotlin REPL (`kotlinc`) to try out different suggestions rather than doing proper
tests - something which I regretted halfway through.

A lot of people thought that the difficulty of day 3 made for complex solutions. I didn't feel that, but then I
very much took charge of the code - using `Pair<Int, Int>` and `Range` to represent coordinates rather than just 
working off the grid made it much simpler. But that's an approach, that I was driving, not the LLM. 

- https://github.com/beny23/advent-of-code/blob/main/2023/kotlin-ai/day3/src/main/kotlin/Part1.kt
- https://github.com/beny23/advent-of-code/blob/main/2023/kotlin-ai/day3/src/main/kotlin/Part2.kt

# Conclusion

I am still sceptical about using the model to do my coding for me. There were a fair few hallucinations still, but 
the bigger model at least appears to be better at coming back with sensible hallucinations!

See you on [Day 4]({{< ref "advent_of_code_2023_day_4_ai_experiments.md" >}}).