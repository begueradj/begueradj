---
title: "Python singleton design pattern"
date: 2017-04-08T12:33:21+08:00
categories: ["development"]
draft: false
---
This article describes the main strategies to implement the Singleton design pattern in Python. I am going to skip the whole very subjective "use and do not use singleton" conundrum.


- [Introduction](#introduction)
- [Definition](#definition)
- [Implementation](#implementation)
    - [Module](#module)
    - [Traditional](#traditional)
	- [Decoractor](#decorator)
    - [Metaclass](#metaclass)
	- [Borg](#borg)
- [Conclusion](#conclusion)

<a name="introduction"></a>
### Introduction
There are developers who simply call to *"ban"* this design pattern. Personally, I think while it does not respect the single responsibility principle, the singleton may still be useful in situations where you need to control a concurrent access to a shared resource, or want to use it to implement other design patterns. 
<a name="definition"></a>
### Definition
On page 144 of the Gof4 book, the singleton design pattern's stated intent is to *"ensure a class only has one instance"*. 
<a name="Implementation"></a>
### Implementation
The singleton design pattern must be implemented in such a way that the sole class instance must be reachable via a global access point -namely a function, and can be extensible by the means of inheritence. Below are listed some methodologies Python uses to concrete it.
<a name="module"></a>
###### Module
The easiest approach to fulfill the singteton design pattern goal is to keep the global state in private variables within module which, in turn, must provide a way to access them by means of public functions.

As an example, here is `singleton.py` file:
```python
_firstname = 'Begueradj' #private state

def get_firstname(): #access point
   return _firstname

```
The main application makes use of it as follows:
```python
import singleton


class UseSingletonModule:

   def __init__(self):
       self.firstname  = singleton.get_firstname()
       self.lastname = 'Amazigh'
       
   def print_fullname(self):
       print('Full name: {} {}'.format(self.firstname, self.lastname))


if __name__ == '__main__':
   singleton_instance = UseSingletonModule()
   singleton_instance.print_fullname()
```
This is a practical approach but the main trivial issue with it is that there is no way to re-use the code through inheritance. This means the module approach does not fully respect the singleton design pattern. One can also argue this may sound a "coward" implementation in that may be most of other high level programming languages could do the same. This can also anger purists in that it is neither  pythonic nor does it take advantage of the object-oriented concepts.
<a name="traditional"></a>
###### Traditional
To remedy to the drawbacks of the previous approach, there is a traditional way to implement the singleton design pattern in Python:
```python
class TraditionalSingleton(object): 

   def __new__(cls): 
       if not hasattr(cls, 'instance'): 
           cls.instance = super(Singleton, cls).__new__(cls) 
       return cls.instance
   
   def __init__(self):
       self.data_test = 'begueradj'       
```
Here an advantage is taken from the special static method `__new__()` which helps to control instance creation. Unlike `__init__()`, it always takes as a parameter the class `cls` in which it is invoked and returns an instance of it.

The Python built-in function `hasattr()` checks if the class `cls` has an attribute called *'instance'*. If that is not the case, the *'instance'* attribute is created and returned.

A better explanation of this implementation can be through the use of `TraditionalSingleton()` class:
```python
>>> first_instance= TraditionalSingleton() 
>>> second_instance = TraditionalSingleton() 
```
Two instances of `TraditionalSingleton()` class are created. In reality, they are the same instance:
```python
>>> first_instance is second_instance True 
```
Hence modifying `data_test` variable in any instance will be automatically mirrored in the other instance:
```python
>>> first_instance.data_test = 'begueradj.com'
>>> second_instance.data_test
'begueradj.com'
```
The main issue of this approach remains its inconvenience to use inheritance.
<a name="decorator"></a>
###### Decoractor
There are many ways to implement the singleton using a decorator. Here is one of the implementations I found on StackOverflow:
```python
class Singleton:

   def __init__(self, cls):
        self._cls = cls

    def get_instance(self):        
        try:
            return self._instance
        except AttributeError:
            self._instance = self._cls()
            return self._instance

    def __call__(self):
        raise TypeError('Use Singleton.get_instance().')

    def __instancecheck__(self, inst):
        return isinstance(inst, self._cls)
```
Here is how to force a class to be a singleton:
```python
@Singleton
class SingletonClass:
   # do whatever needed
```
This is how to instantiate `SingletonClass()` class:
```python
singleton_class_instance = SingletonClass.get_instance()
```
The main drawback with this implementation is that it is not thread-safe.
<a name="metaclass"></a>
###### Metaclass
An object is an instance of a class which is itself an instance of a metaclass:
```python
class Singleton(type):
    _instances = {}
    def __call__(cls):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__()
        return cls._instances[cls]
```
A class can be declared singleton like follows:
```python
class SingletonClass(metaclass=Singleton):
    # do whatever needed
```
While this approach copes well with inheritance, I still personally prefer the decorator approach which is handy.
<a name="borg"></a>
###### Borg
The Borg approach states that the identity of the instantiated objects is not important. Borg focus on the state which must be common and shared. This allows the creation of as many instances as needed, but any of these instances which modifies the state mirrors the modification to all the remaining instances. This is why Borg is also referred as **monostate** design pattern:
```python
class Borg:
   _shared_state = {}
   
   def __init__(self):
       self.__dict__ = self._shared_state
```
Any class which inherits from the above one becomes a Borg class itself:
```python
class SingletonExample(Borg):

   def __init__(self, name=None):
       Borg.__init__(self)
   # Do whatever needed

```
Alex Martelli, the author of Borg, considers this as a design pattern of its own. While some others consider it just a special singleton implementation. I personally belong to this later group.
<a name="conclusion"></a>
### Conclusion
I have gone briefly gone through the  main approaches Python uses to implement it. You can also design your own implementation. It is up to you the developer to decide which one is more suitable to your case.
