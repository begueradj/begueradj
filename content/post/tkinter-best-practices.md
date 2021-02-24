---
title: "Tkinter best practices"
date: 2017-06-27T12:33:21+08:00
categories: ["development"]
draft: false
---
Tkinter is the standard Python interface to Tk/Tcl GUI toolkit. Programmers often wonder how to structure their tkinter program. In this article, I am going to answer to a question posted on StackOverflow which contributions drift away from some of software engineering fundamentals.

I see many questions posted on StackOverflow and CodeReview asking for tkinter best practices and improvements. While many of the contributions to those questions are interesting, they also teach bad and even harmful coding habits that result in scalability and maintenance issues. 

On the present short article, I am mainly going to address the [*Best way to structure a tkinter application*](https://stackoverflow.com/questions/17466561/best-way-to-structure-a-tkinter-application) question asked on StackOverflow and review what is good and what is bad in the provided answers.

### imports
First of all, do not use a wildcard import as stated in the accepted answer. This is important when you use [Tk themed widgets](https://docs.python.org/2/library/ttk.html). To be more clear, when you code:
```python
from tkinter import *
from ttk import *
```
This will result in substituting all `tkinter` widgets with `ttk` ones; but may be you want to use specific `ttk` widgets only.

I agree when the accepted answer states that this "*makes the code completely obvious when you are using Tkinter classes, ttk classes, **or some of your own***". However I disagree with the bold text statement because you should not even dare to code a class name as *Button* for example. Ths is what is written in the famous Clean Code book written by Robert Cecil Martin on page 50: *"not to encode the container type into the name."*

So the recommanded import is as follows:
```python
import tikter as tk # Python 3.x
import Tkinter as Tk # Python 2.x
```
### The main application is a class

While GUI are behind the development of OOP paradigm, there are other non OOP paradigms, such as "functional reactive programming"
, to  design GUIs. However, they do not have, AFAIK, [serious implementations](http://wiki.c2.com/?NonOopGuiMethodologies).  And since you will not get disappointed by relying on OOP concepts to design a tkinter GUI, I have to reproduce the nicely worded paragraph in the accepted answer to the above linked question:
*This gives you a private namespace for all of your callbacks and private functions, and just generally makes it easier to organize your code. In a procedural style you have to code top-down, defining functions before using them, etc. With this method you don't since you don't actually create the main window until the very last step. I prefer inheriting from tk.Frame just because I typically start by creating a frame, but it is by no means necessary.*
### The class initializer
The secret of a beautiful tkinter GUI design lays within the main class' initializer. Thus I give it the full attention.
There are few wrong things with the accepted answer however. Especially with the class' initializer:
```python
class MainApplication(tk.Frame):
    def __init__(self, parent, *args, **kwargs):
        tk.Frame.__init__(self, parent, *args, **kwargs)
        self.parent = parent
		
		<create the rest of your GUI here>
```
The first thing I would like to highlight is the number of parameters hold by the initializer: while `*args` and `**kwargs` allow a function to handle a variable number of parameters, this initializer's design must absolutely be avoided because parameters constitute a different level of abstraction from the function’s or class' name.  In other words, when you instantiate the "MainApplication()" class, you push the reader of your code (and yourself) to think on different levels of details: while maybe one could understand what your class is doing  just from its well chosen name, he will have to think  about  lower level details which are the arguments<sup>[1](#b1)</sup> you have to pass to it. A suitable tkinter class should not be, at worst, monadic. 

The `parent` parameter above is also called sometimes `master`. I personally prefer to call it `master` as there is one master widget by a tkinter application, while any widget can be a parent of an other widget. The accepted answer shows you must code:
```python
self.parent = parent
```
but it does not explain why. Actually there are are two reasons for that. The first one is common to all situations you may encounter in any programming context in any other programming language: "Don’t use routine parameters as working variables It’s dangerous to use the parameters passed to a routine as working variables. Use local variables instead"<sup>[2](#b2)</sup>. The second  reason for this way of doing things is that it will avoid you later in stumbling in many [`AttributeError`](https://docs.python.org/3/library/exceptions.html#AttributeError) exception situations.

The second line of code that should come right away after the `self.master = master` instruction is a `self.configure_gui()`  function<sup>[3](#b3)</sup>  Which only purpose is to save the general settings and configurations of your applications.

For instance, in a typical configuration function you may want to specify the size of the main window of your GUI and disable resizing it and, why not, set a custom title to your application:

```python
def configure_gui(self):
   self.master.title("Snake game")
   self.master.geometry("500x500")
   self.master.resizable(False, False)
```
The advantage of this approach is that you can decide to change the setting of your application whenever you want, and most importantly, you know where you will have to do it!

An other wrong thing taught by the contributions to that question is teaching to instantiate classes and creating application widgets directly from within the initializer itself. The good practice I recommend is to use a function called `create_widgets()` inside which you can create the widgets of your application in case you have very widgets to deal with or, in case you have to design a rich  GUI with different components, areas and surfaces, follow a [top-down](https://en.wikipedia.org/wiki/Top-down_and_bottom-up_design) approach by calling other smaller functions each having its own responsibility:
```python
def create_widgets(self):
   self.create_menu(...)
   self.create_game_board(...)
```
Last but not the least, follow the [SaYa]({filename}article6.md) idiom and never use more than one layout manager for the same containing parent widget.

So here is a basic skeleton for coding a tkinter based GUI:


```python
import tkinter as tk


class MainApplication(tk.Frame):

    def __init__(self, master):
        self.master = master
        tk.Frame.__init__(self, self.master)
        self.configure_gui()
        self.create_widgets()
   
   def congigure_gui(self):
       # ...
   def create_widgets(self):
       # ...

if __name__ == '__main__':
   root = tk.Tk()
   main_app =  MainApplication(root)
   root.mainloop()
        
        	
```






------
<sup><a name="b1"><sup>1</sup> </a>Parameters are the named variables in function signatures, and arguments are the values passed to functions.</sup><br/>
<sup><a name="b2"><sup>2</sup> </a>Steve McConnell,  *Complete Code*, page 176.</sup><br/>
<sup><a name="b3"><sup>3</sup> </a>You can choose a name of your own, but respect the naming conventions specified in PEP8.</sup>
