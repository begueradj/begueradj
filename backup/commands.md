---
title: "Linux commands"
date: 2018-12-18T12:33:21+08:00
categories: ["Development"]
draft: false
---

### Preamble

This article is going to be updated and become longer as the time goes on. I am listing some of the commands I rarely use but I do not want to forget. Of course, I gleaned most of them on the web, sorry for not being able to mention the resources for each listed one below.

1. [Emacs](#emacs)
2. [Git](#git)
3. [Fast copy data](#datacopy)

<a name="emacs"></a>
## 1. Emacs

### Set JS indentation to 2 in Vue.js and Nuxt.js for vue-mode package

When developing with Vue.js or Nuxt.js, my JavaScript code indentation always works as if I set it to 4 spaces. I tried all sorts of solutions such as:
```lisp
;; Set Javascript indentation to 2 spaces
(setq-default indent-tabs-mode nil)
(setq-default tab-width 2)
```
but none of them worked except this one I figured out some time ago by digging into the documentation:
```lisp
;; Set Javascript indentation to 2 spaces
(setq indent-tabs-mode nil
      js-indent-level 2)
```
or simply:
```lisp
;; Set Javascript indentation to 2 spaces
(setq js-indent-level 2)
```
### How to set Eslint to auto-fix

This is an issue I encoutered when developing in Nuxt.js where Eslint always complains to me about mixing tabs and white spaces. It was tedious to me to go line by line to fix the indentation, before I went deeper into the documentation:
```cmd
npx eslint --fix path/to/file
```
### Stop Eslint from preventing the use of console.log()

Whenever I wanted to debug my front-end code by using `console.log()`, Eslint complained about it and I could not run my code. There is a plugin that deals with it, but I did not try it since I found a simpler solution by digging into the documentation: I simply had to add `"no-console": "off"` to the rules by editing my **~/.eslintrc.json** file:
```javascript
{   
    "rules": {
        "no-console": "off"
    }
}
```
<a name="git"></a>
## 2. Git

### Delete local and remote branches
Delete the remote branch:
```cmd
git push --delete origin branch_name>
```
Then delete the local branch:
```cmd
git branch -d <branch_name>
```
### Fetch remote Git branches
The best is to fetch them one by one. First list them:
```cmd
git branch -a
```
Or:
```cmd
git branch -r
```
Then fetch the one you want:
```cmd
git fetch && git checkout <remote_branch_name>
```
<a name="datacopy"></a>
## 3. Fast copy data
Fast copy data locally:
```cmd
rsync -avzr folder/to/copy where/to/copy
```
Fast copy data from server to local machine:
```cmd
rsync -avzr username@server-ip-address:/home/rest_of_path where/to/copy/locally
```
The above command works while the SSH session is open (`ssh username@server-ip-address`). Note that `scp` is used in the same way but it is slower for large data than `rsync` and it establishes the SSH connexion by itself.
