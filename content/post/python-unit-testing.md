---
title: "Python unit testing"
date: 2017-03-13T12:33:21+08:00
categories: ["development"]
draft: false
---


### Introduction
 
I first thought to write about TDD (*Test-Driven Development*), but given the list of reminders I have to emphasize and which would lead to a very lengthy article, I decided to shrink the work into smaller edible articles.

You can find a lots of documentation about unit testing with Python on the Internet, so why am I adding this brick? That is not your business. But landing on this page will be useful for you as I found highly ranked -*by Google*- online resources discussing Python unit testing where the authors either mislead you at best or misinform you, not because they do not know but because they do not give importance to details.Yes, as a programmer, your job is to be  obsessed by details.

- [History](#history)
- [What is unit testing?](#definition)
- [What to test?](#what2test)
- [What are unit tests useful for?](#usefulness)
- [How to write a unit test?](#how)
- [Which tools to use?](#tools)
- [Anti patterns](#antipatterns)
- [Conclusion](#conclusion)

<a name="history"></a> 
### History 
In the old days, when computers were slow, very simple and short programs were taking dozens of minutes to build. In those days, the days of ENIAC and EDVAC, programmers were used to take long hours trying to figure out what went wrong. It was the era of debugging. Yes, debugging is the father of all advanced testing techniques you know today. During the 1970’s, testing became a distinct idea from debugging when computers became faster thanks to IBM who released, by the start of 1970’s,  the first computer to use a semiconductor memory allowing programmers to develop some primitive but yet interesting video games such as Atari 2600 by the end of that decade. 

Nowadays, unit testing is already an old concept which gave birth to several grand children such as TDD, BDD and ATDD; but do not be surprised to hear some of your development colleagues think of it as a new one.

On the coming Summer holidays, you may read *The Growth of Software Testing* by *D. Gelperin, B. Hetzel* who covered the history of testing at best.
<a name="definition"></a>
### What is unit testing? 
Unit testing is all about moving through the small code units of your application to see if, given certain circumstances and conditions, they respond as expected to fulfill the desired functionality. In plainer English, unit testing is  about hunting for eventual errors that may lay in your program functions. If unit tests do not show errors in your code, it does not mean there are no errors or that your program is correct: they just are, when well written, a paramount milestone that help you to product a good quality software by reducing the number of errors. This means unit tests are not a means by themselves but are a way that helps to walk safely to the end.
<a name="what2test"></a>
### What to test?
Obviously, you have to test your smallest code units. The smallest code units you have are not classes but the functions they wrap. If your code  is not organized in terms of classes or functions then you should shift your career from a developer to a dish washer in the nearest restaurant to your living place.

After you refactor a given function without changing its functionality and you found out that the corresponding unit test no longer passes then you can fairly conclude that your unit test is wrong because unit tests are there to confirm or infirm  functionality not implementation details.
<a name="how"></a>
### How to write a unit test?
There are many details to talk about, but I am not going to highlight the basic principles you must not forget when you plan to code a unit test in Python:
<a name="points"></a>

1. Your test class must inherit from `unittest.TestCase`.
2. Your test class should be prefixed with ***Test***.
3. Run the test module by calling ` unittest.main()`.
4. The test modules must trail with ***_test***.
5. The test functions should be prefixed by ***test_***.
6. Last but not the least, all unit tests must be independent from each others.

Keep this in mind and let us work on an example: the present tutorial is the first step to write a primitive calculator with a graphical user interface to perform addition, subtraction, multiplication and division of integer and float numbers.

First, create a virtual environment with the following hierarchy:
```c
.
├── calculator.py
├── __init__.py
└── tests
    ├── __init__.py
    └── unit
        ├── calculator_test.py
        └── __init__.py
```
The convention when programming in Python is to gather your tests within a package called `tests`. As a sub-package, I created `unit`: this is a good practice as with real projects you will have to run other tests that unit tests.

As a starting point, let us focus on the division operation only:
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import logging

class CalculatorCore:
   """Basic calculation functions: +, -, * and /
   For the moment, I deal only with "/" operation.
   """

   def __init__(self):
       pass

   def check_operands_validity(self, *args):
       for arg in args:
           if not (isinstance(arg, float) or isinstance(arg, int)):
               return False
       return True

   def divide_two_integer_or_float_numbers(self, dividend, divisor):
       if self.check_operands_validity(dividend, divisor):
           try:
               a = dividend / divisor
           except Exception as exception:
               logging.error(exception)
               raise           
           else:
               return a                          
           return dividend / divisor
       return 0


if __name__ == '__main__':
   calculator_core = CalculatorCore()
   print(calculator_core.divide_two_integer_or_float_numbers(2,4))
```
Save the above code in `calculator.py` module.

Few comments about the above code:

- I chose to name the above class by `CalculatorCore` instead of *Calculator* because I will code the `CalculatorGUI` class on the next tutorial.
- Checking the parameters validity is done in a separate function called `__check_operands_validity()` to avoid code duplication in further operation.

To test if everything is Ok with our class, you have to test its different code units. Let us start by testing the functionality of the main useful function which properly performs the division using Python’s built in `unittest` module:

```python
import unittest
from calculator import CalculatorCore


class TestCalculatorCore(unittest.TestCase):

   def setUp(self):
       self.calc = CalculatorCore()

   def test_divide_two_integer_or_float_numbers_returns_correct_result(self):
       self.assertEqual(3, self.calc.divide_two_integer_or_float_numbers(9,3))
	   

if __name__ == '__main__':
   unittest.main()
```
You observe that after I imported the necessary things, I respected the [previous](#points) rules. The only new element up to this point is the [`setUp()`](https://docs.python.org/3/library/unittest.html#unittest.TestCase.setUp) method which is there to instantiate once only the class `CalculatorCore`. Save the above code within `calculator_test.py`.

In this test, I want to check if my function performs the division correctly, so I used [`assertEqual()`](https://docs.python.org/3/library/unittest.html#unittest.TestCase.assertEqual) for this purpose.

The simplest way to run this test is to position yourself on the level of `calculator.py` package and execute this command:
```python
python -m tests/unit/calculator_test
```
Note how I wrote `calculator_test` without the `.py` extension.

The output should be this:
```c
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```
Our output says there was only one test function implemented "*Run 1*" and the test has passed "*OK*". The "." reflects the number of tests which **passed**.

As a code tester, you must also check if your unit test fails when given a wrong input:
```python
def test_divide_two_integer_or_float_numbers_returns_error_when_divisor_is_null(self):
       self.assertEqual(3, self.calculator_core.divide_two_integer_or_float_numbers(9,0))
```
Run again:
```python
python -m tests.unit.calculator_test
```
You will get this informative output which speaks by itself:
```python
.ERROR:root:integer division or modulo by zero
E
======================================================================
ERROR: test_divide_two_integer_or_float_numbers_returns_error_when_divisor_is_null (__main__.TestCalculatorCore)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/begueradj/projects/calculator/tests/unit/calculator_test.py", line 14, in test_divide_two_integer_or_float_numbers_returns_error_when_divisor_is_null
    self.assertEqual(3, self.calculator_core.divide_two_integer_or_float_numbers(9,0))
  File "calculator.py", line 21, in divide_two_integer_or_float_numbers
    a = dividend / divisor
ZeroDivisionError: integer division or modulo by zero

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (errors=1)

```
But you can do a better test: you can create a test function to check if the `ZeroDivisionError` is raised. This way you can get rid of the previous function test:
```python
import unittest
from calculator import CalculatorCore


class TestCalculatorCore(unittest.TestCase):

   def setUp(self):
       self.calculator_core = CalculatorCore()
       
   def test_divide_two_integer_or_float_numbers_returns_correct_result(self):
       self.assertEqual(3, self.calculator_core.divide_two_integer_or_float_numbers(9,3))   
    
   def test_divide_two_integer_or_float_numbers_raises_exception_when_divisor_is_zero(self):
       self.assertRaises(ZeroDivisionError, self.calculator_core.divide_two_integer_or_float_numbers, 2, 0)
     

if __name__ == '__main__':
   unittest.main()
```
Both test pass now and you can see the exception confirmation:
```python
ERROR:root:integer division or modulo by zero
..
----------------------------------------------------------------------
Ran 2 tests in 0.001s

OK
```
The question you may ask at this stage is whether or not there is nothing else to test. Luckily there is a good (but not enough) criteria to answer to this question: it is the notion of code coverage. For this purpose, there is a tool called `coverage.py` which you can install as follows:
```python
pip install coverage
```
You will get a Terminal output similar to this one:
```
Collecting coverage
  Downloading coverage-4.3.4-cp35-cp35m-manylinux1_x86_64.whl (191kB)
    100% |████████████████████████████████| 194kB 1.7MB/s 
Installing collected packages: coverage
Successfully installed coverage-4.3.4
```
Let us see the code coverage of our program:
```python
coverage report -m
```
It says that only 73% of our code is tested:
```python
Name            Stmts   Miss  Cover   Missing
---------------------------------------------
calculator.py      22      6    73%   15, 22-24, 27-28
```
As a thumb of rule, code coverage must not be lower than **80%**. So a closer look to our program shows we did not test `check_operands_validity()` function. Let us do it:
```python
import unittest
from calculator import CalculatorCore


class TestCalculatorCore(unittest.TestCase):

   def setUp(self):
       self.calculator_core = CalculatorCore()

   def test_divide_two_integer_or_float_numbers_returns_correct_result(self):
       self.assertEqual(3, self.calculator_core.divide_two_integer_or_float_numbers(9,3))
   
    
   def test_divide_two_integer_or_float_numbers_raises_exception_when_divisor_is_zero(self):
       self.assertRaises(ZeroDivisionError, self.calculator_core.divide_two_integer_or_float_numbers, 2, 0)
   
   def test_check_operands_validity_returns_false_if_parameters_is_character(self):
       self.assertFalse(self.calculator_core.check_operands_validity('b'))   
      

if __name__ == '__main__':
   unittest.main()
```
<a name="usefulness"></a>
### What are unit tests useful for?
Errors in software can cost money, reputation damage, compromised systems by nefarious users who succeed to exploit your application’s flaws, or simply lead to people death. Unit tests are not a magic wand to eliminate once for all those errors, but it helps to significantly reduce them.

If you are drawn to code many unit tests for a given function so that to cover all its possible scenarios then this means your function is not good and you have to shrink it down to smaller code units (functions) with fine grained responsibilities. A function is there to do one thing only: remember of this principle and it will save your life.
<a name="tools"></a>
### Which tools to use?
Testing is such important that it spawned a long list of testing tools in different programming languages: **TestOOB** , **tox**, **TestGears** are some of the good ones. Howeve, my favorite tool is **Nose**. Which one you should use is a matter of personal taste.
<a name="antipatterns"></a>

### Anti patterns

- One of the highly ranked tutorials on this subject ranked by Google teaches you bad practices: for example, the author named *test_primes.py*. If you test your code using a tool like *Nose* then nothing will work because Nose identifies the test files by looking for files which suffix is ***_test***. If the author respected the **PEP 8** standard, he would not be disappointed when using *Nose*.

- The same author injects several input values for one unique test. This is a bad practice because  as soon as one assertion fails, the rest are skipped. The author should use  `subTest()` function instead.

<a name="conclusion"></a>
### Conclusion

Unit tests are a fundamental concept to grasp in that they help to reduce the attack surface and bugs of your applications. I did not cover the *doctest* subject here because I simply do not like it, do not use it professionally and more importantly it does not add a value to what I already do using *unittest*, *nose* and *mock* frameworks (yeah, we will see mock in the coming tutorial and in the right moment).

The best you can read about unit testing is the famous *The Art of Unit Testing* by *Roy Osherove*. The examples provided in this book, however, are in C#. As an exercise, you can complete the program of today with the remaining mathematical operations and design their corresponding unit tests before we continue this tutorial.
