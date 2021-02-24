---
title: "A slightly improved version of bubble sort algorithm"
date: 2018-07-16T12:33:21+08:00
categories: ["development"]
draft: false
---
My wicked brain gave birth to an improved version of the classic bubble sort algorithm. Here it is.

This document is not about explaining the bubble sort algorithm as it  already  exists a good literature out there about it, and it is a classic algorithm studied and known by all CS students all over the world.

There are, obviously, two improved versions of bubble sort algorithm; and thanks to a [question](https://codereview.stackexchange.com/questions/177857/python-optimizing-bubble-sort) asked the Code Review website regarding the first one of them which is:
```python
procedure bubbleSort( A : list of sortable items )
    n = length(A)
    repeat
        swapped = false
        for i = 1 to n-1 inclusive do
            if A[i-1] > A[i] then
                swap(A[i-1], A[i])
                swapped = true
            end if
        end for
        n = n - 1
    until not swapped
end procedure
```
Here is its Python implementation (as provided by the OP):
```python
def bubble_sort(array):
    n = len(array)
    swapped = True
    while swapped:
        swapped = False
        for i in range(n - 1):
            if array[i] > array[i + 1]:
                array[i], array[i + 1] = array[i + 1], array[i]
                swapped = True
        n -= 1
    return array
```
My version removes away a good bunch of the algorithm’s complexity by getting totally rid of the swapped variable flag above and all the instructions inherent to it:
```python
def bubble_sort(array):
    n = len(array)
    while n:
        for i in range(n - 1):
            if array[i] > array[i + 1]:
                array[i], array[i + 1] = array[i + 1], array[i]
        n -= 1
    return array
```

The lengthier the input array is, the more tangible the complexity my version gets rid of becomes important. Well ... maybe this deserve to be mentioned in the literature too :)
