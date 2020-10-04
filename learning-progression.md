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
        * [Benefits of encapsulation](#benefits-of-encapsulation)
        * [Functions](#functions)
        * [Classes](#classes)
        * [Namespaces](#namespaces)
      * [2\. Apply](#2-apply-1)
      * [3\. Present](#3-present-1)
    * [7\. Functions revisited](#7-functions-revisited)
      * [1\. Study](#1-study-2)
        * [Input\-output contract](#input-output-contract)
        * [Pass by value vs pass by reference](#pass-by-value-vs-pass-by-reference)
        * [Function naming](#function-naming)
        * [Recursive functions](#recursive-functions)
      * [2\. Apply](#2-apply-2)
      * [3\. Present](#3-present-2)
    * [8\. Classes revisited](#8-classes-revisited)
      * [1\. Study](#1-study-3)
        * [Classes are type definitions](#classes-are-type-definitions)
        * [Exceptions](#exceptions)
        * [Objects are dictionaries](#objects-are-dictionaries)
        * [Getters and setters](#getters-and-setters)
      * [2\. Apply](#2-apply-3)
      * [3\. Present](#3-present-3)


## Learning Progression 003: Screensavers (Part 2)
[[toc](#table-of-contents)]

This progression is the culmination of the first part of the course, in which we program the bear-bones micro:bit without any external circuitry attached and without communication features. We pull together all the programming language features and best practices that we introduced in the previous two learning progressions, to write a significantly larger target program over 12 steps. This will present the opportunity to learn about some of the design considerations a programmer makes when approaching a larger project. The progression is also going to dig a bit deeper into the _softare stack_ of the micro:bit, and uncover the ways it affects these considerations.

   
### 5. Randomized behavior  
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

##### Importance of randomization

We have used _randomization_ in fairly inconsequential ways so far, mostly to have somewhat unpredictable behavior of the output on the micro:bit 5 x 5 matrix. However, randomization is extremely important in science and engineering. Among other things, it allows generating important variation in test and diagnostics programs, achieving nearly-even coverage in probability distribution sampling, and good spreading in hash table storage. The full significance of randomization is well beyond the scope of this course.

##### Pseudorandom numbers

The numbers that the `random` functions return are actually [_pseudorandom_](https://en.wikipedia.org/wiki/Pseudorandom_number_generator), which means that an algorithm generates them in an extremely long evenly spread-out sequence. The number of elements in the whole sequence is so large, that in any specific location along it, there appears to be no relationship between nearby elements. Truly random functions are usually only achieved based on [random physical phenomena](https://www.random.org/randomness/).

##### Random functions

In MakeCode, we have access to three randomization functions:
1. `Math.random()` returns a pseudorandom floating-point (that is, a (possibly _truncated_) real number) number between 0 and 1. _Note that the value of 1 is [not included](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random)._  
2. `Math.randomBoolean()` returns `true` or `false` with the evenness of a pseudorandom generator. This is akin to a _coin toss_. Just like a coin toss, you cannot expect any kind of _alternation_ of `true` and `false`. They are evenly distributed (that is, there is an approximately even number of `true` and `false` value) only in the _limit_ of _infinite_ trials.  
3. `randint(min, max)` is provided as a convenience function in MakeCode and is not usually part of the canonical JavaScript library. It returns _signed integers_, that is the values of the numbers it returns are whole numbers, which can be negative, positive, and/or zero. Curiously, the ordering of the arguments is not enforced. That is, `randint(0, 4)` is functionally equivalent to `randint(4, 0)`. _Note that both min and max are included in the range from which pseudorandom integers are returned._  

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

This section introduces a new screensaver, called Bouncing Marbles. Here is a [video](https://msudenver.yuja.com/Dashboard/Permalink?authCode=879279461&b=1815473&linkType=video).

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

While we will cover _threads_ (that is, _execution threads_), _multithreading_, and _thread safety_ in a later step, we need to mention that you may encounter the [micro:bit error code](https://makecode.microbit.org/device/error-codes) 980 (`undefined`) when all of a sudden the program tries to call the `move()` method on a `null` (that is, undefined) `Marble` object. _(Error codes are shown on the LED matrix by first showing a `IconNames.Sad` and then scrolling the error number.)_ This is most likely caused by the _"thread unsafety"_ of the micro:bit and is not your fault. In short, the micro:bit has a task scheduler which breaks up longer code blocks into smaller portions to execute, alternating among several so broken-up code sequences. The ordering is somewhat random, which may cause portions of your program to run in the wrong order, relative to the order that you designed them to run in per your program structure.

#### 2. Apply
[[toc](#table-of-contents)]

1. Write a function `randomPrime` that generates random primes in the range [1, 1000]. _How many primes are there in this range?_ Write a small program to demonstrate the operation. _Hint: In a previous Step, you wrote a program to leave only the prime numbers in an array originally filled with the numbers [1, 100]. Extend this program to work for an array filled with the numbers [1, 1000]._    
2. Add depth (aka distance) to your rain simulation, with more distant raindrops appearing dimmer and moving more slowly.  
3. Implement the `bouncing_marbles()` screensaver function and add it to your screensavers program matched to the `Shake` gesture.  
4. **[Optional challenge, max 3 extra step points]** Modify your `rain()` function to show the raindrops falling at 45° to the right.  
5. **[Optional challenge, max 3 extra step points]** Modify your `bouncing_marbles()` to be able to show balls of different trajectories, simulating different elasticity coefficients of the marble material (think steel vs glass vs rubber resin).  

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

_Encapsulation_, in computer programming, is the enclosing of computational artifacts (data and code) into _named_ containers (here meaning distinct program regions) for the purpose of avoiding code duplication, program structuring and organization, and abstraction and simplification of the top-level program loop. Encapsulation is a widely used engineering principle, allowing practitioners to implement and users to utilize technology without knowing all the details. It is the greatest way to decrease the _cognitive load_ of both engineering work and technology usage.

The main encapsulation features of computer programming languages, and also available in the single-file MakeCode programming environment for the micro:bit, are _functions_, _classes_, and _namespaces_. Other environments allow for multi-file programs, thus making files part of the encapsulation and abstraction hierarchy of functions, classes, and namespaces. Other languages additionally organize programs into _modules_, _packages_, and other top-level structural elements.

##### Functions

Functions encapsulate _code_. They are _declared_ as _named executable_ sub-programs. A function's name allows the function to execute when when it is _called_. A functions operation can be _parameterized_, that is, the data they operate on can be abstracted. When a parameterized function is called, the caller specifies _arguments_, that is, data for all function parameters. Functions can also return a single instance of any data type. This makes functions encapsulated sub-programs which have input and output. Functions are said to define an _input-output contract_. This contract is said to be a function's _invariant_. The first line of a function, the one that specifies its parameters and return type, is called a _signature_. In JavaScript, functions are _first-class citizens_, meaning they can be used everywhere variables are used.  

```javascript
// Example 6.1.1

function add(a : number, b : number) : number {     // <-- signature
    return a + b                                    // <-- body
}

function randint(min : number, max : number) : number {
    return min + Math.floor(Math.random() * (max + 1 - min))
}

function pause(ms : number) : void {               // <-- no return value
    fiber_sleep(ms)
}

function randomPrime() : number {                  // <-- no parameters
    return primes[randint(0, primes.length-1)]
}

basic.forever(function () {                        // <-- a function without parameters or return value passed as the argument to another function
                               led.plot(2, 2); pause(100); led.unplot(2, 2); pause(100); 
                          }
             )

basic.forever(() =>       {                        // <-- equivalent to previous 
                               led.plot(2, 2); pause(100); led.unplot(2, 2); pause(100); 
                          }
             )

// and, in typical JS shorthand:
basic.forever(() => { led.plot(2, 2); pause(100); led.unplot(2, 2); pause(100); })
```

##### Classes

Classes encapsulate code and data into _objects_ of programmer-defined _data types_. The term _class_ is synonymous with the term _type_, and the two terms are frequently used interchangeably. 

We know data have types. So, why do classes also encapsulate code along with the data? Because a type allows some operations and not others, thus making code a part of the definition of the type. For example, integers and strings allow completely different operations. Their operation sets are actually completely _disjoint_. The overloaded `+` operator does not change that, since it means _addition_ for integers and _concatenation_ for strings. We cannot meaningfully add strings the way we add integers, nor concatenate integers the way we concatenate strings. The _class methods_ comprise an exhaustive set of the operations that can be applied to objects of the class (aka type). In fact, the methods are a stronger determinant of the type than the data fields, as those can be changed without changing the input-output contract of the methods.

##### Namespaces

Namespaces encapsulate data, functions, and types (classes, `enum`, etc). Namespaces are just _named blocks_ (aka _named scopes_), and are declared as follows:
```javascript
// Example 6.1.2

namespace screensaver {
    // code and data, encapsulated in the namespace
    // some of it may be exported (see the next example)
}
```
Everything inside the curly braces `{}` is part of the namespace. A namespace is a _nested scope_. The `screensaver` namespace is nested in the top-level (aka _global_) scope.  

The "packages" of functions in the MakeCode environment (e.g. `basic`, `input`, `leds`, etc.) are all namespaces. If you are curious, you can explore the code of the [core MakeCode library](https://github.com/microsoft/pxt-microbit/tree/master/libs/core). The programming languages in which the files are written can most often be inferred from their _file extensions_:
1. `.ts` stands for TypeScript. _Remember that what we are programming in MakeCode is actually [TypeScript](https://makecode.com/language), despite calling it JavaScript._  
2. `.cpp` stands for C++.  
3. `.h` stands for header file (in C and C++).  
4. `.json` stands for JSON, a simple data format.  
5. `.jres` stands for a JSON file where project resources are stored.  
6. `.s` stands for _assembler_ (aka _assembly language_). This is the form of directly-executable code your programs are translated (the actual term is _compiled_) into. Both terms only make sense in their original historical setting, but have remained in usage ever since.    

Here is part of the `basic` namespace, contained in the [basic.ts](https://github.com/microsoft/pxt-microbit/blob/master/libs/core/basic.ts) file in the MakeCode codebase. The rest is in [basic.cpp](https://github.com/microsoft/pxt-microbit/blob/master/libs/core/basic.cpp), which is written in C++ and you don't have to read it.
```javascript
// Example 6.1.3

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

1. Encapsulate your screensaver code in a namespace `screensaver` so that the only code that is outside is the main program loop, which calls functions exported by the namespace. In particular:
   1. The function `coding()` should be exported.  
   2. The function `rain()` should be exported.  
   3. The function `freqBars()` should be exported.  
   4. The function `bouncing_marbles()` should be exported.  
   5. Any global data necessary for the screensaver or working functions should be _encapsulated in the functions_.  
   6. The class `Raindrop` should _not_ be exported.  
   7. The class `Marble` should _not_ be exported.  
   8. Any global data and code necessary for the main loop should remain outside of the namespace (e.g. the type `enum Mode`, the variables of type `Gesture` and `Gesture[]`, etc.).  
2. **[Optional challenge, max 10 extra step points]** Write a class to represent _unsigned_ binary integers as strings (e.g. `00011..` is 3<sub>10</sub>), called `UnsignedBinary`. Specifically:
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
   5. The function `add(bi : BinaryInteger) : BinaryInteger` is implemented with the following specifications:
      1. It is called on a `BinaryInteger`, takes a second `BinaryInteger` as argument, and returns a `BinaryInteger`.  
      2. The returned object is a 16-bit result, with carry (if it overflows). 
      3. [Addition in binary](https://www.khanacademy.org/math/algebra-home/alg-intro-to-algebra/algebra-alternate-number-bases/v/binary-addition) works as follows:  
         1. 0<sub>2</sub> + 0<sub>2</sub> = 0<sub>2</sub>.   
         2. 0<sub>2</sub> + 1<sub>2</sub> = 1<sub>2</sub>.   
         3. 01<sub>2</sub> + 01<sub>2</sub> = 10<sub>2</sub>. _Note: Bit width grows by one._   
         4. 10<sub>2</sub> + 01<sub>2</sub> = 11<sub>2</sub>.   
         5. 011<sub>2</sub> + 001<sub>2</sub> = 100<sub>2</sub>. _Note: Bit width grows by one._   
   6. The function `show() : void` is implemented with the following specifications:
      1. It scrolls the binary string without the suffix.  
      2. Then, scrolls the suffix as two dots. Implement it with the animation feature of the micro:bit. Study the following example to find out how to do it:
         ```javascript
         // Example 6.1.4
         
         basic.showAnimation(
         `0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 0 0 1 0 0 0 0 0
          0 0 0 0 0 0 0 1 0 0 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 0 0 1 0 0 0 0 0 0 0 0 0 0
          0 0 1 0 0 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
          0 0 0 0 0 0 0 1 0 0 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 0 0 1 0 0 0 0 0 0 0 0 0 0
          0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 0 0 1 0 0 0 0 0`)
         ```
         _Hint: Blocks of 5x5._  

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

##### Input-output contract  
The input-output contract of a function consists of the particular transformation of the input data (that is, the arguments) into the output data (that is, the return value). Even functions which do not return anything have an input-output contract, as they may be modifying internal data structures (e.g. class methods). Functions which do not take any arguments are often called _routines_.

Formally, the input-output contract includes the number and types of function parameters as well as the type of the return value, if any. The shortest expression of the input-output contract is the function _signature_.

The input-output contract of a function is an _invariant_, meaning that it is strictly fixed, and _does not change_ even when the specific implementation of the function changes. A function is thus an _encapsulation of an input-output contract_.

Functions can also perform operations that are not strictly part of the input-output contract and are not otherwise explicitly stated. Such operations are therefore called _side effects_. Side effects relate to a very important feature of functional input-output contracts, namely that the latter guarantee that the program state is _consistent_ before and after the execution of a function (though it may briefly become inconsistent in the middle of its execution); side effects are potential holes in the input-output contract, and may introduce subtle inconsistencies that are hard to trace and fix.  

##### Pass by value vs pass by reference  

An important aspect of functions is how the arguments are passed to the function through the function call. There are two ways:
1. _Pass by value_ means that the value of the argument is a copy of the original variable (unless it is a _literal_ in which case it is not a copy of anything, for example the number `3`). In this case, the function can use the value but cannot modify the original variable. This method works for all _primitive_ data types.  
```javascript
// Example 7.1.1

let a : number = 2

function double(n : number) : void {
    n = 2*n
}

basic.showNumber(a)
double(a)
basic.showNumber(a)  // a retains its value

function double2(n : number) : number {
    return 2*n
}

a = double2(a)
basic.showNumber(a)  // only now does a change, because we assigned a new value to it
```  
<img src="images/pass-by-value.png" alt="Pass by value" width="600" />  

2. _Pass by reference_ means that the value of the argument is actually the _memory address_ (aka _pointer_, esp. in C/C++) of the data structure or object that is being passed. In this case, the function modifies the original variable. This method works for all arrays and class instances.  
```javascript
// Example 7.1.2

let arr : number[] = [1, 2, 3]

function double(a : number[]) : void {
    for (let i = 0; i < arr.length; i ++)
        a[i] *= 2
    }

double(arr)

arr.forEach(function (value: number, index: number) {
    basic.showNumber(value)                              // the elements of the array are now doubled!
})
   ```  
<img src="images/pass-by-reference.png" alt="Pass by reference" width="600" />  
   
##### Function naming

The argument over function naming has been long and intense, so we are only going to mention two very popular styles that we have already used:
1. _Camel case_ capitalizes every word in the function name except the first one (e.g. `frequencyBars` and `freqBars`).  
2. _Snake case_ is all lower-case and divides the words in the function name with underscores (e.g. `bouncing_marbles`).   

It doesn't matter that much which one you use, as long as you are consistent. This said, we have been inconsistent in naming our screensavers, but only so that we can explicitly talk about these two function naming conventions.  

##### Recursive functions  

Functions can call themselves. Since the signature of the function is already known by the time any call can be made from inside the function block, that call can be to the same function. This is called [_recursion_](https://en.wikipedia.org/wiki/Recursion_(computer_science)). Let's see an example:
```javascript
// Example 7.1.3

function factorial(a : number) : number {
    if (a == 1) {
        return 1
    } else {
        return a * factorial(a - 1)
    }
}

basic.showNumber(factorial(5))
```
Notice the following main points:
1. The recursive call is always with a "smaller" argument (e.g. a number minus one, or a subarray of the original array). The recursive call is solving a _smaller problem_.    
2. There is always a _termination condition_, in our case when a equals 1. This is the _smallest possible problem_ and it terminates the recursive call chain. Forgetting a termination condition will most often result in what is called an _infinite loop_.  

We need to note that recursive functions, while they look very elegant, are very wasteful of space (that is, memory), as they keep calling themselves until they reach the termination condition. All these copies of the function fill up the [_call stack_](https://medium.com/@ryanfarney/breaking-down-the-call-stack-e68b5633fbad), a special memory region that keeps function data for all outstanding function calls.    


#### 2. Apply
[[toc](#table-of-contents)]

1. Write a function `BubbleSort(arr : number[]) : void` to _sort_ _in place_ a numeric array, using the [Bubble Sort](https://en.wikipedia.org/wiki/Bubble_sort) algorithm. Requirements and notes:
   1. The function should take an array of numbers and sort it _in place_, without creating a new array.  
   2. The array should be sorted in _ascending_ order (e.g. `[1, 5, 17, 80, ...]`.  
   3. The function should not return anything.  
   4. There are two versions of Bubble Sort: [iterative and recursive](https://www.techiedelight.com/bubble-sort-iterative-recursive/). This function should implement an _iterative_ Bubble Sort. _Hint: The C syntax is closest to TypeScript, so you should read the C versions of the solutions in the referenced resource, for guidance._  
   5. An _algorithm_ is an exact series of precise (computational) steps that solves a certain problem or a class of problems. _Sorting_ is one of the most often performed computational activity in the modern world and there are many [sorting algorithms](https://en.wikipedia.org/wiki/Sorting_algorithm). Bubble Sort is not an efficient algorithm and is therefore not used in practice. However, it is great for developing intuition about the problem to be solved. For further intuition, watch this short [YouTube video](https://www.youtube.com/watch?v=kPRA0W1kECg&t=130s).
   6. Write a short program that uses your function. It should:
      1. Scroll "Input:" and then the input array. The input array should be `[14, 19, 3, 2, 0, 56, 12, 7, 90, 1, 11]`.     
      2. When done, it should scroll "Sorted:" and then the sorted array.  
2. **[Note: The solution to this problem will count for 2 extra step points]** Implement a _recursive_ `BubbleSort(arr : number[]) : void`. Run the same program you wrote to demo the iterative solution.  
3. **[Optional challenge, max 10 extra step points]** Design and implement the game [Tower of Hanoi](https://en.wikipedia.org/wiki/Tower_of_Hanoi) and a recursive solution. [Practice](https://www.mathsisfun.com/games/towerofhanoi.html) to get an intuition. Requirements and notes:
   1. Your program should be able to take a 3-peg game, with a tower of up to 7 stories at the leftmost peg, and solve it by moving the tower to the rightmost peg.  
   2. Your program should use **recursion** to solve the game.  
   3. Your program should have two display modes:  
      1. One mode will only scroll the start and end configurations (e.g. for a 3-story tower, `321|0|0` and `0|0|321`).  
      2. One that shows the start, all intermediate, and end configurations (e.g. for a 2-story tower, `21|0|0`, `2|1|0`, `0|1|2`,  and `0|0|21`).  
   4. Your implementation - classes, functions, etc. - is up to you. _Try to use what you have learned so far._  

#### 3. Present
[[toc](#table-of-contents)]
   
In the [programs](programs) directory:
1. Add your program from 7.2.1 with filename `microbit-program-7-2-1.js`.  
2. Add your program from 7.2.2 with filename `microbit-program-7-2-2.js`.  
3. Add your program from 7.2.3 with filename `microbit-program-7-2-3.js`.  

In the [Lab Notebook](README.md):
1. Link to the program from 7.2.1.  
2. Link to a demo video showing the execution of the program from 7.2.1.  
3. Link to the program from 7.2.2.  
4. Link to a demo video showing the execution of the program from 7.2.2.  
5. Link to the program from 7.2.3.  
6. Link to a demo video showing the execution of the program from 7.2.3.  
   
   
### 8. Classes revisited
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

##### Classes are type definitions

So, what are classes after all? We mentioned that they are _templates_ for _objects_, but that is too abstract and not well grounded. Most importantly, classes are _data type definitions_. A class defines a data type by defining its relationships with the rest of the data types in the computing environment (e.g. the `BrightPoint` class is essentially an array of three integers, `x`, `y`, and `b`) and constrains the operations that can be performed on objects of this type. Specifically, a class does that in the following ways:
1. Defines the **data** that an object of this type contains.  
2. Defines the **methods** that can be applied (that is, called on) objects of this type.   
3. If necessary, rejects **anomalous usage** (e.g. division by zero) with the help of an important language feature called [_exception handling_](https://basarat.gitbook.io/typescript/type-system/exceptions). 

##### Exceptions

_Exceptions_ are a mechanism for preventing disallowed usage of a data type and/or program because if such usage were to be allowed, the program state would become irrecoverably _inconsistent_. For example, knowing that division by zero is not mathematically defined, how can we rely on a program that has tried to divide by zero? Exceptions prevent this and similar anomalous behavior from ever happening and provide a recovery mechanism. Here is a canonical example **(note: does not work in makecode)**:
```javascript
// Example 8.1.1

class Fraction {
    num : number
    denom : number

    constructor(num : number, denom : number) {
        if (denom == 0) throw new Error("Denominator cannot be zero!")

        this.num = num
        this.denom = denom
    }

    show() {
        basic.showString(this.num.toString() + '/'+this.denom.toString())
    }
}

let f : Fraction = new Fraction(1, 2)
f.show()                                    // <-- this shows fine


try {
    let g : Fraction = new Fraction(5, 0)   // <-- this will "throw" the ValueError exception
    g.show()
} catch (e) {                               // <-- this is the exception handling machanism which can prevent the program being terminated
    basic.showString("Error: " + e.toString())
}
```
Notice the different parts:
1. The `throw` keyword is used to generate an exception, which is essentially a new `Error` object.  
2. The `try...catch` statement is used to enclose code that might thrown an exception, as the line trying to declare a `Fraction` object with a zero denominator.  
3. If the enclosed code does not throw an exception, the `g.show()` executes and the program continues after the `catch` block.  
4. If the enclosed code does throw an exception, it is "caught", processed (here, just printing its message), and then the program continues after the `catch` block.  

Unfortunately, the writers of MakeCode, though they [support exceptions](https://makecode.com/language), they have not exposed the `Error` class. And, despite the `try...catch` clause being accepted syntactically by the editor, it does not work as expected. However, the following code **does** work:
```javascript
// Example 8.1.2

class Fraction {
    num : number
    denom : number

    constructor(num : number, denom : number) {
        if (denom == 0) throw _py.VALUE_ERROR

        this.num = num
        this.denom = denom
    }

    show() {
        basic.showString(this.num.toString() + '/'+this.denom.toString())
    }
}

let f : Fraction = new Fraction(1, 2)
f.show()                                 // <-- this scrolls fine

let g : Fraction = new Fraction(5, 0)    // <-- the thrown exception terminates the program with a sad face 
g.show()                                 // <-- never reached
```
So, `throw` works to give you minimal functionality to reject unsupported input in the constructor. _(Incidentally, the debate over whether a constructor should throw exceptions is also a very heated one!)_


##### Objects are dictionaries

Another way to look at classes is to focus on the objects themselves and realize that they are _dictionaries_. Dictionaries in computing are data structures which match _keys_ with _values_. They are also called _maps_ or _hash tables_, but they are just collections of data pairs. The value can be anything, literally anything, including the same value throughout the dictionary. However, the keys have to be _unique_. There cannot be two different key-value pairs with the same key.

The keys of the objects are their field names (e.g. the `x`, `y`, and `b` of `BrightPoint` objects) and the values are their specific values, which are different for every object. This is the essence of objects - their independent _lifecycle_ - and correspondingly a very valid perspective on what classes are, as object templates. In fact, in JavaScript, this is the dominating perspective!

If you want to read the implementation details of the JavaScript-like language that we use in MakeCode, called Static TypeScript (STS), read their [paper](https://www.microsoft.com/en-us/research/publication/static-typescript/). It's a fascinating read, once you get used to the terminology, and provides an enticing peek into the software stack of the micro:bit.  


##### Getters and setters

In the line of the objects-as-dictionaries perspective on classes that we just discussed, we should mention a very common pattern for class methods, namely the so called [_getters and setters_](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Inheritance#Getters_and_Setters), (aka `get` and `set` accessors). These are methods with specific syntax that are just there to allow us to get and set the values in the "object dictionary". Here's an example:
```javascript
// Example 8.1.3

class Point {
    private _x : number
    private _y : number

    constructor(x : number, y : number) {
        this._x = x
        this._y = y
    }

    get x() {
        return this._x
    }

    set x(new_x : number) {
        this._x = new_x
    }

    get y() {
        return this._y
    }

    set y(new_y : number) {
        this._y = new_y
    }
}

let p0 : Point = new Point(1, 2)

basic.showNumber(p0.x)            // <-- call the getter of `_x`
basic.pause(100)
p0.x = 5                          // <-- call the setter of `_x`
basic.showNumber(p0.x)
```
Notice the following:
1. The `get` and `set` keywords.  
2. The underscore in `_x` and `_y` to avoid duplicate names with the getters and setters of `x` and `y`.
3. The `private` keyword in front of `_x` and `_y`, which disallows calls like `p0._x` (that is, direct access to the object fields). _Hiding_ the implementation of the functionality encapsulated in a class is the reason for the popularity of getters and setters in the first place. _The detailed considerations belong to the theory and history of object-oriented programming._      


#### 2. Apply
[[toc](#table-of-contents)]

1. In a namespece `Complex`:
   1. Write a class to represent [_complex numbers_](https://www.mathsisfun.com/numbers/complex-numbers.html), called `ComplexNumber`.  
   2. Implement the function `Complex.add(a : ComplexNumber, b : ComplexNumber) : ComplexNumber`.   
   3. Implement the function `Complex.conjugate(a : ComplexNumber) : ComplexNumber`.   
   4. Implement the function `Complex.multiply(a : ComplexNumber, b : ComplexNumber) : ComplexNumber`.   
   5. Implement the function `Complex.showComplex(c : ComplexNumber) : void` to scroll a complex number as a string (e.g. `"-14-i31"`, `i`, `-i6`, `4+i7`, etc.).  
2. Using the `ComplexNumber` and the `Complex` methods, define the numbers `6+i7` and `-8-i5`, and:
   1. Add them. The program should scroll the first number, then show `+` for 1500 ms, then scroll the second number, then show `=` for 1500 ms, and finally scroll the result. _Note that you cannot use `showString` for the plus. You will have to define your own icon `Plus`._  
   2. Find their conjugates. For each number, the program should scroll the number, then show `C` for 1500 ms, and finally scroll the result. _See the previous note._   
   3. Multiply them. The program should scroll the first number, then show a custom icon `Mult` (a X centered at (2, 2) and spanning the 3x3 square with origin at (1, 1)) for 1500 ms, then scroll the second number, then show `=` for 1500 ms, and finally scroll the result.  
3. **[Optional challenge, max 10 extra step points]** In the namespace `Complex`:
   1. Write a class to represent _fractions_, called `Fraction`. Fractions should show in their _reduced form_. If a `Fraction` object is defined in _irreduced form_, it should be reduced internally in the constructor. 
   2. Implement the function `Complex.showFraction(f : ComplexNumber) : void` to scroll a reduced fraction as a string (e.g. `"15/16"`, `7`, `13/14`, `9/5`, etc.).  
   3. Modify your `ComplexNumber` class to work with fractions instead of [_continuous real and imaginary coefficients_](https://proofwiki.org/wiki/Real_and_Imaginary_Part_Projections_are_Continuous). If the _real_ or _imaginary_ part of the a complex is an irreducible fraction, show the number with fractional (not continous real) _coefficients_.  
   4. Modify your `showComplex` function to show complex numbers with integers or fractional coefficients (e.g. `12/13+i4/7`, `-14-i31`, `9/5-i7`, etc.).  
   5. Divide `6+i7` by `-8-i5`. The program should scroll the first number, then show a custom icon `Div` (a / centered at (2, 2) and spanning the 3x3 square with origin at (1, 1)) for 500 ms, then scroll the second number, then show `=` for 500 ms, and finally scroll the result. _Note the result has to be in the canonical form A + iB, where A and B are either integers or fractions._    

#### 3. Present
[[toc](#table-of-contents)]
   
In the [programs](programs) directory:
1. Add your program from 8.2.2 with filename `microbit-program-8-2-2.js`.  
2. Add your program from 8.2.3 with filename `microbit-program-8-2-3.js`.  

In the [Lab Notebook](README.md):
1. Link to the program from 8.2.2.  
2. Link to a demo video showing the execution of the program from 8.2.2.  
3. Link to the program from 8.2.3.  
4. Link to a demo video showing the execution of the program from 8.2.3.  

