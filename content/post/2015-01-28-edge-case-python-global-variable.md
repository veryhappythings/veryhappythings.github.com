---
date: '2015-01-28T00:00:00Z'
title: An Edge Case Concerning Python Global Variables
---

A colleague of mine just came across a rather interesting edge case when
working with Python global variables. Of course, most of the time, you should
just try to avoid them altogether, but if you do use them you might come
across this.

If you have a global variable, and you assign to it in a function without
using the global keyword, your code will fail to compile. This can result in a
confusing error message. Here's a simplified example:

```python
TEST_VARIABLE = 'Hello!'

def test_function():
    print 'Here we are, in a function.'
    print 'We want to use our global variable:'
    print TEST_VARIABLE
    print 'If the function ended here, this would work fine.'

    print "Now, let's define our variable as local."
    TEST_VARIABLE = 'Hola!'
    print 'by adding the line above, we cause this function to fail at line 6.'

test_function()
```


Here's the output that you get:

```
[enoch.local][code] ➔ python test_global.py
Here we are, in a function.
We want to use our global variable:
Traceback (most recent call last):
  File "test_global.py", line 13, in <module>
    test_function()
  File "test_global.py", line 6, in test_function
    print TEST_VARIABLE
UnboundLocalError: local variable 'TEST_VARIABLE' referenced before assignment
```


So, line 10 causes an error in line 6. Pretty confusing if you don't know what
you're looking for! The simple solution is, never assign to a global variable
in a function. It is bad practice. If you feel the need to do something like
that, chances are you'd be better served by creating a new local variable.
