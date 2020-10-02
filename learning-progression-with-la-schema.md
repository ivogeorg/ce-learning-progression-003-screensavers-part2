# CPE 1040 - Fall 2020

This is learning progression 003 for the Fall 2020 installment of the course CPE 1040: Introduction to Computer Engineering at MSU Denver.

Table of Contents
=================

* [CPE 1040 \- Fall 2020](#cpe-1040---fall-2020)
  * [Learning Progression 003: Screensavers (Part 2)](#learning-progression-003-screensavers-part-2)
    * [5\. Randomized behavior](#5-randomized-behavior)
      * [1\. Study](#1-study)
        * [Importance of randomization](#importance-of-randomization)
        * [Pseudorandom numbers](#pseudorandom-numbers)
        * [Random functions](#random-functions)
        * [Randomization in the target program](#randomization-in-the-target-program)
        * [Bouncing marbles](#bouncing-marbles)
        * [Simulator infidelity](#simulator-infidelity)
        * [Thread unsafety](#thread-unsafety)
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
     // Example 5.1.1
     
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

Here is one more [video](https://msudenver.yuja.com/Dashboard/Permalink?authCode=1520695895&b=1815435&linkType=video) or the rain, this one with more pronounced variation in brightness.

##### Bouncing marbles

`[<lernact-rd>]`This section introduces a new screensaver, called Bouncing Marbles. Here is a [video](https://msudenver.yuja.com/Dashboard/Permalink?authCode=879279461&b=1815473&linkType=video).

This screensaver probably has the most complex randomization of all that we have seen so far. Just like `rain()`, which used a modulo mechanism to space out the calls to `fall()` for `Raindrop` objects, `bouncing_marbles` uses a modulo mechanism to (i) follow a trajectory with steps spaced at uneven intervals, and (ii) "release" a group of marbles at random intervals, so that they don't all fall together in parallel.

For reference, here is the `trajectory` array, which you may use yourself or modify, as you wish (you _will_ have to modify it for one of the **Optional challenges** below):
```javascript
// Example 5.1.2

enum Falling { Down = 0, Up }
enum Target { Simulator = 0, Microbit }
enum Trajectory { Vertical = 0, Duration, Heading }

this.trajectory = [                  // Heading not necessary but left in for readibility
            [0,  50, Falling.Down],
            [1, 120, Falling.Down],
            [2,  80, Falling.Down],
            [3,  30, Falling.Down],
            [4,  10, Falling.Up],
            [3,  40, Falling.Up],
            [2, 100, Falling.Up],
            [1, 160, Falling.Down],
            [2, 100, Falling.Down],
            [3,  50, Falling.Down],
            [4,  20, Falling.Up],
            [3,  70, Falling.Up],
            [2, 120, Falling.Down],
            [3,  90, Falling.Down],
            [4,  50, Falling.Up],
            [3, 120, Falling.Down],
            [4, 100, Falling.Up],
            [4, 120, Falling.Down]
        ]
```
Please, understand that the numbers in the second column are **not** milliseconds. They only make sense _relative to each other_, and then **only** if other program execution timing factors don't break the proportionality (e.g. extra computation for 5 marbles in comparison to fewer marbles).

Here is a code snippet (not an executable program) from the `move()` method of the `Marble` class:
```javascript
// Example 5.1.3

        if (this.counter % this.trajectory[this.step][Trajectory.Duration] == 0) {
            led.unplot(this.x, this.y)
            this.step ++
            if (this.step == this.trajectory.length) {
                this.active = false
            } else {
                this.y = this.trajectory[this.step][Trajectory.Vertical]
                led.plotBrightness(this.x, this.y, this.y == 4 ? 255 : this.brightness)
            }
        }

        this.counter ++
```
This snippet should help with understanding the function of the `Duration` column times.

Finally, the randomization of the falling marble sets (1 marble, 2 marbles, etc) in the main loop of the `bouncing_marbles` function has four main steps:
1. First, a `numMarbles` is assigned a random number by `randint(1, 5)`. This randomizes the number of marbles that will be released together.    
2. Second, the `numMarbles` are arranged in that many columns. This can be done with an array. This randomizes in which columns each of the number of picked marbles will be bouncing.  
3. Third, in a loop with a modulus mechanism, `numMarbles` `Marble` objects are created with random brightness and specing. This creates the impression that the marbles are _"realeased"_ at differen times.  
4. Fourth, the marble objects, which are themselves kept in an array, have their `move()` method called repeatedly in a round-robin fashion until they are done with their trajectories, at which time they are deleted.

##### Simulator infidelity

You may notice that when `while` loops instead of `basic.forever` for the main loop of your program, you may experience vast difference in execution speed between the micro:bit simulator in MakeCode and the micro:bit device. Usually the `while` loop is much faster than the `basic.forever`. More on this in a later step, but for the `bouncing_marbles()` program, because of having to cycle through multiple ball trajectories and the increased burden of the randomization, the simulator runs about 100 times faster than the device. If you want to use the simulator for development, while still being sure that it will run in a reasonable amount of time on the device, you may need to use a 100 multiplier for the `Duration` column of the `trajectory` array for the simulator, to slow it down relative to the device.

##### Thread unsafety

While we will cover `[<cept>]`_threads_ (that is, _execution threads_), `[<cept>]`_multithreading_, and `[<cept>]`_thread safety_ in a later step, we need to mention that you may encounter the [micro:bit error code](https://makecode.microbit.org/device/error-codes) 980 (`undefined`) when all of a sudden the program tries to call the `move()` method on a `null` (that is, undefined) `Marble` object. _(Error codes are shown on the LED matrix by first showing a `IconNames.Sad` and then scrolling the error number.)_ This is most likely caused by the _"thread unsafety"_ of the micro:bit and is not your fault. In short, the micro:bit has a task scheduler which breaks up longer code blocks into smaller portions to execute, alternating among several so broken-up code sequences. The ordering is somewhat random, which may cause portions of your program to run in the wrong order, relative to the order that you designed them to run in per your program structure.

#### 2. Apply
[[toc](#table-of-contents)]

1. `[<lernact-prac>]`Write a function `randomPrime` that generates random primes in the range [1, 1000]. _How many primes are there in this range?_ Write a small program to demonstrate the operation.    

2. `[<lernact-prac>]`Add depth (aka distance) to your rain simulation, with more distant raindrops appearing dimmer and moving more slowly.  

3. `[<lernact-prac>]`Implement the `bouncing_marbles()` screensaver function and add it to your screensavers program matched to the `Shake` gesture. Requirements:

4. `[<lernact-prac>]`**[Optional challenge, max 3 extra step points]** Modify your `rain()` function to show the raindrops falling at 45° to the right.  

5. `[<lernact-prac>]`**[Optional challenge, max 3 extra step points]** Modify your `bouncing_marbles()` to be able to show balls of different trajectories, simulating different elasticity coefficients of the marble material (think steel vs glass vs rubber resin).  

#### 3. Present
[[toc](#table-of-contents)]

In the [programs](programs) directory:
1. Add your program from 5.2.1 with filename `microbit-program-5-2-1.js`.  
2. Add your program from 5.2.2 with filename `microbit-program-5-2-2.js`.  
3. Add your program from 5.2.3 with filename `microbit-program-5-2-3.js`.  
4. Add your program from 5.2.4 with filename `microbit-program-5-2-4.js`.  
5. Add your program from 5.2.5 with filename `microbit-program-5-2-5.js`.  

In the [Lab Notebook](README.md):
1. Link to the program from 5.2.1.  
2. Link to a demo video showing the execution of the program from 5.2.1.  
3. Link to the program from 5.2.2.  
4. Link to a demo video showing the execution of the program from 5.2.2.  
5. Link to the program from 5.2.3.  
6. Link to a demo video showing the execution of the program from 5.2.3.  
7. Link to the program from 5.2.4.  
8. Link to a demo video showing the execution of the program from 5.2.4.  
9. Link to the program from 5.2.5.  
10. Link to a demo video showing the execution of the program from 5.2.5.  


### 6. Encapsulation    
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

##### Benefits of encapsulation  

`[<lernact-rd>]``[<cept>]`_Encapsulation_, in computer programming, is the enclosing of computational artifacts (data and code) into _named_ containers (here meaning distinct program regions) for the purpose of avoiding code duplication, program structuring and organization, and abstraction and simplification of the top-level program loop. Encapsulation is a widely used engineering principle, allowing practitioners to implement and users to utilize technology without knowing all the details. It is the greatest way to decrease the `[<cept>]`_cognitive load_ of both engineering work and technology usage.

The main encapsulation features of computer programming languages, and also available in the single-file MakeCode programming environment for the micro:bit, are `[<cept>]`_functions_, `[<cept>]`_classes_, and `[<cept>]`_namespaces_. Other environments allow for multi-file programs, thus making files part of the encapsulation and abstraction hierarchy of functions, classes, and namespaces. Other languages additionally organizing programs into `[<cept>]`_modules_, `[<cept>]`_packages_, and other top-level structural elements.

##### Functions

Functions encapsulate `[<cept>]`_code_. 

**TODO** input-output contract

##### Classes

Classes encapsulate code and data into `[<cept>]`_objects_ of programmer-defined `[<cept>]`_data types_. The term `[<cept>]`_class_ is synonymous with the term `[<cept>]`_type_, and the two terms are frequently used interchangeably. 

We know data have types. So, why do classes also encapsulate code along with the data? Because a type allows some operations and not others, thus making code a part of the definition of the type. For example, integers and strings allow completely different operations. Their operation sets are actually completely `[<cept>]`_disjoint_. The overloaded `+` operator does not change that, since it means _addition_ for integers and _concatenation_ for strings. We cannot meaningfully add strings the way we add integers, nor concatenate integers the way we concatenate strings. The `[<cept>]`_class methods_ comprise an exhaustive set of the operations that can be applied to objects of the class (aka type). In fact, the methods are a stronger determinant of the type than the data fields, as those can be changed without changing the input-output contract of the methods.

##### Namespaces

Namespaces encapsulate data, functions, and types (classes, `enum`, etc). Namespaces are just _named blocks_ (aka _named scopes_), and are declared as follows:
```javascript
// Example 6.1.1

namespace screensaver {
    // code and data, encapsulated in the namespace
    // some of it may be exported (see the next example)
}
```
Everything inside the curly braces `{}` is part of the namespace. A namespace is a `[<cept>]`_nested scope_. The `screensaver` namespace is nested in the top-level (aka `[<cept>]`_global_) scope.  

The "packages" of functions in the MakeCode environment (e.g. `basic`, `input`, `leds`, etc.) are all namespaces. If you are curious, you can explore the code of the [core MakeCode library](https://github.com/microsoft/pxt-microbit/tree/master/libs/core). The programming languages in which the files are written can most often be inferred from their `[<cept>]`_file extensions_:
1. `.ts` stands for TypeScript. _Remember that what we are programming in MakeCode is actually [TypeScript](https://makecode.com/language), despite calling it JavaScript._  
2. `.cpp` stands for C++.  
3. `.h` stands for header file (in C and C++).  
4. `.json` stands for JSON, a simple data format.  
5. `.jres` stands for a JSON file where project resources are stored.  
6. `.s` stands for assembly.  

Here is part of the `basic` namespace, contained in the [basic.ts](https://github.com/microsoft/pxt-microbit/blob/master/libs/core/basic.ts) file in the MakeCode codebase. The rest is in [basic.cpp](https://github.com/microsoft/pxt-microbit/blob/master/libs/core/basic.cpp), which is written in C++ and you don't have to read it.
```javascript
// Example 6.1.2

namespace basic {

    /**
     * Scroll a number on the screen. If the number fits on the screen (i.e. is a single digit), do not scroll.
     * @param interval speed of scroll; eg: 150, 100, 200, -100
     */
    //% help=basic/show-number
    //% weight=96
    //% blockId=device_show_number block="show|number %number" blockGap=8
    //% async
    //% parts="ledmatrix" interval.defl=150
    export function showNumber(value: number, interval?: number) {
        showString(Math.roundWithPrecision(value, 2).toString(), interval);
    }
}

/**
 * Pause for the specified time in milliseconds
 * @param ms how long to pause for, eg: 100, 200, 500, 1000, 2000
 */
function pause(ms: number): void {
    basic.pause(ms);
}

/**
 * Repeats the code forever in the background. On each iteration, allows other codes to run.
 * @param body code to execute
 */
function forever(a: () => void): void {
    basic.forever(a);
}
```
Notice the `export` keyword in front of the `showNumber`. This is what allows code outside the namespace to be called by prefixing the function name with the name of the namespace. You can see this in the declarations to `pause` and `forever`, which call `basic.pause` and `basic.forever`, respectively. Incidentally, this is why you can use `basic.forever` and `forever`, as well as `basic.pause` and `pause`, to get the same results.

#### 2. Apply
[[toc](#table-of-contents)]

1. `[<lernact-prac>]`Encasulate your screensaver code in a namespace `screensaver` so that the only code that is outside is the main program loop, which calls functions exported by the namespace. In particular:
   1. The function `coding()` should be exported.  
   2. The function `rain()` should be exported.  
   3. The function `freqBars()` should be exported.  
   4. The function `bouncing_marbles()` should be exported.  
   5. Any global data necessary for the screensaver or working functions should be _encapsulated in the functions_.  
   6. The class `Raindrop` should _not_ be exported.  
   7. The class `Marble` should _not_ be exported.  
   8. Any global data and code necessary for the main loop should remain outside of the namespace (e.g. the type `enum Mode`, the variables of type `Gesture` and `Gesture[]`, etc.).  
2. `[<lernact-prac>]`**[Optional challenge, max 10 extra step points]** Write a class to represent `[<cept>]`_unsigned_ binary integers as strings (e.g. `00011..` is 3<sub>10</sub>), called `UnsignedBinary`. Specifically:
   1. The strings contain two dots at the end as a subscript indicating the number is in binary.  
   2. The binary strings are at most 16 bits long (not counting the subscript).  
   3. The constructor takes a string `constructor(bin_str : string, carry : boolean)` and:  
      1. Rejects any characters that are not `0` or `1`. _Read the section on [exceptions](#exceptions)._  
      2. Calculates and stores the numerical value of the string. _Use **sum of powers**._ 
      3. Adds any leading zeros needed to complete the bit length to 16.  
      4. Stores the copy of the string with the suffix appended.  
      5. Stores the Boolean argument `carry`. (See the next points.)  
   4. The function `carry() : boolean` is implemented with the following specifications:
      1. It reports the value of an internal field which is `true` when the integer has overflowed and `false` otherwise. 
      2. An integer may overflow if it is a result of addition which has overflowed (e.g. `0001..` + `1111..` will overflow, so the result is `0000` and the carry is `true`; on the other hand, `0011..` + `0001` will not overflow so the result is `0111` and the carry is `false`).  
   4. The function `add(bi : BinaryInteger) : BinaryInteger` is implemented with the following specifications:
      1. It is called on a `BinaryInteger`, takes a second `BinaryInteger` as argument, and returns a `BinaryInteger`.  
      2. The returned object is a 16-bit result, with carry (if it overflows). 
      3. Addition in binary works as follows:  
         1. 0<sub>2</sub> + 0<sub>2</sub> = 0<sub>2</sub>.   
         2. 0<sub>2</sub> + 1<sub>2</sub> = 1<sub>2</sub>.   
         3. 1<sub>2</sub> + 0<sub>2</sub> = 10<sub>2</sub>.   
         4. 1<sub>2</sub> + 1<sub>2</sub> = 11<sub>2</sub>.   
   5. The function `show() : void` is implemented with the following specifications:
      1. It scrolls the binary string without the suffix.  
      2. Then, scrolls the suffix as two dots. Implement it with the animation feature of the micro:bit. Study the following example to find out how to do it:
         ```javascript
         // Example 6.1.3
         
         basic.showAnimation(
         `0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 0 0 1 0 0 0 0 0
          0 0 0 0 0 0 0 1 0 0 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 0 0 1 0 0 0 0 0 0 0 0 0 0
          0 0 1 0 0 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
          0 0 0 0 0 0 0 1 0 0 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 0 0 1 0 0 0 0 0 0 0 0 0 0
          0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 0 0 1 0 0 0 0 0`)
         ```
  
(blocks of 5x5)

#### 3. Present
[[toc](#table-of-contents)]

In the [programs](programs) directory:
1. Add your program from 6.2.1 with filename `microbit-program-6-2-1.js`.  
2. Add your program from 6.2.2 with filename `microbit-program-6-2-2.js`.  

In the [Lab Notebook](README.md):
1. Link to the program from 6.2.1.  
2. Link to a demo video showing the execution of the program from 6.2.1.  
3. Link to the program from 6.2.2.  
4. Link to a demo video showing the execution of the program from 6.2.2.  


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
   - Input-output contract  
   - Function signatures & usage  
     - return a value  
     - return a modified argument  
     - change in place  
   - naming  
   - recursive functions  

#### 2. Apply
[[toc](#table-of-contents)]

**TODO** Sort an array...
**TODO** Recursive functions... (Towers of Hanoi)

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
   - [Getters and setters](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Inheritance#Getters_and_Setters)  
   - defining a type:
     - data  
     - methods  
     - exceptions  

##### Exceptions

#### 2. Apply
[[toc](#table-of-contents)]

1. `[<lernact-prac>]`In a namespece `Complex`:
   1. Write a class to represent `[<cept>]`[_complex numbers_](https://www.mathsisfun.com/numbers/complex-numbers.html), called `ComplexNumber`.  
   2. Implement the function `Complex.add(a : ComplexNumber, b : ComplexNumber) : ComplexNumber`.   
   3. Implement the function `Complex.conjugate(a : ComplexNumber) : ComplexNumber`.   
   4. Implement the function `Complex.multiply(a : ComplexNumber, b : ComplexNumber) : ComplexNumber`.   
   5. Implement the function `Complex.showComplex(c : ComplexNumber) : void` to scroll a complex number as a string (e.g. `"-14-i31"`, `i`, `-i6`, `4+i7`, etc.).  
2. `[<lernact-prac>]`Using the `ComplexNumber` and the `Complex` methods, define the numbers `6+i7` and `-8-i5`, and:
   1. Add them. The program should scroll the first number, then show `+` for 500 ms, then scroll the second number, then show `=` for 500 ms, and finally scroll the result. _Note that you cannot use `scrollString` for the plus. You will have to define your own icon `Plus`._  
   2. Find their conjugates. For each number, the program should scroll the number, then show `C` for 500 ms, and finally scroll the result. _See the previous note._   
   3. Multiply them. The program should scroll the first number, then show a custon icon `Mult` (a X centered at (2, 2) and spanning the 3x3 square with origin at (1, 1)) for 500 ms, then scroll the second number, then show `=` for 500 ms, and finally scroll the result.  
3. `[<lernact-prac>]`**[Optional challenge, max 10 extra step points]** In the namespace `Complex`:
   1. Write a class to represent `[<cept>]`_fractions_, called `Fraction`. Fractions should show in their `[<cept>]`_reduced form_. If a `Fraction` object is defined in `[<cept>]`_irreduced form_, it should be reduced internally in the constructor. 
   2. Implement the function `Complex.showFraction(f : ComplexNumber) : void` to scroll a reduced fraction as a string (e.g. `"15/16"`, `7`, `13/14`, `9/5`, etc.).  
   3. Modify your `ComplexNumber` class to work with fractions instead of `[<cept>]`[_continuous real and imaginary coefficients_](https://proofwiki.org/wiki/Real_and_Imaginary_Part_Projections_are_Continuous). If the `[<cept>]`_real_ or `[<cept>]`_imaginary_ part of the a complex is an irreducible fraction, show the number with fractional (not continous real) `[<cept>]`_coefficients_.  
   4. Modify your `showComplex` function to show complex numbers with integers or fractional coefficients (e.g. `12/13+i4/7`, `-14-i31`, `9/5-i7`, etc.).  
   5. Divide `6+i7` by `-8-i5`. The program should scroll the first number, then show a custon icon `Div` (a / centered at (2, 2) and spanning the 3x3 square with origin at (1, 1)) for 500 ms, then scroll the second number, then show `=` for 500 ms, and finally scroll the result. _Note the result has to be in the canonical form A + iB, where A and B are either integers or fractions._    

#### 3. Present
[[toc](#table-of-contents)]
   
