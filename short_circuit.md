# Short Circuiting of Boolean Operators
In Lua the `and` and `or` operator do not return boolean values exclusively. They return one of their operands depending on whether one of them evaluates to `true`:
* `x and y` evaluates to `y` if `x` evaluates to `true` and to `x` if it evaluates to `false`
* `x or y` evaluates to `x` if `x` evaluates to `true` and to `y` if it evaluates to `false`

*Reminder: In Lua only `false` and `nil` evaluate to `false`.*

# Common Idioms
## Ternary Conditional Operator
Some languages provide a way to write [conditional expressions](https://en.wikipedia.org/wiki/%3F:) and while Lua does not do so explicitely, they can be emulated (in most cases) with `and` and `or`:
```lua
local signedValue = positive and value or -value
```
`signedValue` will have the value of `value` if `positive` is `true` and the value of `-value` otherwise.

Care should be taken if false-ey values are used for the expressions to be returned, for example:
```lua
local notFlag = flag and false or true
```
will return `true` in any case, since when `flag` is `true`, `flag and false` will be `false` and in turn `false or true` will be `true`.

## Default Arguments
Optional argument values are often assigned like this:
```lua
local function drawRect(x, y, color)
  x = x or 0
  y = y or 0
  color = color or {1, 1, 1, 1} -- white
  -- the actual code
end 
```
So if an argument is `nil`, because it was not passed to the function, the second operand of the `or` expression will be used.
