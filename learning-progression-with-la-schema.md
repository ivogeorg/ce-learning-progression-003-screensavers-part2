# CPE 1040 - Fall 2020

This is learning progression 003 for the Fall 2020 installment of the course CPE 1040: Introduction to Computer Engineering at MSU Denver.

Table of Contents
=================

* [CPE 1040 \- Fall 2020](#cpe-1040---fall-2020)
* [Table of Contents](#table-of-contents)
  * [Learning Progression 003: Screensavers (Part 2)](#learning-progression-003-screensavers-part-2)
    * [5\. Randomized behavior](#5-randomized-behavior)
      * [1\. Study](#1-study)
      * [2\. Apply](#2-apply)
      * [3\. Present](#3-present)
    * [6\. Encapsulation](#6-encapsulation)
      * [1\. Study](#1-study-1)
      * [2\. Apply](#2-apply-1)
      * [3\. Present](#3-present-1)
    * [7\. Functions revisited](#7-functions-revisited)
      * [1\. Study](#1-study-2)
      * [2\. Apply](#2-apply-2)
      * [3\. Present](#3-present-2)
    * [8\. Classes revisited](#8-classes-revisited)
      * [1\. Study](#1-study-3)
      * [2\. Apply](#2-apply-3)
      * [3\. Present](#3-present-3)
      
## Learning Progression 003: Screensavers (Part 2)
[[toc](#table-of-contents)]

This progression is the culmination of the first part of the course, in which we program the bear-bones micro:bit without any extenral circuitry attached and without communication features. We pull together all the programming language features and best practices that we introduced in the previous two learning progressions, to write a significantly larger target program over 12 steps. This will present the opportunity to learn about some of the design considerations a programmer makes when approaching a larger project. The progression is also going to dig a bit deeper into the _softare stack_ of the micro:bit, and uncover the ways it affects these considerations.

   
### 5. Randomized behavior  
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

##### Importance of randomization

`[<lernact-rd>]`We have used `[<cept>]`_randomization_ in fairly inconsequential ways so far, mostly to have somewhat unpredictable behavior of the output on the micro:bit 5 x 5 matrix. However, randomization is extremely important in science and engineering. Among other things, it allows generating important variation in test and diagnostics programs, achieving nearly-even coverage in probability distribution sampling, and good spreading in hash table storage. The full significance of randomization is well beyond the scope of this course.

##### Pseudorandom numbers

The numbers that the `random` functions return are actually `[<cept>]`[_pseudorandom_](https://en.wikipedia.org/wiki/Pseudorandom_number_generator), which means that an algorithm generates them in an extremely long evenly spread-out sequence. The number of elements in the whole sequence is so large, that in any specific location along it, there appears to be no relationship between nearby elements. Truly random functions are usually only achieved based on [random physical phenomena](https://www.random.org/randomness/).

##### Random functions

In MakeCode, we have access to three randomization functions:
1. `Math.random()` returns a pseudorandom floating-point (that is, a (possibly `[<cept>]`_truncated_) real number) number between 0 and 1. _Note that the value of 1 is [not included](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random)._  
2. `Math.randomBoolean()` returns `true` or `false` with the evenness of a pseudorandom generator. This is akin to a `[<cept>]`_coin toss_. Just like a coin toss, you cannot expect any kind of `[<cept>]`_alternation_ of `true` and `false`. They are evenly distributed (that is, there is an approximately even number of `true` and `false` value) only in the `[<cept>]`_limit_ of `[<cept>]`_infinite_ trials.  
3. `randint(min, max)` is provided as a convenience function in MakeCode and is not usually part of the canonical JavaScript library. It returns `[<cept>]`_signed integers_, that is the values of the numbers it returns are whole numbers, which can be negative, positive, and/or zero. Curiously, the ordering of the arguments is not enforced. That is, `randint(0, 4)` is functionally equivalent to `randint(4, 0)`. _Note that both min and max are included in the range from which pseudorandom integers are returned._  

Using these functions, you can write your own randomization routines, to fit your purposes.

##### Randomization in the target program

Let's review the randomizations we have used so far in this progression, along with some implementation pointers:
1. `coding()` has a very simple randomization, namely the length of the "code lines", which can be achieved with a straightforward use of `randint(0, 5)` or `randint(1, 5)`, depending on whether we want to have empty lines or not.  
2. `freqBars()` also has a similarly simple randomization, namely the height of the bars. Again, this can be achieved with the use of `randint(0, 4)`. _Note, however, that the vertical coordinate of the LED matrix grows from 0 at the top down to 4 at the bottom, so you need to subtract the number you get from the maximum height. And you may also have to plot LEDs in an inverted `for` loop._  
3. `rain()` has a little more elaborate randomization, namely that it adopts an internal concept of _distance_ in order to move the "raindrops" differently depending on how far they are from the viewer. In particular, there are 5 levels of depth, with 0 being the "closest" and 4 being the "farthest". The raindrops are brighter in the foreground than in the background, and are moving faster across the screen in the foreground than in the background. This kind of randomization requires something other than `basic.pause()` to achieve, because `basic.pause()` causes the processor to idle, regardless of what else might also be running on it. `distance` is used in two ways:
   - first, initialized with `Math.randomRange(0, 4)`, to pick the brightness of the raindrop from the array `brightLevels: number[] = [240, 180, 60, 30, 1]`, and  
   - second, combined with another field called `time_step` (which is initialized to zero in the constructor and also upon reset), to pick how often to let the raindrop fall, in the `fall()` method of the `Raindrop` class:
     ```javascript
     fall() {
         this.setBrightness(this.brightLevels[this.distance])
         if (this.time_step == this.distance) {                       // let the raindrop fall only when it matches its distance (see last line)
             this.hide()
             this.y = this.y + 1
             this.show()
         }
         this.time_step = (this.time_step + 1) % (this.distance + 1)  // advance time_step modulo the distance (indexed starting at 1, not 0)
     }
     ```
   The rain simulation creates a certain number of raindrops and reuses them when they fall off the bottom, resetting them to a random distance and a random column and wrapping them around to the top. The rain is just an array `let rainfall: Raindrop[] = []` that a `rain()` function cycles through indefinitely. This makes the simulation appear random, rather than a repeating pattern.

**TODO** new rain video

##### Bouncing marbles

This section introduces a new screensaver, called Bouncing Marbles. Here is a [video](). **TODO** bouncing marbles video

**TODO** narrative, possibly after rewriting the 

#### 2. Apply
[[toc](#table-of-contents)]

1. `[<lernact-prac>]`Write a function `randomPrime` that generates random primes in the range [1, 1000]. _How many primes are there in this range?_ Write a small program to demonstrate the operation.    

2. `[<lernact-prac>]`Add depth (aka distance) to your rain simulation, with more distant raindrops appearing dimmer and moving more slowly.  

3. `[<lernact-prac>]`Implement the `bouncing_marbles()` screensaver function and add it to your screensavers program matched to the `Shake` gesture.

4. `[<lernact-prac>]`**[Optional challenge, max 3 extra step points]** Modify your `rain()` function to show the raindrops falling at 45° to the right.  

#### 3. Present
[[toc](#table-of-contents)]

In the [programs](programs) directory:
1. Add your program from 5.2.1 with filename `microbit-program-5-2-1.js`.  
2. Add your program from 5.2.2 with filename `microbit-program-5-2-2.js`.  
3. Add your program from 5.2.3 with filename `microbit-program-5-2-3.js`.  
3. Add your program from 5.2.4 with filename `microbit-program-5-2-4.js`.  

In the [Lab Notebook](README.md):
1. Link to the program from 5.2.1.  
2. Link to a demo video showing the execution of the program from 5.2.1.  
3. Link to the program from 5.2.2.  
4. Link to a demo video showing the execution of the program from 5.2.2.  
5. Link to the program from 5.2.3.  
6. Link to a demo video showing the execution of the program from 5.2.3.  
7. Link to the program from 5.2.4.  
8. Link to a demo video showing the execution of the program from 5.2.4.  


### 6. Encapsulation    
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

   - the benefits of encapsulation  
   - functions
   - classes   
   - namespaces (`basic`, `input`, `game`, etc.)    

#### 2. Apply
[[toc](#table-of-contents)]
#### 3. Present
[[toc](#table-of-contents)]
   
### 7. Functions revisited  
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

   - Pass by value, pass by reference  
     ```javascript
     let arr : number[] = [1, 2, 3]

     function double(a : number[]) {
         for (let i = 0; i < arr.length; i ++)
             a[i] *= 2
     }

     double(arr)

     arr.forEach(function (value: number, index: number) {
         basic.showNumber(value)    
     })
     ```  
   - Function signatures & usage  
     - return a value  
     - return a modified argument  
     - change in place  

#### 2. Apply
[[toc](#table-of-contents)]
#### 3. Present
[[toc](#table-of-contents)]
   
### 8. Classes revisited    
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

   - object properties  
   - object literals: objects as dictionaries  
   - in JS, [classes are functions under the hood](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)  
   - TODO  
     - [Static TS](https://www.microsoft.com/en-us/research/publication/static-typescript/) implementation  
     - prototypes (too much)  
     - JS vs TS  

#### 2. Apply
[[toc](#table-of-contents)]
#### 3. Present
[[toc](#table-of-contents)]
   
