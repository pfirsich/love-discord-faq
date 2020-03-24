# Methodical Syntax Sugar
Lua has [first-class functions](https://en.wikipedia.org/wiki/First-class_function), them being treated just like any other¹ variable. While the language itself is not [object-oriented](https://en.wikipedia.org/wiki/Object-oriented_programming), it has ways to make it simpler to support such usage, simulating "methods" that would be tied to, and work on, objects.

*¹: The only exception is the ... vararg, which is the only second-class type.*

## Function Definition
There are a few ways to define functions in Lua.
* `foo = function(params) --[[code--]] end` is the most basic way, due to functions being first class in the language.
* `function foo(params) --[[code--]] end` is the one many might be most familiar with, but it itself is syntax sugar for the above line.

The `local` keyword works for the above two lines as well, if one doesn't want them to be globals, that is, included in the current environment's `_G` table implicitly.

The following two examples demonstrates that both forms can be used with explicit tables as well, no matter how deeply nested.
* `baz.bar.foo = function(params) --[[code--]] end`
* `function baz.bar.foo(params) --[[code--]] end`

*Do note that the `.` dot notation is _also_ syntax sugar for `[]` square bracket notation for tables, that isn't covered here.*

For simulating OOP methods, lua provides the `:` colon notation instead of the `.` dot notation, which contains some implicit mechanisms; this only works with the second syntax:
* *`bar:foo = function(params) --[[code--]] end` is __erroneous__.*
* `function bar:foo(params) --[[code--]] end`

The above implicitly adds a parameter with the name `self` as the first parameter into the definition, that can be used. This is equivalent to the following:
* `function bar.foo(self, params) --[[code--]] end`
* `bar.foo = function(self, params) --[[code--]] end`

## Function Call
There is one call mechanism in lua, by using `()` parentheses on a variable; this works on functions as well as tables that have a `__call` metamethod defined on them; metamethods themselves aren't covered in detail here.

* `foo(params)`
* `baz.bar.foo(params)`

The `:` colon notation is available with function calls as well, in which case, it passes in the _innermost_ table to the self parameter of the function. The following two calls are equivalent:
* `baz.bar.foo(baz.bar, params)`
* `baz.bar:foo(params)`

## Complete testable example showcase
*Note that in both cases of function definitions and calls, the language doesn't keep tabs on where the `:`colon notation was used, and one can (but probably shouldn't) mix and match the two notations between definitions and calls.*
```lua
local T = {}
-- also includes [] table notation for completeness' sake.

function T.foo(a,b) return self,a,b end
-- equivalent to: T.foo = function(a,b) return self,a,b end
-- and also to:   T['foo'] = function(a,b) return self,a,b end
-- unlike what the highlight shows, self here is just a regular variable,
-- either a global or an upvalue, which can be nil.

function T:bar(a,b) return self,a,b end
-- equivalent to: function T.bar(self,a,b) return self,a,b end
-- and also to:   T.bar = function(self, a, b) return self,a,b end
-- and also to:   T['bar'] = function(self, a, b) return self,a,b end

-- The first and last are the ones having matching notation.
print(T.foo(2,3)) ---> self global/upvalue, 2,           3
print(T:foo(3,4)) ---> self global/upvalue, tostring(T), 3
print(T.bar(1,2)) ---> 1,                   2,           nil
print(T:bar(2,3)) ---> tostring(T),         2,           3
```
