# CPE 1040 - Fall 2020

This is learning progression 003 for the Fall 2020 installment of the course CPE 1040: Introduction to Computer Engineering at MSU Denver.

Table of Contents
=================

* [CPE 1040 \- Fall 2020](#cpe-1040---fall-2020)
  * [Learning Progression 003: Screensavers (Part 1)](#learning-progression-003-screensavers-part-1)
    * [1\. Arrays revisited](#1-arrays-revisited)
      * [1\. Study](#1-study)
        * [Arrays](#arrays)
        * [Array methods](#array-methods)
        * [Multi\-dimensional arrays](#multi-dimensional-arrays)
      * [2\. Apply](#2-apply)
      * [3\. Present](#3-present)
    * [2\. Screensavers](#2-screensavers)
      * [1\. Study](#1-study-1)
        * [Overview](#overview)
        * [Code writing](#code-writing)
        * [Rain](#rain)
        * [Frequency bar](#frequency-bar)
      * [2\. Apply](#2-apply-1)
      * [3\. Present](#3-present-1)
    * [3\. Program structure](#3-program-structure)
      * [1\. Study](#1-study-2)
        * [Overall program structure](#overall-program-structure)
        * [Global variables](#global-variables)
        * [Function and class declarations](#function-and-class-declarations)
        * [Event handler registrants](#event-handler-registrants)
        * [Main program (forever loop)](#main-program-forever-loop)
        * [Proper indentation](#proper-indentation)
      * [2\. Apply](#2-apply-2)
      * [3\. Present](#3-present-2)
    * [4\. Divide &amp; conquer: program decomposition](#4-divide--conquer-program-decomposition)
      * [1\. Study](#1-study-3)
        * [Two modes: working &amp; asleep](#two-modes-working--asleep)
        * [Sub\-programs](#sub-programs)
        * [Matching gestures and screensavers](#matching-gestures-and-screensavers)
      * [2\. Apply](#2-apply-3)
      * [3\. Present](#3-present-3)
    * [5\. Randomized behavior](#5-randomized-behavior)
      * [1\. Study](#1-study-4)
      * [2\. Apply](#2-apply-4)
      * [3\. Present](#3-present-4)
    * [6\. Encapsulation](#6-encapsulation)
      * [1\. Study](#1-study-5)
      * [2\. Apply](#2-apply-5)
      * [3\. Present](#3-present-5)
    * [7\. Functions revisited](#7-functions-revisited)
      * [1\. Study](#1-study-6)
      * [2\. Apply](#2-apply-6)
      * [3\. Present](#3-present-6)
    * [8\. Classes revisited](#8-classes-revisited)
      * [1\. Study](#1-study-7)
      * [2\. Apply](#2-apply-7)
      * [3\. Present](#3-present-7)
    * [9\. Code reading](#9-code-reading)
      * [1\. Study](#1-study-8)
      * [2\. Apply](#2-apply-8)
      * [3\. Present](#3-present-8)
    * [10\. Iterative development with Github](#10-iterative-development-with-github)
      * [1\. Study](#1-study-9)
      * [2\. Apply](#2-apply-9)
      * [3\. Present](#3-present-9)
    * [11\. Reactive system](#11-reactive-system)
      * [1\. Study](#1-study-10)
      * [2\. Apply](#2-apply-10)
      * [3\. Present](#3-present-10)
    * [12\. Matrix dynamics](#12-matrix-dynamics)
      * [1\. Study](#1-study-11)
      * [2\. Apply](#2-apply-11)
      * [3\. Present](#3-present-11)
## Learning Progression 003: Screensavers (Part 1)
[[toc](#table-of-contents)]

This progression is the culmination of the first part of the course, in which we program the bear-bones micro:bit without any extenral circuitry attached and without communication features. We pull together all the programming language features and best practices that we introduced in the previous two learning progressions, to write a significantly larger target program over 12 steps. This will present the opportunity to learn about some of the design considerations a programmer makes when approaching a larger project. The progression is also going to dig a bit deeper into the _softare stack_ of the micro:bit, and uncover the ways it affects these considerations.

### 1. Arrays revisited  
[[toc](#table-of-contents)]

#### 1. Study  
[[toc](#table-of-contents)]

##### Arrays
[[toc](#table-of-contents)]

`[<lernact-rd>]`We have already seen how versatile and powerful arrays can be in a program, allowing us to achieve complex program behavior far more easily than if arrays weren't available to the programming language. Let's quickly review what arrays are. An `[<cept>]`_array_ is an _ordered sequence_ of elements _of the same type_. Because it holds more than one piece of data, it is also called a `[<cept>]`[_data structure_](https://en.wikipedia.org/wiki/Data_structure). Like any other variable and function, an array has a _name_. This name can be used to refer not only to the whole array, but to individual elements of the array. Because they are ordered, they can be referenced by `[<cept>]`_index_, that is, by their place in the order, using the `[<cept>]`_selection operator_ `[]` (opening and closing square bracket). Let's take a look:
```javascript
// Example 1.1.1

let firstPrimes : number[] = [1, 3, 5, 7, 11, 13, 17, 19, 23, 31]

let favoritePrime : number = firstPrimes[3]  // selects 7

for (let i = 0; i < firstPrimes.length; i ++) {
    if (firstPrimes[i] != favoritePrime) {
        basic.showNumber(firstPrimes[i])
    } else {
        basic.showString("My favorite prime is " + favoritePrime.toString() + "!")
    }
}
```
As we can see, the `[]` operator is used heavily with arrays. In fact, that's how you can spot array variables in a program even if you are not looking at the array declaration. Let's enumerate its uses:
1. In `let firstPrimes...`, it indicates an array data type by attaching to the right of the `[<cept>]`_base type_. We are declaring an array of numbers, so the base time is `number`, and the type of the array variable `firstPrimes` is `number[]`. The single pair of brackets `[]` also indicates that this is `[<cept>]`_unidimensional_ array.  
2. In `let favoritePrime...`, it selects a particular element of the array, in this case the one with index 3, making it the forth element. Note that the array index always starts at 0.  
3. In `firstPrimes[i]`, it picks out the element, the index of which is equal to the current value of the loop variable `i`. So, the `[]` operator admits `[<cept>]`_expressions_ between the brackets, as long as they evaluate to an `[<cept>]`_integer_ value. That is, the result of evaluating the expression has to be a whole number. In addition, the integer has to be in the index `[<cept>]`_range_ of the array. That is, the number between the brackets has to be among the valid indices of the array. In our case, the range is [0, 9].  

##### Array methods
[[toc](#table-of-contents)]

`[<lernact-rd>]`The usefulness of the arrays doesn't stop with the ability to keep a collection of data together. Arrays come with many useful `[<cept>]`_methods_ and `[<cept>]`_properties_ for manipulating the elements of the collection or the whole collection itself. In Example 1.1.1, we already encountered the `length` property. The best way to explore methods in MakeCode is to declare an array variable, invoke the dropdown by using the `.` selection operator to pick a method or property, 

<img src="images/method-dropdown.png" alt="MakeCode editor method dropdown" width="400"/>  

and then hover over the name to invoke the documentation popup.

<img src="images/doc-popup.png" alt="MakeCode editor method dropdown" width="400"/>  

Before we review the array methods, we need to point out what the difference is between methods and properties. Looking back at the material on classes in the previous learning progression, we can see that the array methods look like class methods and the `length` property as a `[<cept>]`_class field_, that is, a data point. The actual implementation is a bit more complicated, due to the need for MakeCode to support devices other than the micro:bit, but from our purposes, we can think of these methods and property as defined in the class `Array`. _Methods_ are called like functions, with parentheses `()`, as in `arr.reverse()`, while _properties_ are called like data fields, without parentheses, as in `arr.length`.

Use the following functions to look at the contents of arrays in the next examples:
```javascript
// Example 1.1.2

function showNumericArray(a : number[]) {
   for (let i=0; i<a.length; i++) {
      basic.showNumber(a[i])
   }
}

function showStringArray(a : string[]) {
   for (let i=0; i<a.length; i++) {
      basic.showString(a[i])
   }
}
```

Now, let's look at the available methods systematically, grouping them by general function:
1. Content methods insert, remove, and replace elements of the array, changing its contents. These are (Note: Arguments, if any, are omitted for brevity.):
   - `push()`, also known as _"append"_, adds an element at the end of the array.    
   - `pop()` removes the last element and returns it.  
   - `insertAt()` inserts an element at an index.  
   - `removeAt()` removes an element at an index.    
   - `set()` sets the value of an element at an index.  
   - `removeElement()` removes the first occurrence of an element.    
   - `fill()` Read the JS documentation on its [usage](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/fill).)  
   - `shift()` remove an element from the beginning of the array and return the new length.  
   - `unshift()` add an element at the beginning of the array and return the new length.  
   - `splice()` removes a number of elements from an array, starting at an index.  
   ```javascript
   // Example 1.1.3
   let a : number[] = [5, 8, 12, 91, 41, 0, 22]
   a.push(13)
   showNumericArray(a)
   
   basic.showNumber(a.pop())
   
   a.insertAt(2, 56)
   showNumericArray(a)

   basic.showNumber(a.removeAt(1))
   
   a.set(0, 22)
   showNumericArray(a)
   
   if (a.removeElement(22)) {
       basic.showString("Successfully removed!") 
       showNumericArray(a)
   }

   a.fill(87, 2, 4)
   showNumericArray(a)
   ```
2. Accessor methods:
   - `get()` return the value of the element at an index. `a.get(5)` is equivalent to `a[5]`.  
   - `indexOf()` returns the index of an element, if found, or `-1`, if not found.  
   - `find()` returns the value of the first element in the array that satisfies the boolean criterion function passed as argument.  
   - `slice()` returns a section of an array between two indices as another array.  
   ```javascript
   // Example 1.1.4
   let a : number[] = [5, 8, 12, 91, 41, 0, 22]
   basic.showNumber(a.indexOf(91))  // returns 3
   
   basic.showNumber(a.indexOf(87))  // returns -1 (no 87 in the array)
   
   function odd(a : number, index : number) { return a % 2 == 1 }  // the second parameter is required though not used
   basic.showNumber(a.find(odd))
   
   let c = a.slice(2, 5)  // the end index, here 5, is not included
   showNumericArray(c)
   ```
3. Order methods, which change the order of the elements:
   - `reverse()` permanently reverses the order of the elements of the array on which it was called.  
   - `sort()` returns the array sorted `[<cept>]`_in place_, meaning the array remains sorted after the call. `reverse()` is also in-place.    
   ```javascript
   // Example 1.1.5
   
   let a : number[] = [5, 8, 12, 91, 41, 0, 22]
   a.reverse()
   showNumericArray(a)
   showNumericArray(a.sort())  // note that the array a is now sorted
   ```
4. Methods on two arrays:
   - `concat()` returns a new array with the argument array concatenated (that is, appended) to the array on which it was called.  
   ```javascript
   // Example 1.1.6
   
   let a : number[] = [5, 8, 12]
   let b : number[] = a.concat([91, 41, 0, 22])
   showNumericArray(b)
   ```
5. String-specific methods:
   - `join()`, for `string` arrays, concatenates the elements, delimited by a `[<cept>]`_separator_.  
   ```javascript
   // Example 1.1.7

   let a : string[] = ["Learning", "progression", "003"]
   showStringArray(b)
   basic.showString(a.join(" "))  // the separator is a space (that is, " ")
   ```
6. Boolean methods:
   - `every()` tests of all elements satisfy a criterion specified as a funciton.   
   - `some()` tests of all elements satisfy a criterion specified as a funciton.  
   ```javascript
   // Example 1.1.8

   let a : number[] = [5, 8, 12]

   function odd(a : number, index : number) { return a % 2 == 1 }  // the second parameter is required though not used
   function even(a : number, index : number) { return a % 2 == 0 }  // the second parameter is required though not used

   if (a.every(odd))
      basic.showString("All odd!")
   else if (a.every(even))
      basic.showString("All even!")
   else
      basic.showString("Even and odd!")
   ```
7. General methods applying a `[<cept>]` callback function on every element. (Notice that `find()`, `every()`, and `some()` also take callbacks as arguments, but they have very specific function.)
   - `map()` applies a callback function to each element of an array and returns the resulting array. The original array is not modified.  
   - `forEach()` applies a callback function to each element of an array.  The original array is not modified.  
   - `reduce()` applies a callback function to each element, while aggregating the result.  
   ```javascript
   // Example 1.1.9
   let words : string[] = ["HIPY", "PAPY", "BTHUTHDTH", "THUTHDA", "BTHUTHDY"]

   showStringArray(words.map(function (value: string, index: number) {
       return value.toLowerCase()
   }))

   let shortWords : string[] = ["hop", "baby", "nose", "scat", "grog"]

   shortWords.forEach(function (value: string, index: number) {
       let numeral : string
       switch (index) {
           case 0:
           numeral = "first"
           break
           case 1:
           numeral = "second"
           break
           case 2:
           numeral = "third"
           break
           default:
           numeral = `${ index + 1 }th`
           break
       }
       basic.showString(`The ${ numeral }th element is ${ value }.`)
   })
   
   let a : number[] = [4, 9, 2, 4]

   basic.showString(`The sum of array elements is ${ 
       a.reduce(
           function (previousValue: number, currentValue: number, currentIndex: number) {
               return previousValue + currentValue
           }, 
           null) 
   }`)
   ```
##### Multi-dimensional arrays
[[toc](#table-of-contents)]

`[<lernact-rd>]`Multi-dimensional arrays are _arrays of arrays_. In other words, the base type of multi-dimensional array is also a (multi-dimensional) array. The number of dimensions is indicated by the number of `[]` pairs after the base type of the innermost array, as in `let threeDimensionalGrid : number[][][]`. The indexing of elements works by level, from the outermost to the innermost, as in `threeDimensionalGrid[5][4][10]` references the value of _the element of index 10 of the element of index 4 of the element of index 5_. This is a lot easier to understand with an example:
```javascript
// Example 1.1.10

let path = [
    [0, 0], [1, 0], [2, 0], [3, 0], [4, 0], // top row to the right
    [4, 1], [4, 2], [4, 3], [4, 4],         // right column down
    [3, 4], [2, 4], [1, 4], [0, 4],         // bottom row to the left
    [0, 3], [0, 2], [0, 1],                 // left column up
    [1, 1], [2, 1], [3, 1],                 // top but 1 row to the right (remaining headitions)
    [3, 2], [3, 3],                         // right but 1 column down (remaining headitions)
    [2, 3], [1, 3],                         // bottom but 1 row to the left (rem head)
    [1, 2],                                 // left but 1 col up (rem head)
    [2, 2]                                  // center
]

const SNAKE_LEN: number = 7                 // snake length parameter
let snake: number[] = []                    // snake represented by trajectory indices
for (let i = - SNAKE_LEN; i < 0; i++)
    snake.push(i)                           // populate snake array

// the snake goes back and forth along the path
basic.forever(function () {
    let head: number = 0
    let tail: number
    while (head < 25) {
        led.plot(path[head][0], path[head][1])
        snake.push(head++)
        tail = snake.removeAt(0)
        if (tail >= 0)
            led.unplot(path[tail][0], path[tail][1])
        basic.pause(200)
    }

    // reverse the snake "in place"
    head = tail
    tail = 24
    while (tail > 0) {
        if (head >= 0)
            led.plot(path[head][0], path[head][1])
        snake.insertAt(0, head--)
        tail = snake.pop()
        led.unplot(path[tail][0], path[tail][1])
        basic.pause(200)
    }
})
```
Things to notice here:
1. The `path` variable is a two-dimensional array representing the trajectory of the snake. It contains 25 (x, y) positions on the 5x5 grid, which are so arranged that the `snake` travels along a tightening spiral, touching every position in the grid.  
2. The `snake` itself is a one-dimensional array of 7 consecutive indices into the the `path` array. `head` is the index of the snake's front, and `tail` is the index of the snake's back. The snake travels head forward. So, to show the snake, we use calls to `led.plot()` with coordinates `path[head][0]` for the x parameter and `path[head][1]` for the y parameter.  
3. Because the snake is moving, we advance the `head` and remove at the `tail`.
    
#### 2. Apply  
[[toc](#table-of-contents)]

1. `[<lernact-prac>]`Create an array of the numbers 1 to 100, inclusive. Do not waste your time typing the numbers. Write a loop and use the appropriate array method to add elements. In a separate loop, display the last 17 numbers.  
2. `[<lernact-prac>]`Using the method of the [Sieve of Eratosthenes](https://www.smartickmethod.com/blog/math/operations-and-algebraic-thinking/divisibility/prime-numbers-sieve-eratosthenes/), update your program from 1.2.1 to go through the array and leave only the prime numbers. Show the remaining numbers in a loop.  
3. `[<lernact-prac>]`Modify the program from Example 1.1.10 so that the snake appears from the center, goes to the top left corner and returns back.

#### 3. Present  
[[toc](#table-of-contents)]

In the [programs](programs) directory:
1. Add your program from 1.2.1 with filename `microbit-program-1-2-1.js`.  
2. Add your program from 1.2.2 with filename `microbit-program-1-2-2.js`.  
3. Add your program from 1.2.3 with filename `microbit-program-1-2-3.js`.  

In the [Lab Notebook](README.md):
1. Link to the program from 1.2.1.  
2. Link to a demo video showing the execution of the program from 1.2.1.  
3. Link to the program from 1.2.2.  
4. Link to a demo video showing the execution of the program from 1.2.2.  
5. Link to the program from 1.2.3.  
6. Link to a demo video showing the execution of the program from 1.2.3.  


### 2. Screensavers   
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

##### Overview

`[<lernact-rd>]`This progression has a significant target program as an end goal. This allows us to visit programming topics that would otherwise not arise in a sequence of small few-line example programs. Some of these topics concern programming as a process (e.g. program decomposition, encapsulation, program structure), others have to do with computer resources (e.g. efficient coding, achieving desired behavior within the memory and processor speed limitations of the target device), and still others address user interface design (e.g. responsiveness, intuitive dynamics). The details of the implementation of the computer's software stack may bear directly on any of these topics. Exploring these is the point of this progression, and thus it is the culmination of the first part of the course, in which we only program the bear micro:bit, without any external circuitry.

The target program consists of several smaller sub-programs, which jointly simpulate a computer, on which a programmer codes, and on which various screensavers display when the computer is "asleep". See this overview [video of the overall program operation](https://msudenver.yuja.com/Dashboard/Permalink?authCode=1778867948&b=1761370&linkType=video), including the coding simulation in work mode and two sceensavers, one simulating rain and the other a frequency bar.

##### Code writing

Here is a longer [video of the code-writing simulation](https://msudenver.yuja.com/Dashboard/Permalink?authCode=5150693&b=1799587&linkType=video). Notice the scrolling after the first page is exhausted.

##### Rain

Here is a longer [video of the rain simulation screensaver](https://msudenver.yuja.com/Dashboard/Permalink?authCode=1875257468&b=1799524&linkType=video). Notice how the "farther" (dimmer) raindrops, which look like they are falling in the background, are falling _faster_. Similarly, the ones at the forefront, are brightest and fall slowest. This is what you would see if you are looking at rain.

##### Frequency bar

Here is a longer [video of the frequency bar simulation screensaver](https://msudenver.yuja.com/Dashboard/Permalink?authCode=45885861&b=1799554&linkType=video). Notice how the highest point to which a frequency column rises in a single beat lingers lit up until the column fades down completely. This is common behavior of frequency bars, in which the overall frequency envelope of the music signal is displayed.
     
#### 2. Apply
[[toc](#table-of-contents)]

1. `[<lernact-prac>]`Write a standalone program to simulate the code writing sub-program. Guidelines and hints:
   1. First, write a program that can draw 5 "program lines" of different lengths, from left to right.  
   2. Notice that at all times, there are _at most_ 5 lines on the screen. Does that perhaps suggest an array of line lengths? How would you add and remove lines to keep it at most 5-element long?  
   3. Finally, notice that while writing the "first page" of the program, there is no scrolling until the fifth line is reached. How would you modify your program to achieve this?  
   
2. `[<lernact-prac>]`Write a standalone program to simulate rain for one of the screensavers. Guidelines and hints:
   1. First, write a program to have LED "raindrops" fall from top to bottom, and disappear at the bottom of the screen.  
   2. Now make them vary in brightness.  
   3. Now think how you can have some of the go faster than others, and match that with the brightness. _You don't have to get this right in this step, but it will help to think about it and generate some ideas._
   
3. `[<lernact-prac>]`Write a standalone program to simulate a frequency bar for one of the screensavers. Guidelines and hints:
   1. First, write a program that can plot LED columns _from botton to top_ and back. _Note the vertical coordinate grows from top to bottom, so you will need to invert it._    
   2. Randomize the height the way you randomized the line length in the code-writing simulation, and make the highest point show at highest brightness. _Can you see another application of an array of highest point vertial coordinates?_    
   3. How would you make the column retreat down while the highest point stays until the columns is gone, and only then turns off?  

#### 3. Present
[[toc](#table-of-contents)]

In the [programs](programs) directory:
1. Add your program from 2.2.1 with filename `microbit-program-2-2-1.js`.  
2. Add your program from 2.2.2 with filename `microbit-program-2-2-2.js`.  
3. Add your program from 2.2.3 with filename `microbit-program-2-2-3.js`.  

In the [Lab Notebook](README.md):
1. Link to the program from 2.2.1.  
2. Link to a demo video showing the execution of the program from 2.2.1.  
3. Link to the program from 2.2.2.  
4. Link to a demo video showing the execution of the program from 2.2.2.  
5. Link to the program from 2.2.3.  
6. Link to a demo video showing the execution of the program from 2.2.3.  


### 3. Program structure  
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

##### Overall program structure

`[<lernact-rd>]`In a large program, proper structure is essential. While the particular structure is somewhat arbitrary, general guidelines and conventions are quick to appear in any programming domain. One important factor in coming up with a good structure for a large program is whether the program can contain multiple files or just a single one. The MakeCode micro:bit environment allows for only one user-defined source file at a time, with all the rest of the functionality (language runtime, device runtime, MakeCode and user-defined packages) all come precompiled and are not modifiable from inside the user program. For our single-file micro:bit programs, the following structure has emerged:

```
 _____ _       _           _                   _       _     _            
|  __ \ |     | |         | |                 (_)     | |   | |           
| |  \/ | ___ | |__   __ _| | __   ____ _ _ __ _  __ _| |__ | | ___  ___  
| | __| |/ _ \| '_ \ / _` | | \ \ / / _` | '__| |/ _` | '_ \| |/ _ \/ __| 
| |_\ \ | (_) | |_) | (_| | |  \ V / (_| | |  | | (_| | |_) | |  __/\__ \ 
 \____/_|\___/|_.__/ \__,_|_|   \_/ \__,_|_|  |_|\__,_|_.__/|_|\___||___/ 
                                                                          
 __________________________________________________________________________________________________________________ 
/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/ 
                                                                          


  ______                _   _                               _        _                     _           _                 _   _                 
 |  ____|              | | (_)                             | |      | |                   | |         | |               | | (_)                
 | |__ _   _ _ __   ___| |_ _  ___  _ __     __ _ _ __   __| |   ___| | __ _ ___ ___    __| | ___  ___| | __ _ _ __ __ _| |_ _  ___  _ __  ___ 
 |  __| | | | '_ \ / __| __| |/ _ \| '_ \   / _` | '_ \ / _` |  / __| |/ _` / __/ __|  / _` |/ _ \/ __| |/ _` | '__/ _` | __| |/ _ \| '_ \/ __|
 | |  | |_| | | | | (__| |_| | (_) | | | | | (_| | | | | (_| | | (__| | (_| \__ \__ \ | (_| |  __/ (__| | (_| | | | (_| | |_| | (_) | | | \__ \
 |_|   \__,_|_| |_|\___|\__|_|\___/|_| |_|  \__,_|_| |_|\__,_|  \___|_|\__,_|___/___/  \__,_|\___|\___|_|\__,_|_|  \__,_|\__|_|\___/|_| |_|___/
                                                                                                                                               
                                                                                                                                               
 __________________________________________________________________________________________________________________ 
/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/ 
                                                                          

  ______               _          _                     _ _                            _     _                   _       
 |  ____|             | |        | |                   | | |                          (_)   | |                 | |      
 | |____   _____ _ __ | |_ ______| |__   __ _ _ __   __| | | ___ _ __   _ __ ___  __ _ _ ___| |_ _ __ __ _ _ __ | |_ ___ 
 |  __\ \ / / _ \ '_ \| __|______| '_ \ / _` | '_ \ / _` | |/ _ \ '__| | '__/ _ \/ _` | / __| __| '__/ _` | '_ \| __/ __|
 | |___\ V /  __/ | | | |_       | | | | (_| | | | | (_| | |  __/ |    | | |  __/ (_| | \__ \ |_| | | (_| | | | | |_\__ \
 |______\_/ \___|_| |_|\__|      |_| |_|\__,_|_| |_|\__,_|_|\___|_|    |_|  \___|\__, |_|___/\__|_|  \__,_|_| |_|\__|___/
                                                                                  __/ |                                  
                                                                                 |___/                                   
 __________________________________________________________________________________________________________________ 
/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/_____/ 
                                                                          

  __  __       _                                                      ____                                _                 __  
 |  \/  |     (_)                                                    / / _|                              | |                \ \ 
 | \  / | __ _ _ _ __    _ __  _ __ ___   __ _ _ __ __ _ _ __ ___   | | |_ ___  _ __ _____   _____ _ __  | | ___   ___  _ __ | |
 | |\/| |/ _` | | '_ \  | '_ \| '__/ _ \ / _` | '__/ _` | '_ ` _ \  | |  _/ _ \| '__/ _ \ \ / / _ \ '__| | |/ _ \ / _ \| '_ \| |
 | |  | | (_| | | | | | | |_) | | | (_) | (_| | | | (_| | | | | | | | | || (_) | | |  __/\ V /  __/ |    | | (_) | (_) | |_) | |
 |_|  |_|\__,_|_|_| |_| | .__/|_|  \___/ \__, |_|  \__,_|_| |_| |_| | |_| \___/|_|  \___| \_/ \___|_|    |_|\___/ \___/| .__/| |
                        | |               __/ |                      \_\                                               | |  /_/ 
                        |_|              |___/                                                                         |_|      
```
ASCII art acknowledgement: [taag](http://patorjk.com/software/taag/#p=display&f=Big&t=Type%20something)

In the rest of the sections, we'll briefly explain each of these program regions. All examples are assembled in the program in Example 3.1.1 at the end of the Study section.

##### Global variables

Global variables are variables that are in the top-level scope of the program, and are thus visible to all the program's components, including functions and classes. They are the main _program data_ (aka `[<cept>]`_program state_). In general, there should be a minimal number of global variables, thought the single-file environment mitigates most of the risks. Nevertheless, it makes good sense to put the at the very top of the program. This makes them easy to find, helps avoid duplication, and gives a brief overview of the program to a new reader. In Example 3.1.1, our global variable, against which all program operations are performed, is the `balls` array.

##### Function and class declarations

Functions and classes are _named_ encapsulation structures, which help modularize the program code and prevent duplication of code. Functions have input and output, and can have local data needed for their operation. Functions are `[<cept>]`_called_ by invoking their name and specifying the necessary arguments, if any, between parentheses. While not all functions have `[<cept>]`_parameters_ (meaning the declarations of the `[<cept>]`_arguments_), a function call **requires** the parentheses `()`. In Example 3.1.1, we have two functions, `createBalls()` and `bounceBalls()`. Note that they have distinct functionality, properly communicated by their names.

Classes are the backbone of the object-oriented programming paradigm, allowing programmers to define their own types, known as `[<cept>]`_user-defined types_, by encapsulating complex data and determiniting the exact set of computational operations that can be performed on them. Classes are thus the templates for the creation of user-type entities, called `[<cept>]`_objects_.

Keeping functions and classes clustered together follows the `[<cept>]`_library_ (aka `[<cept>]`_application programming interface (API)_) paradigm, in which all the functions and user data types available to the programmer are presented in an exhaustive and descriptive list.

##### Event handler registrants

Events are phenomena, internal or external to the computer's processor, that the processor has to be informed of. There are two ways to communicate events to the processor: `[<cept>]`_interrupts_, which are signals sent by the event originator to the processor and which the processor inteprets and acts on, and `[<cept>]`_polling_, which is a process of cyclic interrogation of the possible originators by the processor. The micro:bit actually uses a hybrid method, where on the hardware level interrupts are used, but the device and language runtimes actually implement polling. The MakeCode packages which are involved in, among other thigns, various event detection and response are `input`, `pins`, `radio`, `conrol`, and `serial`. The way the processor and/or runtime responds to an event is by assigning an `[<cept>]`_event-handler_ function to each event. In MakeCode, this is done by event-handler `[<cept>]`_registrants_, which are themselves functions, which take the event-handler functions as one of their arguments. In Example 3.1.1, we have a `Gesture` event-handler registrant, called `input.onGesture`. It takes two arguments: the name of the gesture, in this case `Shake`, and the event-handler to be executed upon detection of the shake-gesture event, namely `function () { balls = createBalls(); }`.

Due to the way they are executed, event handlers should not be put inside loops, functions, or classes, but at the top level of the program, and clustered together so that it is easy to see at a glance what events the program is listening to. More on this in a later step.

##### Main program (forever loop)

The main program contains the main program loop, in the case of MakeCode micro:bit, most often a `basic. forever()` loop function. It is best to keep the contents of the loop short and descriptive, usually by encapsulating the details away into functions and classes. In Example 3.1.1, the only function that the main program loop is calling is `bounceBalls()`, and nothing else, making the intent of the program exceedingly obvious and setting firm expectations of the program behavior that may be observed.

##### Proper indentation

Lastly, proper program structure can be easily defeated if the proper language-specific indentation style is not adopted. In general, the rules are as follows:
1. Top-level constructs (e.g. declarations) start at column 0 (that is, flush against the left-hand edge of the editor).  
2. Each encapsulation (aka scope) nesting (e.g. function block/body, conditional statement block/body, and the full contents of a class) are started **4 characters in**.  
3. Whether the opening brace `{` of a block is at the end of the preceding line, like
```javascript
function foo() {
    // function body
}
```
or at the start of the new line, 
```javascript
function foo() 
{
    // function body
}
```

is a matter of personal style, but it should be _consistent_.

Notice how the proper indentation and consistent style in Example 3.1.1 makes the program easy to read and comprehend at a glance.

```javascript
// Example 3.1.1

/* *** Global variables *** */
let balls : game.LedSprite[] = []

/* *** Function declarations *** */
function createBalls() {
    for (let i = 0; i < 3; i ++) {
        let ball = game.createSprite(Math.randomRange(0, 4),
                                     Math.randomRange(0, 4))
        if (Math.randomBoolean()) {
            if (Math.randomBoolean()) {
                ball.turnLeft(45)
            } else {
                ball.turnRight(45)
            }
        }
        balls.push(ball)
    }
    return balls
}

function bounceBalls() {
    if (balls.length > 0) {
        for (let i = 0; i < 20; i ++) {
            for (let b = 0; b < balls.length; b ++) {
                balls[b].move(1)
                basic.pause(50)
                balls[b].ifOnEdgeBounce()
            }
        }
        for (let b = 0; b < balls.length; b ++)
            balls[b].delete()
    }    
}

/* *** Event-handler registrants *** */
input.onGesture(Gesture.Shake, function () {
    balls = createBalls()
})

/* *** Main program (forever loop) *** */
basic.forever(function () {
    bounceBalls()
})
```

#### 2. Apply
[[toc](#table-of-contents)]

1. `[<lernact-prac>]`Rewrite your program from 2.2.1 to follow the structure presented in this step. Requirements:
   1. The code for the coding simulation should be encapsulated in a funciton, called `coding`.  
   2. The `basic.forever` loop should only call `coding()` and nothing else.  
   
2. `[<lernact-prac>]`Rewrite your program from 2.2.2 to follow the structure presented in this step. Requirements:
   1. The functionality for a raindrop should be encapsulated in a class, called `Raindrop`. You may or may not subclass from `game.LedSprite`, as you wish. Refer to LP002-[9-11] for an example.  
   2. The actual rain should be encapsulated in a function, called `rain`.  
   3. The `basic.forever` loop should only call `rain()` and nothing else.  
   
3. `[<lernact-prac>]`Rewrite your program from 2.2.3 to follow the structure presented in this step. Requirements:  
   1. The code for the coding simulation should be encapsulated in a funciton, called `freqBars`.  
   2. The `basic.forever` loop should only call `freqBars()` and nothing else.  


#### 3. Present
[[toc](#table-of-contents)]
   
In the [programs](programs) directory:
1. Add your program from 3.2.1 with filename `microbit-program-3-2-1.js`.  
2. Add your program from 3.2.2 with filename `microbit-program-3-2-2.js`.  
3. Add your program from 3.2.3 with filename `microbit-program-3-2-3.js`.  

In the [Lab Notebook](README.md):
1. Link to the program from 3.2.1.  
2. Link to a demo video showing the execution of the program from 3.2.1.  
3. Link to the program from 3.2.2.  
4. Link to a demo video showing the execution of the program from 3.2.2.  
5. Link to the program from 3.2.3.  
6. Link to a demo video showing the execution of the program from 3.2.3.  


### 4. Divide & conquer: program decomposition  
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

##### Two modes: working & asleep

`[<lernact-rd>]`It is good to create a development plan for a program that is expected to be large. The most powerful technique is `[<cept>]`_decomposition_, the breaking up of a large task into smaller pieces, each one of them as independent from the others as possible. In a previous Step, we saw some sub-programs of the large target program, namely the `coding()`, `rain()`, and `freqBars()` functions, each encapsulating an independent simulation. So, we have actually already made steps toward decomposing the large program, specifically by taking a `[<cept>]`_bottom-up_ approach, in which we first build the small consituent pieces, which we will put together later. 

From the opposite direction, in what we can call `[<cept>]`_top-down_ approach, the high-level structure of the program is that it has _two mutually exclusive modes_, "working" and "asleep", which have no program behavior in common. Such a case is ideal for a small `enum` class, which creates a type of _named_ elements, as in the following example:
```javascript
// Example 4.1.1

enum Mode { Asleep = 0, Working }

let mode : Mode = Mode.Asleep
```
This is helpful because it maintains maximum _"self-describability"_ of the program. We don't need excessive comments, if the program itself reads like a narrative.

In mode `Working`, we call the `coding()` sub-program, while in mode `Asleep`, we call whichever sub-program corresponds to the last gesture detected (or the default). We can accomplish this easily within a single `basic.forever() {}` loop, which is ideal for our overall program structure.

##### Sub-programs

The most important aspects of the sub-programs `coding()`, `rain()`, and `freqBars()` is that they are _independent of each other_ and are _similarly encapsulated_. Towards the first aspect, only one of these programs is running at a time, and no other program is running _in the background_ to compete for computing resources or perturb the timing of the program currently executing. Toward the second aspect, all of these sub-programs are encapsulated as functions with the same signature, namely no parameters and no return type, as in `function foo() : void {}`. This makes them easy to call from the same place, in our case the `basic.forever()` loop.

We have only looked at 2 screensavers (meaning the programs running in `Asleep` mode) so far, but now that we have a solid program structure, both bottom-up and top-down, we can easily go about developing and adding other screensavers. Just wrap them into a `void` function with no parameters, whether the internal implementation is a function or a class, and add it to the options for the `Asleep` mode. We see two important properties of well-structured programs: `[<cept>]`_implementation hiding_, which dictates that the implementation details for a certain independent program component need not be visible (in other words, at the top level of the program, we don't care about how a particular screensaver sub-programs is implemented), and `[<cept>]`_extensibility_, which allows for seamless extension of the functionality of the large program by just adding more options, without changing the overall design and structure.

##### Matching gestures and screensavers

Here, we will also see how the encapsulation, implementation hiding, and extensibility of our structured program design will help us with keeping the main program simple. In particular, how do we match gestures and screensaver programs? Do we use `if...else` cascades, a `switch` statement, or some other construct. Actually, we will use the marvelous property of JavaScript (and, hence TypeScript) that it has `[<cept>]`_first-class functions_. Let's look at an example:
```javascript
// Example 4.1.2

let gesture : Gesture = Gesture.TiltLeft
let gestures : Gesture[] = [Gesture.TiltLeft, Gesture.TiltRight]

let screensavers = [rain, freqBars]
```
Notice how we have created to parallel arrays, one with gestures, one with screensavers. Notice also that, if we want to add more screensavers and matching gestures, we can just add to these global arrays. How exactly we do the matching is shown in the next example:
```javascript
// Example 4.1.3

basic.forever(function () {
    screensavers[gestures.indexOf(gesture)]()
})
```
Let's unpack this:
1. We can call a function, which is an array element, by combinging the element selection syntax for arrays and the function call syntax, as in `screensavers[0]()`, where `screensavers[0]` is just the function `rain`, and the parentheses `()` at the end end off a proper function call.  
2. Assuming that we have constructed the parallel arrays so that the gestures and screensavers match _by index_, we can use the `indexOf` method to get the index of the last gesture stored in the variable `gesture` in the `gestures` array and then apply this index in the selector for the corresponding screensaver, as we san in (1).  

#### 2. Apply
[[toc](#table-of-contents)]

1. `[<lernact-prac>]`Write a program with two modes, `Working` and `Asleep`. Requirements:
   1. A global variable `mode` of time `Mode` holds he latest gesture detected.  
   2. Mode `Working` is default.  
   3. In mode `Working`, display the single character `W`.  
   4. In mode `Asleep`, display the single character `Z`.  
   5. Button `A` sets the mode to `Asleep`.  
   6. Button `B` sets the mode to `Asleep`. 

2. `[<lernact-prac>]`Write a program which recognizes two gestures, `TiltLeft` and `TiltRight`. Requirements:
   1. A global variable `gesture` of type `Gesture` holds he latest gesture detected.  
   2. Gesture `TiltLeft` is default.  
   3. When gesture `TiltLeft` is detected, display the single character `L`.  
   4. When gesture `TiltRight` is detected, display the single character `R`.  
   
3. `[<lernact-prac>]`Combine the two programs. Requirements:
   1. In `Working` mode, keep calling `coding()`.  
   2. In `Asleep` mode, keep calling the screensaver function which corresponds to the last gesture detected, or the default.  
      1. `rain()` for `TiltLeft`.  
      2. `freqBars()` for `TiltRight`.  

#### 3. Present
[[toc](#table-of-contents)]
   
In the [programs](programs) directory:
1. Add your program from 4.2.1 with filename `microbit-program-4-2-1.js`.  
2. Add your program from 4.2.2 with filename `microbit-program-4-2-2.js`.  
3. Add your program from 4.2.3 with filename `microbit-program-4-2-3.js`.  

In the [Lab Notebook](README.md):
1. Link to the program from 4.2.1.  
2. Link to a demo video showing the execution of the program from 4.2.1.  
3. Link to the program from 4.2.2.  
4. Link to a demo video showing the execution of the program from 4.2.2.  
5. Link to the program from 4.2.3.  
6. Link to a demo video showing the execution of the program from 4.2.3.  

   
### 5. Randomized behavior  
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

    - dimensions of randomization  
    - the family of random functions  (`random`, `randint`, `randomBoolean()`, etc.)   
    - write your own custom randomization routines  
    - the bouncing-ball randomization (multiple dimensions)  

#### 2. Apply
[[toc](#table-of-contents)]
#### 3. Present
[[toc](#table-of-contents)]
   

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
   
### 9. Code reading
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

[`game.ts`](https://github.com/microsoft/pxt-microbit/blob/master/libs/core/game.ts)  
   - export  
   - functions (`createSprite`)  
   - classes (`LedSprite`)  
   - namespaces (`game`)  
   - use of variables  
   - TODO:
     - micro:bit [tech docs](https://makecode.com/docs)  
     - TS [namespaces](https://www.typescriptlang.org/docs/handbook/namespaces.html)  

#### 2. Apply
[[toc](#table-of-contents)]

#### 3. Present
[[toc](#table-of-contents)]

   
### 10. Iterative development with Github  
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

   - status, add, commit, pull, push  
   - remote & local (git SCM)  
   - Github workflow (pull requests, Github Classroom **Feedback**)  
   - informative commit messages  
   - releases & tags  
   - incremental development example

#### 2. Apply
[[toc](#table-of-contents)]
#### 3. Present
[[toc](#table-of-contents)]
   
### 11. Reactive system  
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

`forever` function vs `while` loop  
feature | `forever` | `while`
-- | -- | --
condition | no | yes
`break` | no | yes
scheduling | yes | no
simulator fidelity | yes | no

    - [reactive system](https://makecode.microbit.org/device/reactive)  
    - Why you shouldn't have event handling in a `forever` loop  
    - How is `pause` executed and what is affected (e.g. other repeated behavior, event handling, etc.)  
      - `pause()` should be avoided, especially with multiple `forever()` loops  
    - Advanced material: [software stack](https://mattwarren.org/2017/11/28/Exploring-the-BBC-microbit-Software-Stack/)  
      - **threads**, fibers, and the [fiber scheduler](https://lancaster-university.github.io/microbit-docs/advanced/)  
      - Issues of speed, memory, etc.  
      - Thread(fiber)-unsafety: Issues of non-deterministic splitting & rearrangement of code portions   
      - (Brendan) Does a time slice have to end for event handling to proceed?  

#### 2. Apply
[[toc](#table-of-contents)]
#### 3. Present
[[toc](#table-of-contents)]
   
### 12. Matrix dynamics  
[[toc](#table-of-contents)]

#### 1. Study
[[toc](#table-of-contents)]

    - can use out-of-bound coordinates in `plot()` and `unplot()`  
    - don't use `pause()`, `show*()` for smooth graphics 
    - `clearScreen()` is often necessary but it's fast, so no problem  
    - why do we need `pause()` after `clearScreen()`?
      ```javascript
      while (true) {
          if (isHeart)                                             
              basic.showIcon(IconNames.Heart)
          else
              basic.showIcon(IconNames.Butterfly)
          basic.pause(100)
          basic.clearScreen()
          basic.pause(100)                             // THIS IS REQUIRED TO SEE THE ICON BLINK
      }
      ```
    - _frame_-based display for speed and smoothness  
    - mod-based timing  
      - simulator vs device  
      - fine-tuning

#### 2. Apply
[[toc](#table-of-contents)]
#### 3. Present
[[toc](#table-of-contents)]
   

