---
title: "Introduction to Flask"
date: 2017-05-08T12:33:21+08:00
categories: ["development"]
draft: false
---
This articles shakes hands with Python web development. It introduces the Flask microframework. It is rather a light and soft introduction in that it tells the newbies only what they need to know  at this stage. It is intentionally kept short, simple and without confusing terminology.

- [What is Flask?](#definition)
- [Installation](#installation)
- [Components](#components)
- [Example](#hello)

<a name="definition"></a>
### What is Flask?
Flask is a web development Python framework. More precisely, it is a microframework because it is so small that you could quite quickly be familiar with its source code. It is small yet powerful in that you can develop fully-fledged web applications with it. If none of the available extensions satisfy the needs, one can develop his own ones as Flask core is small in scope and hence one can grasp its logic within a reasonable period of time.
<a name="installation"></a>
### Installation
To avoid dependencies and versions conflicts  the most convenient way to install Flask is to create a semi-isolated virtual environment.

Below is how to install virtualenv on Ubuntu 16.04 LTS for Python 3:
```python
sudo apt-get install python3-pip
sudo pip3 install virtualenv
```
Now to create a private copy of the Python 3 interpreter, `virtualenv` command can be run followed with a folder name:
```python
virtualenv flask_dev
```
An output similar to this one will appear on Terminal:
```python
Using base prefix '/usr'
New python executable in /home/begueradj/flask_dev/bin/python3
Also creating executable in /home/begueradj/flask_dev/bin/python
Installing setuptools, pip, wheel...done.
```
To install the required packages without messing up with the global Python interpreter of the operating system in use:
```python
 cd flask_dev
 source bin/activate 
```
 
 The last command line above will enable the virtual environment named `flask_dev`:
 
```python
 (flask_dev) begueradj@h4ck:~/flask_dev$ 
```
Only then Flask package can be installed properly:
```python
pip install flask
```
`pip`  is an installer program automatically added to virtual environments created using `virtualenv` command.
The Flask installation can be checked:
```python
(flask_dev) begueradj@h4ck-B:~/flask_dev$ python
Python 3.5.2 (default, Nov 17 2016, 17:05:23) 
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import flask
>>> 
```
<a name="components"></a>
### Components
After running `pip install flask`, the installation process displays a component without which Flask can not work: Werkzeug which is a Web Server Gateway Interface receiving clients' requests and forwarding them to a Flask application instance to deal with them properly. As it is hard to write HTML output within Python code, Flask relies on Jinja2 which is designer-friendly templating language . Both Werkzeug and Jinja2 are developed by Flask authors and they are all installed together with the previous command.
<a name="hello"></a>
### Example
To demonstrate the classic *Hello World!* example, here is a simple way to do it:
```python
from flask import Flask
hi = Flask(__name__)
```
All clients’ requests are passed to the `Flask` instance through the WSGI protocol to process them. The `__name__` argument is there to help Flask to determine the root path of the application.
```python
@hi.route('/')
def index():
   return '<h1><font color="red">Hello World!</font></h1>'
```
`route()` is the simplest way to define a route on which a request lands. It is simply a [decorator]({filename}article2.md) that registers the `index()` function as a route.  Each client request landing on the root URL `/` is forwarded to `index()` which, in this case, simply returns an HTML string. In Flask, `index()` is called a *view function*.
```python
if __name__ == '__main__':
   hi.run(debug=True)
```
To run the server, `run()` is called. This function can take several optional arguments. During the development process, it is useful to activate the debugger and the reloader. The server will keep running until it is explicitly stopped (for instance, by pressing <kbd>Ctrl</kbd> + <kbd>C</kbd> on the Terminal session).

Saving the previous lines in a `hello.py` file:
```python
from flask import Flask


hi = Flask(__name__)

@hi.route('/')
def index():
   return '<h1><font color="red">Hellow World!</font></h1>'

if __name__ == '__main__':
   hi.run(debug=True)
```
and executing it with as a usual Python file will run this simple Flask application. The Terminal session will show the debugger activated too:
```python
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 169-150-169
```
Opening the browser following this address `http://127.0.0.1:5000/` will lead to displaying *Hello World!*:

![Hello World!]({filename}../images/flask_hello.png)

 And this client HTTP request can be seen on Terminal:
 ```python
 127.0.0.1 - - [09/May/2017 17:05:36] "GET / HTTP/1.1" 200 -
 ```
