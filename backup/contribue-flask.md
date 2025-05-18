---
title: "How to contribute to Flask"
date: 2017-05-13T12:33:21+08:00
categories: ["development"]
draft: false
---
I am an opensource advocate. I want to contribute to Flask microframework. So I followed the official documentation on GitHub where I stumbled on 6 issues. The problem is that the documentation itself is misleading and that can be a good starting point. 

I want to contribute to Python Flask microframework. I read its [CONTRIBUTING.rst](https://github.com/pallets/flask/blob/master/CONTRIBUTING.rst) file from GitHub. I encountered few issues because the documentation is not clear for me. It even [sounds](https://stackoverflow.com/questions/43923756/errors-when-trying-to-run-flasks-testsuite) to be outdated.

I share here the steps that will allow you to later contribute to Flask. I hope they are more straightforward and clear than the official *How to contribute to Flask*.

**Step 1.** I cloned the repository:
```python
git clone https://github.com/pallets/flask.git
```
The cloned repository is called by default flask. I renamed to something else:
```python
mv flask/ contribute_to_flask/
```
I know it is all Ok to keep the name as it is, but because it contains a subfolder called also flask, I wanted to distinguish the parent from the child folder:
```python
cd contribute_to_flask/ && ls
artwork           docs      Makefile     setup.cfg              tox.ini
AUTHORS           examples  MANIFEST.in  setup.py
CHANGES           flask     README       test-requirements.txt
CONTRIBUTING.rst  LICENSE   scripts      tests
```

**Step 2.** I create a virtual environment inside *contribute_to_flask* folder and installed `pytest` and `tox`:
```python
virtualenv my_venv

```
The virtual environment can be anywhere except inside *contribute_to_flask/flask/* folder. This is in order to avoid Flask to think that it's installed rather than running locally. The test doesn't expect the project to appear to be installed<sup>[1](#b1)</sup>.
I then activated the virtual environment:
```python
cd my_venv
source bin/activate
```
I installed `pytest` to perform unit testing:
```python
pip install pytest
```
I also installed `tox` in order to *run all tests against multiple combinations Python versions and dependency versions*
```python
pip install tox
```
**Step 3.** I *installed Flask as an editable package using the current source:*
```python
pip install --editable .

```
**Step 4.** I installed the test requirements:
```python
pip install -r test-requirements.txt
```
The `-r` option is there to specify that pip must is going to install requirements usually listed in a plain text file.

**Step 5.** I run the tests:
```python
pytest tests/
```
I got this output: *332 passed, 10 skipped in 32.86 seconds*.

**Step 6.** I installed and run the test coverage:
```python
pip install pytest-cov
```
The test coverage:
```python
pytest --cov=flask tests/
```
It seems there is a good code coverage:
```python
----------- coverage: platform linux, python 3.5.2-final-0 -----------
Name                    Stmts   Miss  Cover
-------------------------------------------
flask/__init__.py          17      0   100%
flask/__main__.py           4      4     0%
flask/_compat.py           52     28    46%
flask/app.py              580     42    93%
flask/blueprints.py       159     36    77%
flask/cli.py              275     85    69%
flask/config.py            90      2    98%
flask/ctx.py              151     23    85%
flask/debughelpers.py      91     12    87%
flask/ext/__init__.py       7      0   100%
flask/exthook.py           61      3    95%
flask/globals.py           26      0   100%
flask/helpers.py          324     40    88%
flask/json.py              89     13    85%
flask/logging.py           46      1    98%
flask/sessions.py         143      8    94%
flask/signals.py           29      2    93%
flask/templating.py        82      3    96%
flask/testing.py           65      3    95%
flask/views.py             41      1    98%
flask/wrappers.py          74      4    95%
-------------------------------------------
TOTAL                    2406    310    87%
```

That is it. Now I am ready to go further.

------
<sup><a name="b1"><sup>1</sup> </a>*test_main_module_paths fails on RHEL and Ubuntu when using Amazon EC2*, [#1879](https://github.com/pallets/flask/issues/1879#issuecomment-300842507)</sup>
