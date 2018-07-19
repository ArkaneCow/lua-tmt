lua-tmt
-------

Simple lua binding for (libtmt)[https://github.com/deadpixi/libtmt], the tiny mock terminal library.

Create terminal emulators etc. in Lua with this library.



Installation
------------

After cloning the repository, or downloading the .zip, modify the makefile if needed. By default, it compiles using the Lua 5.1 headers.

Then build the project by running `make` in this folder.

The generated tmt.so can be copied to your project path, or(to install systemwide) somewhere in lua's package.cpath.
(list all lua package.cpath locations from bash: `lua -e "print((package.cpath:gsub(';', '\n')))"`)



Usage
-----

The library exports one function(`tmt.new(w,h)`) and a lookup table(`tmt.special_keys[KEY_NAME] = terminal_escape_code`):
A terminal returned by `tmt.new` supports the following functions:


* `events = term:write(str)`

   writes the string `str` to the terminal. The string can contain terminal escape sequences supported by libtmt.

   events is a table containing a list of events. Each events has a type.  
   The following events might be generated:

  * `screen` the screen content has been updated
  * `bell` the terminal should do whatever bell should do
  * `answer` the terminal has recived an answer. The answer is in the `answer`-field in the event's table
  * `cursor` the cursor has moved. The new x,y is stored in the event's table at `x` and `y`.


* `screen = term:get_screen()`

   gets the current screen content as a table.this table contains 3 fields, `width`(screen width), `height`(screen height), and lines.

   lines is a list lines, each a list of cells, so that  
   `lines[y]` contains a line(list of cells), and
   `lines[y]` contains a cell.

   Each line contains a field `dirty`, which is true if the line was changed since it was last drawn.

   Each cell is a table and has the following fields:
   `char` (integer) The character number(convert to string using string.char)
   `fg` (integer) The foreground ANSI terminal color
   `bg` (integer) The background ANSI terminal color
   `bold`, `dim`, `underline`, `blink`, `reverse`, `invisible` (boolean) character attributes


* `x, y = term:get_cursor()`

   Gets the current cursor position.


* `width, height = term:get_size()`

   Gets the terminal size(columns, rows)


* `term:set_size(width, height)`

   Sets the terminal size to width, height(columns, rows)



Example
-------

For a more complete example, see example_dump_screen.lua.


'''
local tmt = require("tmt")
local term = tmt.new(80, 25)
term:write("Hello World!")
local screen = term:get_screen()
for _, cell in ipairs(screen.lines[1]) do
	io.write(string.char(cell.char))
end
io.write("\n")
'''
