title: 'Lua learning notes (1)'
date: 2014-05-08 14:35:01
tags: Lua
description: Note on Chapter 1 of "Programming in Lua, 3rd Edition"
---

# Getting Started

How to run program:

```
% lua hello.lua
```
## Chunks

You can use semicolon if you wish, but it's not recommended.

```
a = 1
b = 2
a = 1; b = 2
a = 1 b = 2    --> ugly code
```
You can use `-i` option to instruct Lua to start an interactive session after the given chunk.

```
% lua -i hello.lua
```
Another way to run chunks is with the `dofile` function.
If you have a file `lib1.lua` with the following code:

```
function twice(x)
    return 2*x
end
```

Then, in interactive mode, you can type

```
> dofile("hello.lua")
> n = twice(10)
```
## Some Lexical Conventions

`i`, `_12`, `i2` and `_INPUT` is OK in Lua. You should avoid identifiers starting with an underscore followed by one or more upper-case letters (e.g., `_VERSION`); they are reserved for special uses in Lua.

single line comment

```
--This is comment
```

comment block

```
--[[
This is a comment block
--]]
```

## Global variable

Global variables do not need declarations. It will be `nil` if not declared.

```
% print(a) --> nil
```
## Stand-Alone Interpreter

The usage of Lua is

```
lua [options] [script [args]]
```
For instance, consider this call:

```
lua -e "sin=math.sin" script a b
```
The interpreter collects the arguments as follows

```
     arg[-3] = "lua"
     arg[-2] = "-e"
     arg[-1] = "sin=math.sin"
     arg[0] = "script"
     arg[1] = "a"
     arg[2] = "b"
```
