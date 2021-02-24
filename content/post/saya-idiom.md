---
title: "tkinter: the SaYa idiom"
date: 2017-03-19T12:33:21+08:00
categories: ["development"]
draft: false
---
Python is a paramount programming languages. The same is to be said about user graphical interfaces. *SaYa* is an idiom I devised for tkinter developers to help them build less error-prone applications.

- [Background](#background)
- [Explanation](#explanation)
- [Motivation](#motivation)
- [Statement](#statement)
- [Advantages](#advantages)

<a name="background"></a>
### Background

This morning I landed on [this](https://stackoverflow.com/questions/42882330/positional-argument-error-classes-and-tkinter) question asked on StackOverflow where the OP stumbled on this error he shared through his/her comment to my answer:
```python
NoneType' object has no attribute 'destroy'
```
The origin of the problem is that the OP created a button widget within the class initializer and positioned in the **same time**:
```python
self.widget_object = ttk.WidgetType(arguments).pack()
```
When a mouse click event is performed, the callback is executed:
```python
self.widget_object.destroy()
```
<a name="explanation"></a>
### Explanation
The Python interpreter raises a `TypeError` exception late at runtime because it was assigned to destroy  the `None` singleton object. 

In Python, everything is an object (yes, including classes), to achieve this principle, Python forces functions without a return type to return the `None` object. This is the case of the 3 methods used to place widgets in tkinter: `place()`, `pack()` and `grid()`. 

This means the value of `self.widget_object` in the initializer is `None`. No wonder the interpreter can not apply `destroy()` method on it.
<a name="motivation"></a>
### Motivation
Similar situations are frequent when developing GUIs in tkinter among newbies as well as experienced developers. That is why I created the SaYa idiom  which I name using my parents's first names first syllables to thank them for giving me the opportunity to live and a chance they were not able to afford for themselves: to study.
<a name="statement"></a>
### Statement
SaYa idiom states: When building a GUI in tkinter, always place widgets **after** their creation.
<a name="advantages"></a>
### Advantages
An idiom has at least one reason to exist. Here are the benefits of SaYa on tkinter GUIs:

- Scalability
- Ease of maintenance
- Narrowing down the attack surface on applications as this reduces bugs

SaYa is an idiom that proves to be benefical for all Python-tkinter developers.
