---
title: "XML parsing with minidom"
date: 2017-04-23T12:33:21+08:00
categories: ["development"]
draft: false
---
This is a basic tutorial to introduce `minidom`. In fact, this is not really a tutorial but just a few list of notes about this library as there is already a short but good tutorial about it online<sup><a href="#1">1</a></sup>.

Let us consider this XML data:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<stock>
   <product id="p1">
      <title>Product 1</title>
      <reference>999-X</reference>
      <price>20.10</price>
      <dimensions>
         <weight>4</weight>
         <height>50</height>
         <width>20</width>
      </dimensions>
   </product>
   <product id="p2">
      <title>Product 2</title>
      <reference>999-B</reference>
      <price>55.5</price>
      <dimensions>
         <weight>3.99</weight>
         <height>50</height>
         <width>20</width>
      </dimensions>
   </product>
</stock>
```
You can either save this as an XML file or consider it as a string. In both cases, you will need to import the library:
```python
from xml.dom import minidom
```
In the later case, copy/paste the XML data above:
```python
xml_data = """
# Copy/paste previous XML data above here.
"""
```
And then parse it using `parseString()` function which returns an XML Document object:
```python
from xml.dom import minidom
xml_doc = minidom.parseString(xml_data)
```
If you encounter this error message:
```python
xml.parsers.expat.ExpatError: XML or text declaration not at start of entity: line 2, column 0
```
Then you have to remove the leading and trailing characters due to the copy/paste text operation:
```python
from xml.dom import minidom

xml_data = """
# Copy/pase above XML data here.
"""
xml_data = xml_data.strip()
xml_doc = minidom.parseString(xml_data)
```
In case you decide to save the data in an XML file (with `.xml` extension) then you need to use `parse()` to parse it:
```python
xml_doc = minidom.parse(xml_file_name)
```
Once the XML Document object xml_doc obtained, the rest of the operations remain the same for both cases.

    Getting the root element
```python
>>> xml_doc.documentElement
<DOM Element: stock at 0x7ff5469ebb48>
>>> xml_doc.documentElement.tagName
u'stock' 
```
We have only two products as direct children of stock which is the root element in our XML Document `xml_doc`. So we expect the number of children elements of `root = xml_doc.documentElement` to be 2, but we get a different result:
```python
>>> root = xml_doc.documentElement
>>> len(root.childNodes)
5
```

To understand this weird result, you can inspect the output of `root.childNodes`:
```python
>>> root.childNodes
[<DOM Text node "'\n   '">, <DOM Element: product at 0x7f4550c59470>, <DOM Text node "'\n   '">, <DOM Element: product at 0x7f4550c59930>, <DOM Text node "'\n'">]```

Now things become clear: we see 3 additional Text children consisting of the new lines `\n` we did not think about. This is important to keep in mind when especially when you try to get values of certain child nodes:
```python
>>> root.childNodes[0].nodeValue
'\n   '
>>> root.childNodes[1].nodeValue
>>> root.childNodes[1]
<DOM Element: product at 0x7f4550c59470>
>>> root.childNodes[1].childNodes
[<DOM Text node "'\n      '">, <DOM Element: title at 0x7f4550c59508>, <DOM Text node "'\n      '">, <DOM Element: reference at 0x7f4550c595a0>, <DOM Text node "'\n      '">, <DOM Element: price at 0x7f4550c59638>, <DOM Text node "'\n      '">, <DOM Element: dimensions at 0x7f4550c596d0>, <DOM Text node "'\n   '">]
```
For example, let us display the tag name of the root’s child which has `id == p1`:
```python
>>> for child in root.childNodes:
...     if child.childNodes:
...             if child.hasAttribute('id') and child.getAttribute('id') == 'p1':
...                     print(child.tagName)
... 
product
```
One important thing to be aware of is that minidom is very memory consuming and I think this is due to the fact it is heavilty based on recursive functions. It can be handy only when processing small files of XML data.
An other thing to know is minidom does not support XPATH expressions.
To remedy to the two forementioned drawbacks, it is recommended to use the `lxml` library.

The library is quite well documented<sup><a href="#2">2</a></sup>. In case you do not have Internet access to check the documentation, you can always the most important minidom available functions by calling the XML Document object you created this way:
```python
dir(xml_doc)
```
And you can inspect an individual function (chosen from the list the forementioned line outputs) this way:
```python
help(xml_doc.getElementsByTagName)
```

---
<a name="1"></a>
<sup>1</sup>.https://wiki.python.org/moin/MiniDom
<a name="2"></a><br/>
<sup>2</sup>.https://docs.python.org/3.0/library/xml.dom.html
