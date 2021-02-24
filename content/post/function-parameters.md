---
title: "Do not make a direct use of function parameters"
date: 2017-09-04T12:33:21+08:00
categories: ["development"]
draft: false
---
Today, I have answered a  question on Code Review website. The OP’s code sample itself was simple, but a user commented my answer stating that he never heard about function’s parameters should not be used directly.

Following my answer to [this question](https://codereview.stackexchange.com/questions/174763/what-is-an-idiomatic-way-to-write-if-a-if-b-if-a-or-b-else), I wrote this: "*Whatever the programming language you use, do not use the parameters inside your function*" and I explained what I meant:
```python
def my_function(param):
   par = param
   # Now work with "par" instead of "param"
```
 A user was curious about the last section of my thread and commented: "*Is your point on 'do not use function params directly, no matter what language' (paraphrased) definitely correct? I'm not doubting you, just haven't seen this before with Python*".
 
 Of course, with the low reputation I have on that website, I could not  respond to the comments in question without justifying my argumentation with a more trusted and official resource. This has been hard for me to remember where I exactly read this information. Actually this is even harder to justify now that I do not see, AFAIK, where this is written other than in a singe book that made my day.
 
 The book in question is called: "**Code Complete: A Practical Handbook of Software Construction***". On its Chapter 7, section 7.5, *Steve McConnell* has written: **Don’t use routine parameters as working variables** It’s dangerous to use the parameters passed to a routine as working variables. Use local variables instead*. The author has provided a Java sample code similar to the one of my answer:
```java
 int Sample( int inputVal ) {
    inputVal = inputVal * CurrentMultiplier( inputVal );
    inputVal = inputVal + CurrentAdder( inputVal );
    ...
   return inputVal;
}
```
And I wanted to share the experience and text of the author about it:
>In this code fragment, inputVal is misleading because by the time execution reaches the last line, inputVal no longer contains the input value; it contains a computed value based in part on the input value, and it is therefore misnamed. If you later need to modify the routine to use the original input value in some other place, you’ll probably use inputVal and assume that it contains the original input value when it actually doesn't.

And the solution provided in *Complete Code* is exactly what my memory reproduced through my answer:
```java
 int Sample( int inputVal ) {
    int workingVal = inputVal;
    workingVal = workingVal * CurrentMultiplier( workingVal );
    workingVal = workingVal + CurrentAdder( workingVal );
	...
   return workingVal;
}
```

The author wrote a side note in which he stated this gives you the advantage of making use of *inputVal* if needed within the above function’s body, or elsewhere in the program. The author adds:

- Introducing the new variable *workingVal* clarifies the role of *inputVal* and eliminates the chance of erroneously using *inputVal* at the wrong time.
- Assigning the input value to a working variable emphasizes where the value comes from.

If you  still are not convinced of the author’s experience and advice, let me share with you a practical must use case of this rule, by creating a primitive tkinter based GUI that disaplays a simple button:
```python
import tkinter as tk

class Demo(tk.Frame):
   def __init__(self, master):
       tk.Frame.__init__(self, master)
       self.create_widgets()
       
   def create_widgets(self):
       self.button = tk.Button(master, text='Demo')
       self.button.pack()

if __name__ == '__main__':
   root = tk.Tk()
   d = Demo(root)
   root.mainloop()
```
In the initalizer, I ignored by purpose our magic rule regarding the `master` parameter. The compiler triggers this exception: `NameError: name 'master' is not defined
`. 

Let us re-write the same program and be compliant to our rule:

```python
import tkinter as tk

class Demo(tk.Frame):
   def __init__(self, master):
       self.master = master
       tk.Frame.__init__(self, self.master)
       self.create_widgets()
       
   def create_widgets(self):
       self.button = tk.Button(self.master, text='Demo')
       self.button.pack()

if __name__ == '__main__':
   root = tk.Tk()
   d = Demo(root)
   root.mainloop()
```
Even if this situation is, maybe, not the best to explain the importance of this rule as it involves class variables, the rule is more practical than it seems. And for sure you can find out many other practical situations where this rule must be considered and respected.
