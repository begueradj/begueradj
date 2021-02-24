---
title: "Python decorators"
date: 2018-02-18T12:33:21+08:00
categories: ["development"]
draft: false
---
What are the origins and goals of Python decorators? How to implement them and why are they useful?
# Introduction
Even when dealing with scientific and technical subjects, your subjectivity rules over you. I mean, each person has her own style in learning and explaining ideas. This subjective aspect is shaped not only by your intellectual capacities but more importantly by your personality. By personality, I mean how you see and interpret things. This subjectivity dimension highly impacts your learning curve. This is what you may already have observed in yourself. This is also what the Swiss development psychologist Jean Piaget detailed in his theory of cognitive development. So maybe the best book dealing with Python decorators would not fit you. Thus, you understand the reason behind this an other, yet different, contribution on Python decorators that hopefully will fit you.

The first thing I would like to start with is that Python decorators have nothing to do with the decorator design pattern described by the *Gand of Four* in their famous and now classic book *Design Patterns: Elements of Reusable Object-Oriented Software*.

### Back to 2005
If you are like me when I have to learn new technical notions, you may ask yourself about the origin and goals of Python decorators. As you may already have read elsewhere, Python decorators were first implemented in  Python2.4 when introducing `@classmethod` and `@staticmethod` built-ins.

I read an article published on 2005 by the author of Python Web Server Gateway Interface specification (PEP 333) in which he said: *as a relatively new feature, their full possibilities have not yet been explored, and perhaps the most exciting uses haven't even been invented yet*. 

As you can see, it is not always evident to see the importance of a new concept, especially if you are not the one who conceived it.  Philip Edy had hard time to find the necessity to use decorators. To highlight the 2 reasons as why you need to implement decorators, he invoked a short code snippet without a decorator:

```python
import atexit

def goodbye():
   print "Goodbye, world!"

atexit.register(goodbye)
```
and then with a decorator version:
```python
import atexit

@atexit.register
def goodbye():
   print "Goodbye, world!"
```
While I agree with the first advantage, I do not agree with the last one in that repeating the function name in this situation does not go against the **DRY** principle you have learned in *The Pragmatic Programmer: From Journeyman to Master*. The DRY principle is rather violated in [this](http://stackoverflow.com/questions/12179271/python-classmethod-and-staticmethod-for-beginner/12179752#12179752) highly rated answer I randomly picked from StackOverflow:

```python
class Date(object):

    day = 0
    month = 0
    year = 0

    def __init__(self, day=0, month=0, year=0):
        self.day = day
        self.month = month
        self.year = year
```
This class is wrongly designed: You can see that the  information hold by both the class and instance variables are of the same nature. The class variables must be removed because each day is unique.  Not only this does not conform to DRY principle but it is also a confusing design.

If I do not agree with Phillip Eby then what makes decorators a unique and useful Python feature? I will answer this question by the end of this article because you can not grasp the meaning of what I say without actually introducing what decorators are.

### Definition
Unfortunately, I see many resources making a parallel between decorators and Java annotations. I say unfortunately because the only common aspect between these 2 notions is the syntactic sugar line itself which looks exactly alike. Everything else is different:

- Java annotations have no effect on the method or class they decorate, unlike Python decorators which are functions able to modify the behavior (but not the definition) of other functions arbitrarily at runtime.
- Java annotations provide metadata to class or method while Python decorators take a function as an argument and return a function.

I often read comments on other online Python communities complaining about the fact decorators are simple to use but difficult to implement. The other thing I hear is about the simplicity of implementing decorators if you develop in Python under the OOP perspective. So let us take some useful steps that will lead and help to understand what Python decorators are about.

# Python functions
Save the following simple Python function in a file called `hello.py`:
```python
def greet():
   print('Hello')
if __name__ == '__main__':
   greet()
```
Running `python hello.py` will generate a bytecode which Python runtime loads to get interpreted by the virtual machine which then creates a function object to which the name `greet` is binded.
### FCO
I am not here to teach you what is a function. I suppose you already know that. But it is important to remember that in Python, all functions are CFO. But what is this weird term? Well, actually, many programming languages implement the notion of first-class functions (CFC). This means that in such programming languages you can, among other things, use functions as parameters as well as  return values which you can store in a variables. This useful concept was introduced by *Christopher Strachey*. But because everything in Python is an object (yes, including classes!), we rather need to use the acronym FCO instead of FCF. FCO sands for first-class objects.<br/><br/>
If you do not believe that functions are objects in Python, add this line to the previous `hello.py` code:
```python
print(greet)
```
This will result in printing:
```python
<function greet at 0x7fd2d1fb32f0>
```
This output simply means that the virtual machine has allocated a memory address `0x7fd2d1fb32f0` for the object called `greet`.<br><br>
A function can be passed as an argument to a function:
```python
def greet(function_name):
   """This functions takes as an argument an other function.
   Only the function name is passed
   """
   # Call to function_name and printing out the value it returns
   print('Hello {} !'.format(function_name()))

def name(fname = 'Jack'):
   """This function takes a named and optional argument with a default value.
   It returns the passed argument.
   Well, useless example, but this is just for illustration.
   Once you understand the basics, you can code your own magical stuff.
   """
   return fname
if __name__ =='__main__':
   greet(name)
```
The above example does not only show that we can pass a function as an argument to an other function:
```python
greet(name)
```
but it also shows that you can use a function as a return value to store in a variable:
```python
print('Hello {} !'.format(function_name()))
```
In case you are stubborn and still do not believe that functions in Python are first class objects then add this line to the previous program:
```python
print(greet.__class__)
print(issubclass(greet.__class__, object))
```
The last line above will print `True` because the function `greet`, as all other objects in Python, inherits from the `object` baseclass.
### Nested functions
Python allows you to define -and call- a function inside an other one. This feature is what is commonly called nested functions. So let us see nested functions in actions by saving the following program in a `email.py` file:
```python
import re

def email(email_address):
   """This is an outer function.
   It receives an arbitrary email address.
   It delegates the validation check to is_valid() inner function
   """
   def is_valid(email_address):
       """This is an inner function.
       It has access to the variable of the enclosing scope
       to check its validation.       
       """
       ok = re.search(r'(^[a-zA-Z0-9\.\+_-]+)@[a-zA-Z].[a-zA-Z\.]+$', email_address)
       return True if ok else False
   #This will prints True or False
   print(is_valid(email_address))

# Main program
if __name__ == '__main__':
   email('begueradj@begueradj.com')
```
Note that you can nest your functions as deeply as you want as long as you respect variables scope. <br/>
Dealing with nested functions, it is important to keep in mind the variables scope. If you need to modify the value of a variable enclosed within the namespace of an outer function and lays inside its inner function, then you must declare it with `nonlocal` keyword, otherwise the Python interpreter will create a variable local to the inner function:
```python
def outer():
   x = 1
   def inner():
       nonlocal x #This means you want to access the x defined in outer()
       x = 2
   inner()
   print('x = {}'.format(x)) #The value of x is no longer equal to 1
   
# Main program
if __name__ == '__main__':
   outer()
```
If you got a syntax error after running the above program, this means you used Python2.x while the keyword `nonlocal` is introduced in Python3.x. Do not confuse `nonlocal` with the `global` keyword which rather is used to reference variables define in the scope of a module.
# Closures
Let us turn back to our previous `email.py` to tweak it a little bit like follows:
```python
>>> import re
>>> def email(email_address):
...    def is_valid(): #Changed only this
...        ok = re.search(r'(^[a-zA-Z0-9\.\+_-]+)@[a-zA-Z].[a-zA-Z\.]+$', email_address)
...        return True if ok else False
...    return is_valid
... 
>>> result = email('begueradj@begueradj.com')
>>> result()
True
>>> del email
>>> result()
True
>>> 

```
Do not you notice something weird? Well, actually there are two unexpected behaviors:

- The result of first call to `result()` is not expected because `email_address` is local to `email()`. This means any result of an operation made on `email_address` could no longer exist after `email()` is executed. But you clearly see this is not the case because `is_valid()` is called only after the return of `email()`.
- After deleting `email()` we still got information about the result of the operation done on its local variable `email_address`! Local variables are supposed to disappear after the execution of their enclosing scope! This is weird enough!

Actually this is achieved thanks to a feature of Python called closures. A closure consists of a nested function must point to a variable which is local to the enclosing function which must return the nest function. I introduced closures here because they are extensively used for Python decorators.

Maybe you can understand the Python shell better:
```python
>>> result.__closure__
(<cell at 0x7f80c224d7f8: str object at 0x7f80c04af228>,)
>>> 
```
# Decorators
If you have understood the previous secions, you are already equiped to learn properly about the decorators:
### Definition
I see lot of resources misleading newbies by telling them that the decorator is that weird notation in the form of. Other resources give a more formal definition that may not be that easy for a beginner to understand. Albert Einstein once wrote (or said): *If you can't explain it to a six year old, you don't understand it yourself*. So let me define what is a decorator in plain English: a decorator is a function which takes an other function as an argument in order to modify its behavior. Yes, as simple as that.

There are two formal definitions of decorators that maybe you want to learn about anyway. If this is your case then you have to know the first and almost instinctive formal definition commonly used to describe decorators is: *a decorator is a callable returning an other callable*. Do not be scared of the *callable* notion here. In fact, as in any other programming language you already know, you can call a function in Python; hence the adjective callable. More technically speaking, every object which implements the `__call__()` method is said to be a callable object.

The second formal definition you can hear about elsewhere is: *decorators are a metaprogramming concept just like metaclasses*. Honestly, this is not the best definition to provide for a newbie. But I let you know that *metaprogramming* simply means that one (or more) function or class of your program tries to modify other pieces (function, class) of your code. For the *metaclass notion*, I am not going to describe it here, but I may write a simple tutorial about it if you ask me through a comment below.  
### Example
As with other things in your daily life, the best definition you can provide is done by giving an example:
```python
def decorator(decorated):
   """This decorator wraps a nested function to add
   more functionality to the decorated() function.
   """
   
   def inner():
       """Here is modified the behavior of decorated()
       This is achieved by adding functionality to it.
       """
       print('Hello and welcome to my page.')
       decorated()
       print('I am of Algerian nationality living in Paris and born in a commune situated in Bejaia called Feraoun')
       
   return inner   
    
def decorated():
   print('I am Billal BEGUERADJ.')
   
   
# Main program goes here
if __name__ == '__main__':
   about = decorator(decorated)
   about()
```
Yes, we have programmed our first decorator. The previous example respects the definition and principles of a decorator: the callable (function) `decorator()` takes an other callable I named `decorated()`, it uses a nested function which name is `inner()` and returns a callable (this later one). Does this remember you of closures? That is why I said previously that closures are extensively used to implement decorators.<br/><br/>
The function `decorated()` is supposed to execute only this instruction: `print('I am Billal BEGUERADJ.')` but thanks to our decorator, we have decorated it to display a detailed self-representation.


```python
def decorated():
   print('I am Billal BEGUERADJ.')
   
   
# Main program goes here
if __name__ == '__main__':
   about = decorator(decorated)
   about()
```
are equivalent to:
```python
@decorator   
def decorated():
   print('I am Billal BEGUERADJ.')
   
   
# Main program goes here
if __name__ == '__main__':
   decorated()
```
I see many online tutorials teach you that `@decorator` is the decorator. That is very wrong. That line of code is what you may call *decoration notation* but it is not the decorator itself. I think the first advantage of decorators may be quite obvious: at least you reduced the size of your code (number of instructions) which thing is simply beneficial.
### Decorators of functions with arguments
The previous decorator is quite simple because the function it decorates takes no arguments. So how to decorate a function with parameters?<br/>Here is an example:
```python
import math

def find_root(func):
   """This decorator adds functionality to get_root().
   """
   
   def inner(a):
       """The most important thing you have to do here is to pass
       always the parameters of the callable object to your 
       nested function.
       """
       print('The root of {}: '.format(a))
       try:           
           return func(a)
       except ValueError:
           print('Opps ! Can not find the root of a negative value !')
           pass
		   
   return inner

# Decoration notation goes here
@find_root
def get_root(a):
   return math.sqrt(a)
   

# Main program starts here
if __name__ == '__main__':
   get_root(-4)
```
As you may have noticed, the trick is easy to grasp: always insert the name of the function to be decorated without parameters or even parentheses. Then pass the parameters of the function to decorated to the decorator’s nested function. That is it.<br/><br/>
Of course, you can incorporate all what I did in `inner()` into `get_root()`. For this specific example, you may think it is a matter of opinion. But when you will read below about the utility of decorators, you will change your mind.<br/><br/>
Note that in case the function to decorate has an arbitrary number of parameters packed within a list or dictionary, you can use `*` and `**` notations to respectively unpack them:
```python
def decorator(decorated):
    def nested(*args, **kwargs):
        print("+ ----------------------- +")
        return decorated(*args, **kwargs)
		print("+ ----------------------- +")
    return nested
```
### Chaining decorators
You can chain decorators. This means you can use multiple decorators to decorate the same function as you previously learned. To use such multiple decorated function, you can use this notation:
```python
@decorator1
@decorator2
@decorator3
decorated_function(*args)
```
# Decorators and OOP
Until now, you have seen, and hopefully, decorators in action. But you are likely to be asked by your employee to develop use object-oriented programming at your workplace. So how to implement decorators when you design the solution to your problem using classes?<br/><br/>
Let me re-write the very first decorator example you learned previously under the umbrella of OOP:
```python
class Decorator(object):
   def __init__(self, decorated):
       self.decorated = decorated
   def __call__(self):
       print('Hello and welcome to my page.')
       self.decorated()
       print('I am of Algerian nationality living in Paris and born in a commune situated in Bejaia called Feraoun')

@Decorator
def decorated():
   print('I am Billal BEGUERADJ')

if __name__ == '__main__':
   decorated()
```
To make the instances of a class callable, you need to use `__call__()`. To simplify things for you, think of `__call__()` as the equivalent of the nested function you learned from above examples.<br/>
Most newbie developers say decorators are easier to implement using classes.
### What are decorators useful for?
Even if the example described above are very simply, there is one main area where you can not get ready of decorators: when you develop a framework. Everything else you read in books is primarily-based opinion.<br/><br/>
When you develop a framework, it is useful to hide the inner implementations of the different functionalities you offer so that the user of your framework can focus in designing the solution to his actual problem. If you read about the architecture of Flask framework, you are already satisfied and surprised to be able to deal with client request so easily:
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'
```
But you can implement decorators for a large range of needs such as when you want to measure the performance of two versions of your code by calculating the time its takes to give me an output: 
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# We need this module for time operations
import time

def duration(func):
   """You can use this decorator to calculate the time
   a function takes to finish its execution.   
   """
   def nested(*args, **kwargs):
       """This is the nested function in which
       the operations are, properly speaking, done.
       """
       # Save the starting time of the function
       start = time.time()
       # Execute the function
       func(*args, **kwargs)
       # Caclulate the difference between present time and the previous one.
       since = time.time() - start
       # Easy to read output goes here
       print('+ --------------------- +')
       print('{}() executed in {} ms'.format(func.__name__, int(since * 1000)))
       print('+ --------------------- +')
   return nested
   
@duration
def greet(a):   
   print('I just print my argument: {}'.format(a))

# Main program starts here
if __name__ == '__main__':
   greet(2)
```
# Conclusion
Decorators are a flexible means to reuse code (compared to functions) and are more useful to interface functionalities for the users of your framework. You can google *PythonDecoratorLibrary* and you will land on a page providing many examples of decorators that you may reuse in your programs.
Your comments are welcome.
