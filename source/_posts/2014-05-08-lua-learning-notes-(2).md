title: 'Lua learning notes (2)'
date: 2014-05-08 15:37:55
tags: Lua
description: Note on Chapter 2 of "Programming in Lua, 3rd Edition"

--- 

# Types and Variables

## Nil
uses `nil` as a kind of non-value, to represent the absence of a useful value. A global variable has a nil value by default, before its first assignment, and you can assign nil to a global variable to delete it.

## Booleans
The boolean type has two types, `true` and `false`

Conditional tests (e.g., conditions in control structure) consider both the boolean `false` and `nil` as false and anything else as true. **In particular, Lua considers both zero and the empty string as true in conditional tests.**

## Numbers
The number type represents real (double-precision floating-point) numbers. Lua has no integer type.

Any number up to $2^53$ (approximately 10^16) has an exact representation as a double-precision floating number. **In pariticular, a Lua number can represent any 32-bit integer without rounding problems.**

Examples of valid numberic constants are:

```
    4    0.4    4.57e-3    0.3e12    5E+20
```
Examples of hexadecimal constants, prefixing them with `0x`, which can have a fractional part and a binary exponent (prefixed by `p` or `P`).

```
    0xff (255)    0x1A3 (419)    0x0.2 (0.125)   0x1p-1 (0.5)
    0xa.bp2 (42.75)
```

## Strings

Strings in Lua are *immutable* values as the same as Python.

You can get the length of a string using the prefix operator `#`:

```
a = 'hello'
print(#a)    --> 5
print(#"good\0byte")    --> 8
```
Strings in Lua can contain the following C-like escape sequences:

```
￼\a bell
\b back space
\f form feed
\n newline
\r carriage return \t horizontal tab \v vertical tab
\\ backslash
\" double quote \' single quote
```
Lua uses `[[` and `]]` to delimit long strings. Moreover, this form ignores the first character of the string when this character is a newline.

```
page = [[
<html>
<head>
<title>An HTML Page</title>
</head>
<body>
<a href="http://www.lua.org">Lua</a>
</body>
</html>
]]
write(page)
```
Lua provides automatic conversions between numbers and strings at run time.

```
print("10" + 1)    --> 11
print("10 + 1")    --> 10 + 1
print("-5.3e-10"*"2")    --> -1.06e-09
print("hello" + 1)    --> ERROR (cannot convert "hello")
```
As a rule, it is better not to count on coercions. If you need to convert a string to a number, you can use the function `tonumber`; Otherwise, `tostring`.

## Table

The table type in Lua is similar to dictionary type in Python. 

```
a = {}           -- create a table and store its reference in 'a'
k = "x"
a[k] = 10        -- new entry, with key="x" and value=10
a[20] = "great"  -- new entry, with key=20 and value="great"
print(a["x"])    --> 10
k = 20
print(a[k])      --> "great"
a["x"] = a["x"] + 1     -- increments entry "x"
print(a["x"])    --> 11
```
Like global variables, table fields evaluate to nil when not initialized. Also like global variables, you can assign nil to a table field to delete it.

```
a = {}
a["x"] = 10
b = a    -- 'b' refers to the same table as 'a'
print(b["x"])    --> 10
b["x"] = 20
print(a["x"])    --> 20
a = nil    -- only 'b' still refers to the table
b = nil    -- no references left to the table
```
When a program has no more references to a table, the table will be deleted.

Lua provides `a.name` as syntactic sugar for `a[name]` as follows:

```
a.x = 10    -- same as a["x"] = 10
print(a.x)    -- same as print(a["x"])
print(a.y)    -- same as print(a["y"])
```

A common mistake for beginners is to confuse `a.x` with `a[x]`. The first form represents `a["x"]`, that is, a table indexed by the string `“x”`. The second form is a table indexed by the value of the variable `x`. See the difference:

```
a = {}
x = "y"
a[x] = 10    -- put 10 in field "y"
print(a[x])    --> 10    -- value of field "y"
print(a.x)    --> nil     -- value of field "x" (undefined)
print(a.y)    --> 10    -- value of field "y"
```
## Userdata and Threads

Explained later.

