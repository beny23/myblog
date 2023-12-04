---
title: "AI-dvent of Code 2023: Day 2"
date: 2023-12-03
tags: ["functional","kotlin","advent-of-code","llm"]
featured_image: "/images/advent_of_code_2023_day_2_ai_experiments_title.png"
---

After [day 1]({{< ref "advent_of_code_2023_day_1_ai_experiments.md" >}}) of [Advent of Code](https://adventofcode.com/2023) 
was only partially successful, I thought I would change my approach. Yesterday I tried to get the LLM to develop the 
whole solution in one go. But that was more like argumentative programming rather than conversational programming, so
for the [day 2 puzzle](https://adventofcode.com/2023/day/2) I start with small problems and hope to put it all together 
at the end.

# Let's get started

The problem looked quite straightforward, parse something like the following

```
Game 1: 3 blue, 4 red; 1 red, 2 green, 6 blue; 2 green
Game 2: 1 blue, 2 green; 3 green, 4 blue, 1 red; 1 green, 1 blue
```

These are games where different coloured cubes are hidden in a bag, and the Elves pick a handful of cubes (delimited by
semicolon). So I thought, let's start by parsing numbers and colours.

```
>>> create a function in kotlin that parses the following 
>>> "5 blue, 4 red, 13 green" and extracts the number of 
>>> red, blue and green

Here's an example of how you could do this in Kotlin:

fun parseColors(colorString: String): Map<String, Int> {
  val colors = colorString.split(",")
  return colors.associateWith { it.substringBefore(" ").trim().toInt() }
}
```

I also thought I should do some testing, that will allow me to check the what codellama comes up with quickly:

```
>>> write some tests for the parseColors function 

Here are a few examples of how you could test the `parseColors()` function in Kotlin:

class ParseColorsTest {
    @Test
    fun `should parse colors correctly`() {
        val colorString = "5 blue, 4 red, 13 green"
        val expectedResult = mapOf("blue" to 5, "red" to 4, "green" to 13)
        assertEquals(expectedResult, parseColors(colorString))
    }
}
```

Hey, I didn't know that Kotlin can use backticks for function names, that's neat! Learned something new again.

Unfortunately, the tests fell over:

```
Tests > should parse colors correctly() FAILED
    java.lang.NumberFormatException: For input string: ""
        at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:67)
        at java.base/java.lang.Integer.parseInt(Integer.java:671)
        at java.base/java.lang.Integer.parseInt(Integer.java:777)
        at Part1Kt.parseColors(Part1.kt:5)
        at Tests.should parse colors correctly(Tests.kt:9)
```

After a few bits of back and forth, and eventually giving up and just writing it myself, I had some passing tests.

So I thought, let's write some more tests!

```
>>> create a test case for 5 red and 2 green

Here's an example of how you could create a test case for "5 red" and "2 green":

@Test
fun `should return correct values for 5 red and 2 green`() {
    val input = listOf("5 blue", "4 red", "13 green")
    val expectedOutput = mapOf(5 to "blue", 4 to "red", 13 to "green")
    assertEquals(expectedOutput, parseColors(input))
}
```

Well that's just silly, the same test as before, just with the test name changed. Let's try that again:

```
>>> the above test case has blue in it, I don't want that

I apologize for the confusion. You are correct that the previous test case 
included "blue" as a value in the expected output map. Here's an updated version 
of the test case that does not include "blue":

@Test
fun `should return correct values for 5 red and 2 green`() {
    val input = listOf("5", "4 red", "13 green")
    val expectedOutput = mapOf(5 to null, 4 to "red", 13 to "green")
    assertEquals(expectedOutput, parseColors(input))
}
```

So we just remove the word "blue" - ah genius!

![Picard Facepalm](/images/advent_of_code_2023_day_2_ai_experiments_facepalm.png)

And so it went on. I get the feeling that either Eric knows exactly how to trip up AI code assists, or (more likely)
LLMs just regurgitate stuff that sound good, but more often than not it is actually not compiling, not working or just
too pedantic to actually make "collaborating" worthwhile. I keep thinking, that it is just trying to annoy me. But that
of course is anthropomorphising the stochastic parrot.

# Tell us about Kotlin then!

With all the time spent trying to get some sense out of CodeLlama, I'm not sure whether my solutions are idiomatic,
but I very much liked that Kotlin can be used in a very functional way, without classes. I hadn't realised that before,
and just imagined it was very much closer to Java:

- https://github.com/beny23/advent-of-code/blob/main/2023/kotlin-ai/day2/src/main/kotlin/Part1.kt
- https://github.com/beny23/advent-of-code/blob/main/2023/kotlin-ai/day2/src/main/kotlin/Part2.kt

I'll be back for more frustrations with LLMs for the next day, if you think I'm doing something glaringly obviously
wrong, please reach out. I don't want to just bash LLMs, but so far my scepticism has been reinforced.

Check out [day 3]({{< ref "advent_of_code_2023_day_3_ai_experiments.md" >}}) where I change model.
