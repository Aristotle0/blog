title: 'Lua Learning Notes (4)'
date: 2014-05-09 20:33:03
tags: Lua
description: Note on Chapter 1 of "Programming in Lua, 3rd Edition"
---

# Statements

## Assignment

Lua always adjusts the number of values to the number of
variables: when the list of values is shorter than the 
list of variables, the extra variables receive nil as 
their values; when the list of values is longer, the 
extra values are silently discarded:
```
a, b, c = 0, 1
print(a, b, c)          --> 0 1 nil
a, b = a+1, b+1, b+2    -- value of b+2 is ignored 
print(a, b)             --> 1 2
a, b, c = 0
print(a, b, c)          --> 0 nil nil
```
The last assignment in the above example shows a common 
mistake. To initialize a set of variables, you must 
provide a value for each one:
```
a, b, c = 0, 0, 0
print(a, b, c)         --> 0   0   0
```

## Local Variables and Blocks

Besides global variables, Lua supports local variables.
We create local variables with the local statement:
```
j = 10         -- global variable
local i = 1    -- local variable
```
Unlike global variables, local variables have their 
scope limited to the block where they are declared. 
A block is the body of a control structure, the body 
of a function, or a chunk (the file or string where 
the variable is declared):
```
x = 10
local i = 1             -- local to the chunk
while i <= x do 
    local x = i*2       -- local to the while body
    print(x)            --> 2, 4, 6, 8, ...
    i=i+1
end
```

These `do` blocks are useful also when you need finer 
control over the scope of some local variables:
```
do
  local a2 = 2*a
  local d = (b^2 - 4*a*c)^(1/2)
  x1 = (-b + d)/a2
  x2 = (-b - d)/a2
end          -- scope of 'a2' and 'd' ends here
print(x1, x2)
```
It is good programming style to use local variables 
whenever possible.

A common idiom in Lua is
```
local foo = foo
```
This code creates a local variable, `foo`, and 
initializes it with the value of the global variable 
`foo`. (The local `foo` becomes visible only after its 
declaration.) This idiom is useful when the chunk 
needs to preserve the original value of `foo` even 
if later some other function changes the value of 
the global `foo`; it also speeds up the access to `foo`.

## Control Structure

** if then else **
```
if a < b then
    return a
elseif a > b then
    return b
else
    print('a equal b')
end
```
** while **
```
local i = 1
while a[i] do
    print(a[i])
    i=i+1 
end
```
** repeat until **
```
-- print the first non-empty input line
repeat
line = io.read()
until line ~= ""
print(line)
```
Unlike in most other languages, in Lua the scope 
of a local variable declared inside the loop 
includes the condition:
```
local sqr = x/2
repeat
    sqr = (sqr + x/sqr)/2
    local error = math.abs(sqr^2 - x)
until error < x/10000   -- local 'error' still visible here
```
** numeric for **
A numeric for has the following syntax:
```
for var = exp1, exp2, exp3 do
    <something> 
end
```
The for loop has some subtleties that you should learn in order
to make good use of it. First, all three expressions are 
evaluated once, before the loop starts. For instance, in 
our first example, Lua calls f(x) only once. Second, the control
variable is a local variable automatically declared by the for 
statement, and it is visible only inside the loop. A typical 
mistake is to assume that the variable still exists after 
the loop ends:
```
for i = 1, 10 do print(i) end
max = i      -- probably wrong! 'i' here is global
```


