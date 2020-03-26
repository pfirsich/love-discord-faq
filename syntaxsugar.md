# Syntactic Sugar in Lua
Syntax Sugar, in short, is a way to write something that might be more simpler to understand, or at least in an alternate way to how the language would express it by default. Lua has quite a few instances of this, which will be demonstrated below.

## Tables
Tables are the only complex data type in lua; they can store multiple things inside them, including more tables. Tables store *values* referenced by _keys_; values can be anything, keys can be anything except `nil`.

### Table indexing
The basic way of indexing a table is to use the **`[]` square bracket notation**, however, for specific *string* keys, lua provides syntax sugar in the form of the **`.` dot notation**; these keys are the ones *not starting with a number*, and *are not lua keywords* (e.g. `and`, `or`, `if`, etc.).
```lua
tbl = {}
tbl['anything'] = 'arbitrary string key'
tbl['or']       = 'arbitrary string key that happens to be a lua keyword'
tbl.something   = 'specific string key, as mentioned above'
```

Tables can be nested as well.
```lua
tbl1 = {}
tbl1['tbl2'] = {}
tbl1.tbl2.tbl3 = {}
```

### Table constructor
Tables can have their fields defined inside the **`{}` curly brackets**, with the caveat that one can't reference other fields from within, since they will be created simultaneously. Two forms can be used this way, and can be mixed together as well:
* `key = value` defining specific keys inside the table, in the ways written above, and
* `value` leaving out the key, thereby making lua use whole number keys starting from 1.

Both of these forms need to be separated by `,` commas, and the last one can have a trailing one without it causing errors.
```lua
tbl = {'first', 'second', 'third',}
tbl1 = {
	[1]          = 'first',
	tbl2         = {},   -- the key is equivalent to ['tbl2']
	['tbl3']     = {},
	['anything'] = 'arbitrary string key',
	['or']       = 'arbitrary string key that happens to be a lua keyword',
	'second',    -- [2] is the key here, due to [1] already being defined above.
	something    = 'specific string key, as mentioned above', -- this comma at the end is fine.
}
```

## Functions
### Definition
There are a few ways to define functions in Lua.
* `fun = function(params) --[[code--]] end` treats them the same as any other variable,
* `function fun(params) --[[code--]] end` might be a more familiar way to define them.

Both of these work with the `local` keyword in front of them as well, although the second one is going to be equivalent to
* `local fun; fun = function(params) --[[code--]] end` instead, allowing recursion to be done.

Furthermore, functions can also be stored in tables as well, no matter how deeply nested.
* `tbl1['tbl2']['fun'] = function(params) --[[code--]] end`
* `tbl1.tbl2.fun = function(params) --[[code--]] end`
* `function tbl1.tbl2.fun(params) --[[code--]] end`

### Calling
There is one call mechanism in lua, by using **`()` parentheses** on a variable.
* `fun(params)`
* `result = fun(params)`
* `res1, res2 = fun(params)`

Additionally, for all forms, the calling `()` parentheses are optional if and only if the function gets passed either one table literal, or one string literal:
* `fun{}`
* `fun"text"` or `fun'text'` or `fun[=[text]=]`

*For the sake of completeness, note that these works on functions as well as tables that have a `__call` metamethod defined on them; metamethods themselves aren't covered in detail here.*

## `self`
We can combine tables with functions to achieve an object-oriented coding style. Lua provides its users the **`:` colon notation**, that is to be used instead of the `.` dot notation, it itself syntax sugar for the `[]` square bracket notation, as written above.

This notation works only with the following definition syntax:
* `function tbl:fun(params) --[[code--]] end`

This notation will add an implicit parameter called `self` to the front of the parameter list that can be used inside the function, making it equivalent to the following:
* `function tbl.fun(self, params) --[[code--]] end`
* `tbl.fun = function(self, params) --[[code--]] end`
* `tbl['fun'] = function(self, params) --[[code--]] end`

The call syntax is similar, with self being the _innermost_ table that has the function as a field in it:
* `tbl1.tbl2:fun(params)`

Which is again equivalent to the following:
* `tbl1.tbl2.fun(tbl1.tbl2, params)`
* `tbl1['tbl2']['fun'](tbl1['tbl2'], params)`

*Note that in both cases of function definitions and calls, the language doesn't check where the `:`colon notation was used, and one can (but probably shouldn't) mix and match the two notations between definitions and calls.*
