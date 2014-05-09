title: 'Lua learning notes (3)'
date: 2014-05-09 06:10:24
tags: Lua
description: Note on Chapter 3 of "Programming in Lua, 3rd Edition"
---

# Expressions

## Arithmetic Operators

Exponentation in Lua use `^` ( e.g., `x^2`)

For real operands, `%` has some extra uses. For instance, `x%1` is the fractional part of `x`, and so `x-x%1` is its integer part. Similarly, `x-x%0.01` is `x` with exactly two decimal digits:

```x = math.piprint(x - x%0.01)       --> 3.14
```
## Ralational Operators

The negation of equality in Lua is `~=`. We can apply both operators to any two values. If the values have different types, Lua considers them not equal. Otherwise, Lua compares them according to their types. Specifically, nil is equal only to itself.Lua compares tables and userdata by reference, that is, two such values are considered equal only if they are the very same object. For instance, after the code

```a = {}; a.x = 1; a.y = 0
b = {}; b.x = 1; b.y = 0 
c=a
```you have `a==c` but `a~=b`.
## Logical Operators
The `and` operator returns its first argument if it is false; otherwise, it returns its second argument. The `or` operator returns its first argument if it is not false; otherwise, it returns its second argument:
```
print(4 and 5)    --> 5
print(nil and 5)    --> nil
print(false and 5)    --> false
print(4 or 5)    --> 4
print(false or 5)    --> 5
```
A useful Lua idiom is `x=x or v`, which is equivalent to 

```
if not x then x = v end
```That is, it sets `x` to a default value `v` when `x` is not set (provided that `x` is not set to `false`).
Another useful idiom is `(a and b)or c`, or simply `a and b or c`, because `and` has a higher precedence than `or`. For instance, we can select the maximum of two numbers x and y with a statement like```max = (x > y) and x or y```When `x > y`, the first expression of the and is true, so the and results in its second expression (`x`), which is always true (because it is a number), and then the or expression results in the value of its first expression, `x`. When `x>y` is false, the and expression is false and so the or results in its second expression, `y`.
The not operator always returns a boolean value:

```print(not nil)    --> trueprint(not false)    --> trueprint(not 0)    --> falseprint(not not 1)    --> trueprint(not not nil)  --> false
```## Concatenation
Lua denotes the string concatenation operator by `..` (two dots). If any operand is a number, Lua converts this number to a string. 

```print("Hello " .. "World")  --> Hello Worldprint(0 .. 1)               --> 01print(000 .. 01)            --> 01
```
## The Length Operator

The length operator works on strings and tables. On strings, it gives the number of bytes in the string. On tables, it gives the length of the sequence represented by the table.The length operator provides several common Lua idioms for manipulating sequences:
```print(a[#a])    -- prints the last value of sequence 'a'a[#a] = nil    -- removes this last valuea[#a + 1] = v    -- appends 'v' to the end of the list
```
The length operator is unpredictable for lists with holes (nils). It only works for sequences, which we defined as lists without holes.
```
a[1] = 2; a[2] = 1; a[4] = 2;
print(#a)    --> 2
a[3] = 1
print(#a)    --> 4
```
## Precedence
Operator precedence in Lua follows the table below, from the higher to the lower priority:

```^not # - (unary) */%+-..< > <= >= ~= == andor
```
All binary operators are left associative, except for `^` (exponentiation) and `..` (concatenation), which are right associative. 

## Table Constructors

Table in Lua is the equal of dictionary in Python, regarding list as a dictionary with key 1, 2, ... n.

```
days = {'a', 'b', 'c'}
print(days[3])    --> c
```
Lua also offers a special syntax to initialize a table record-like, as in the next example:

```     a = {x=10, y=20}
```This previous line is equivalent to these commands:
```a = {}; a.x=10; a.y=20
```
The original expression, however, is faster, because Lua creates the table already with the right size.

We can mix record-style and list-style initializations in the same constructor:

```
polyline = {color="blue",            thickness=2,            npoints=4,            {x=0,   y=0},    -- polyline[1]            {x=-10, y=0},    -- polyline[2]            {x=-10, y=1},    -- polyline[3]            {x=0,   y=1}    -- polyline[4]
print(polyline[2].x)    --> -10print(polyline[4].y)    --> 1}
```
there is another, more general, format. In this format, we explicitly write the index to be initialized as an expression, between square brackets:

```
a = {['x'] = 0, ['y'] = 0}    -- a = {x = 0, y = 0}
a = {[1] = 'r', [2] = 'g', [3] = 'b'}    -- a = {'r', 'g', 'b'}
```
there is another, more general, format. In this format, we explicitly write the index to be initialized as an expression, between square brackets:

```
{x=10, y=45; "one", "two", "three"}
```

