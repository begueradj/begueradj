---
title: "PHP unit testing"
date: 2018-08-20T12:33:21+08:00
categories: ["development"]
draft: false
---

These are the steps to install and set the necessary tools to run unit tests in PHP projects:

Installing Composer on Ubuntu 16.04 LTS:
```cmd
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```
Installing PHPUnittest:
```json
{   
    "require-dev": {
        "phpunit/phpunit": "^7"
    }
}
```
Install xdebug on Ubuntu 16.04 LTS.

Run the tests:
```cmd
./vendor/bin/phpunit --bootstrap vendor/autoload.php tests/EmailTest.php 
```
Run the code coverage:
```
./vendor/bin/phpunit  --coverage-html . tests/EmailTest.php
```
Setting phpunit.xml file I created the phpunit.xml file in the root directory of the project:
```cmd
find -not -path './vendor/*'
```
Result:
```cmd
./tests/EmailTest.php
./test.php
./composer.json
./composer.lock
./vendor
./src
./src/Email.php
./phpunit.xml
```
This is its content:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit bootstrap="./vendor/autoload.php">
    <testsuites>
        <testsuite name='begueradj'>
            <directory>./tests/</directory>
        </testsuite>
    </testsuites>
    <filter>
        <whitelist>
            <directory suffix=".php">./src/</directory>
        </whitelist>
    </filter>
</phpunit>
```
