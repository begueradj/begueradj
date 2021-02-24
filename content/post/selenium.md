---
title: "Installing Selenium drivers on Ubuntu"
date: 2017-10-15T12:33:21+08:00
categories: ["development"]
draft: false
---
Here is a common exception raised when trying to invoke selenium’s webriver component for browser automation launching. Here is how to fix it.

For a professional need, I installed selenium testing framework on Ubuntu 17.04 LTS within a virtual environment I created using the recommended method I found on the documentation:
```python
pip install selenium
```
### Chrome
 I then tried to launch my website on  Chrome:
```python
from selenium import webdriver


driver = webdriver.Chrome()
driver.get("https://www.begueradj.com")

```
I have been slapped by this exception:
```python
Traceback (most recent call last):
  File "/home/begueradj/sel/lib/python3.5/site-packages/selenium/webdriver/common/service.py", line 74, in start
    stdout=self.log_file, stderr=self.log_file)
  File "/usr/lib/python3.5/subprocess.py", line 947, in __init__
    restore_signals, start_new_session)
  File "/usr/lib/python3.5/subprocess.py", line 1551, in _execute_child
    raise child_exception_type(errno_num, err_msg)
FileNotFoundError: [Errno 2] No such file or directory: 'chromedriver'

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/begueradj/sel/lib/python3.5/site-packages/selenium/webdriver/chrome/webdriver.py", line 62, in __init__
    self.service.start()
  File "/home/begueradj/sel/lib/python3.5/site-packages/selenium/webdriver/common/service.py", line 81, in start
    os.path.basename(self.path), self.start_error_message)
selenium.common.exceptions.WebDriverException: Message: 'chromedriver' executable needs to be in PATH. Please see https://sites.google.com/a/chromium.org/chromedriver/home
```
As I usual, I read the error message, and found the last line interesting:
```python
selenium.common.exceptions.WebDriverException: Message: 'chromedriver' executable needs to be in PATH.
```
As a robot, I googled about that weird "chromedriver" and landed on [this](https://sites.google.com/a/chromium.org/chromedriver/):

*WebDriver is an open source tool for automated testing of webapps across many browsers. It provides capabilities for navigating to web pages, user input, JavaScript execution, and more.  ChromeDriver is a standalone server which implements WebDriver's wire protocol for Chromium. ChromeDriver is available for Chrome on Android and Chrome on Desktop (Mac, Linux, Windows and ChromeOS).*

So I obeyed to the error message I got above by downloading the latest version of ChromeDriver, uncompressed the executable file and set a path to it like this:
```python
export PATH=$PATH:/home/begueradj/Downloads/chromedriver
```
### Firefox
Even if I do not use Mozilla Firefox on Ubuntu 17.04 and Ubuntu 16.10 for speed performance reasons, I was curious and tried to achieve the same goal I did with Chrome previously:
```python
from selenium import webdriver


driver = webdriver.Firefox()
driver.get("https://www.begueradj.com")
```
A similar exception has been raised:
```python
Traceback (most recent call last):
  File "/home/begueradj/sel/lib/python3.5/site-packages/selenium/webdriver/common/service.py", line 74, in start
    stdout=self.log_file, stderr=self.log_file)
  File "/usr/lib/python3.5/subprocess.py", line 947, in __init__
    restore_signals, start_new_session)
  File "/usr/lib/python3.5/subprocess.py", line 1551, in _execute_child
    raise child_exception_type(errno_num, err_msg)
FileNotFoundError: [Errno 2] No such file or directory: 'geckodriver'

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "so.py", line 4, in <module>
    driver = webdriver.Firefox()
  File "/home/begueradj/sel/lib/python3.5/site-packages/selenium/webdriver/firefox/webdriver.py", line 144, in __init__
    self.service.start()
  File "/home/begueradj/sel/lib/python3.5/site-packages/selenium/webdriver/common/service.py", line 81, in start
    os.path.basename(self.path), self.start_error_message)
selenium.common.exceptions.WebDriverException: Message: 'geckodriver' executable needs to be in PATH. 
```
How to fix this issue is highlighted by the last line of the above error message:
```python
selenium.common.exceptions.WebDriverException: Message: 'geckodriver' executable needs to be in PATH. 
```
As previously, I googled about the weird 'geckodriver' and found that it is a web browser engine used  most noticeably the Firefox web browser. So I simply [downloaded](https://github.com/mozilla/geckodriver/releases) *geckodriver*, uncompressed it and defined a path to the executable file:
```python
export PATH=$PATH:/home/begueradj/Downloads/geckodriver
```
I believe you can proceed similarly whatever your favorite web browser is.
