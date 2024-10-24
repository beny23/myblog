---
title: "AI-dvent of Code 2023: Day 1"
date: 2023-12-02
tags: ["functional","kotlin","advent-of-code","generative-ai"]
featured_image: "/images/advent_of_code_2023_day_1_ai_experiments_title.png"
---

So it is that time of the year again. [Advent of Code](https://adventofcode.com/2023) is back. Yey! This means 
I get to try to look at a new language again. This time, why not Kotlin? But as an extra challenge, I thought 
why not see how the vaunted LLMs would help. Is AI really the accelerator that
would elevate a mere developer to a rockstar ninja (whatever that is)? 

I have to add that I am a bit of an [AI sceptic]({{< ref "apocalyps_ai.md" >}}) and keep saying that

> I welcome LLMs! Fixing the stupid mistakes that will be made will keep me in gainful employment well past my
> retirement age

But it is easy to shoot from the hip, so I figured I'll see whether it can help me to develop some code.

# Setting up

For my first steps with LLMs, I thought I'd keep it nice and simple and would give [ollama.ai](https://ollama.ai) a try.
It is a runner that allows me to play with multiple different models - and it's easy to install.

```
$ brew install ollama
```

Then it is just a matter of picking [a model](https://ollama.ai/library). I chose `codellama:13b` for my first play:

```
$ ollama run codellama:13b
>>> Please write a program to solve the following in Kotlin:
[...]
Here is a Kotlin program to solve the puzzle:
```kotlin
fun main() {
    val input = "pqr3stu8vwx" // Replace with your puzzle input
    
    var sum = 0
    for (line in input.lines()) {
        val value = line.first().toString() + line.last().toString()
        sum += value.toInt()
    }
    
    println(sum) // Prints the sum of all calibration values found in the input
}
```

Ok, so it wasn't going to be this easy. Comparing the "solution" with what was asked for on [day 1](https://adventofcode.com/2023/day/1)
was a fair bit off. I was hoping for some code that would:
- parse a file
- for each line get the first and last digits
- and then add them up

To be fair to the model, it got the idea right, it's just the implementation that fell over. I also should add that
it took me much longer than I had figured to set up `gradle` to compile and run kotlin. I guess I'm a bit rusty.

So I started arguing with the model:

```
>>> you have to use the first and the last digit, there can be more than two digits!

Here is a Kotlin program to solve the puzzle:
```kotlin
fun main() {
    val input = "pqr3stu8vwx" // Replace with your puzzle input
    
    var sum = 0
    for (line in input.lines()) {
        val value = line.toCharArray()
                        .filter(Char::isDigit)
                        .map(Int::parseInt)
        sum += value[0] * value[value.size - 1]
    }
    
    println(sum) // Prints the sum of all calibration values found in the input
}
```

And it failed to compile, and combining the first and last digit to make a two-digit number by multiplying also isn't
right.

Not the best of starts!

# What about some help with simple tasks?

I hadn't used kotlin in anger before, so I don't know a lot of simple programming tasks. Normally, I would just google
for the answers, usually via some StackOverflow answer, but I thought I'd ask the model:

```
>>> can you read the input from a file? don't explain the code for now

Yes, it is possible to read the input from a file in Kotlin. You can use the `File`... 
[...]
val lines = File("input.txt").readLines()
[...]
```

That's quite useful. Shame it was surrounded by 100 lines of text with lots of different options and instructions on
how to print the absolute path for a file.

Eventually, I came up with a decent solution for [part 1](https://github.com/beny23/advent-of-code/blob/main/2023/kotlin-ai/day1/src/main/kotlin/Part1.kt):

```kotlin
fun main() {
    val input = File("input.txt").readText()

    var sum = 0
    for (line in input.lines()) {
        val value = line.toCharArray()
                        .filter(Char::isDigit)
                        .map(Char::digitToInt)
        sum += value[0] * 10 + value[value.size - 1]
    }

    println(sum)
}
```

If I'm honest, I'd have preferred it to be a bit more functional, and I had to look up `Char::digitToInt` using google,
as the model kept on insisting on using `Int::parseInt`, but hey-ho, it worked.

# There's a part two?

Part 2 is where it got interesting. I think a lot of people were surprised about the complexity for day 1 part 2. 
Instead of just digits, the challenge was that numbers could also be represented by words, such as `one`, `two` etc. 
But it didn't stop there, the input also had to deal with scenarios like `twone` - this could be `2` and `1`.

I got quite frustrated with the model, as I felt like I had to constantly badger the model to do the right thing. And
more often than not it would just revert to the same solution used previously, that didn't work.

I ended up quite annoyed when codellama would utter the phrase:

> I apologize for the confusion earlier. It looks like there was an issue with my previous response.
> Here's a revised version of the code that should work:

It felt like working with someone who was very junior whose hand had to be held and everything had to be double-checked.

# Well what do you expect from a model run locally?

I thought maybe running on my Mac was a fools errand, so I ended up giving ChatGPT 3.5 a simplified exercise:

> I want a function in kotlin that searches for the first occurrence of "one" or "two" regardless of what order 
> and replaces it with "1" or "2"

That didn't work either. See the [log of my conversation](https://chat.openai.com/share/1cfa7a19-abb5-4155-86dc-39c76a60643f).
It took 24 prompts to get a working answer, and I thought it was particularly ugly:

```
var replaced = 0
return regex.replace(input) {
    val replacement = replacements[it.value.toLowerCase()] ?: it.value
    if (replaced == 0) {
        replaced++
        replacement
    } else {
        it.value
    }
}
```

My solution for [part 2 is here](https://github.com/beny23/advent-of-code/blob/main/2023/kotlin-ai/day1/src/main/kotlin/Part2.kt)

# Conclusion

Using the "AI" taught me a fair amount about Kotlin, so in a way it was a success. But it taught me Kotlin because I 
already knew Java and Scala and because of a fair amount of traditional googling. I was quite impressed how easy
it was to get an LLM running locally, but that may well be down to me not having played with LLMs previously. Who knows,
maybe Copilot or ChatGPT4 are much better, but I remain sceptical. 

For [day 2]({{< ref "advent_of_code_2023_day_2_ai_experiments.md" >}}) I think I'll approach it differently.
