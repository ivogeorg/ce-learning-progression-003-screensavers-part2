# CPE 1040 - Fall 2020

This is learning progression 003 for the Fall 2020 installment of the course CPE 1040: Introduction to Computer Engineering at MSU Denver.

Table of Contents
=================

* [CPE 1040 \- Fall 2020](#cpe-1040---fall-2020)
  * [Learning Progression 003: Screensavers (Part 2)](#learning-progression-003-screensavers-part-2)
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
      
## Learning Progression 003: Screensavers (Part 2)
[[toc](#table-of-contents)]

This progression is the culmination of the first part of the course, in which we program the bear-bones micro:bit without any extenral circuitry attached and without communication features. We pull together all the programming language features and best practices that we introduced in the previous two learning progressions, to write a significantly larger target program over 12 steps. This will present the opportunity to learn about some of the design considerations a programmer makes when approaching a larger project. The progression is also going to dig a bit deeper into the _softare stack_ of the micro:bit, and uncover the ways it affects these considerations.

   
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
   
