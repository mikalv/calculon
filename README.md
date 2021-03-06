calculon
========

A terminal-based programmer's calculator
----------------------------------------

I haven't found many decent programmer's calculators for Mac and I spend a fair bit of time copying and pasting between Calculator.app, a Python REPL and a debugger, so I figured I'd have a go at writing a quick terminal-based calculator in Python. The result is Calculon.

Calculon is a programmer's calculator based on an embedded Python REPL. It's split into two components - the display and the REPL - each of which are run in a separate terminal. There are two options for the REPL - either the embedded Python REPL (based on the Python `code` module) or an instance of `bpython`.

Here is Calculon running with the embedded Python REPL in two panes of an iTerm window:

[![calculon example](http://i.imgur.com/XEFqWr1.png)](#example)

And here is Calculon running with the `bpython` REPL with a narrower display:

[![calculon example2](http://i.imgur.com/F5BJYAu.png)](#example2)

Installation
------------

    $ python setup.py install

Configuration
-------------

An example config (`example.cfg`) is included with the source. Copy it to `~/.calculon/config` and edit it if you want to customise the display, otherwise the defaults in the `defaults.cfg` will be used.

Usage
-----

To run the display:

	$ calculon display

To run the embedded REPL:

	$ calculon console

Or, to connect to the display from within a `bpython` instance:

	$ bpython
	>>> import calculon.load

From here, any Python code entered into the REPL that results in a numeric value will be rendered in the display. For example:

![format_example_1](http://i.imgur.com/Njn9RRJ.png)

The result, 0x9a4, will be rendered in the display. When using the embedded REPL (not `bpython`), any numeric results in the REPL will be formatted using the format string defined in the config. By default, this is white coloured hex numbers. The format string can be customised in the configuration, or set in the REPL on the fly like this:

![format_example_2](http://i.imgur.com/y46S1c9.png)

Calculon adds some hackery to the REPL for watching variables. Calling `watch <expr>` will add the given expression to a list of expressions that are tracked and updated every time they change. For example:

    >>> watch a
    >>> watch b
    >>> watch a + b

Now when these variables are updated:

	>>> a = 1234
	>>> b = 1234

Their values will be tracked and the expressions reevaluated. Expressions can be removed from this display with the `unwatch` keyword:

	>>> unwatch 0

Where 0 is the ID displayed at the end (or beginning, when right aligned) of the line.

Calculon can also connect to [Voltron](https://github.com/snare/voltron) using its [REPL client](https://github.com/snare/voltron/wiki/REPL-Client), inspect register state and memory, and execute debugger commands.

Inspecting registers:

    >>> V.rip
    0x100000d20

Memory:

    >>> V[V.rbp]
    'x'
    >>> V[V.rbp:V.rbp + 32]
    'x\xee\xbf_\xff\x7f\x00\x00\xfd\xf5\xad\x85\xff\x7f\x00\x00'

Values from Voltron can now be included in `watch` expressions, and will be updated when the Voltron views are updated (ie. when the debugger is stepped or execution is started and stopped again):

    >>> watch V.rip

Calculon can also execute debugger commands from the REPL:

    >>> print V("reg read")
    General Purpose Registers:
           rax = 0x0000000100000d00  inferior`test_function
           rbx = 0x0000000000000000
           rcx = 0x00007fff5fbff9b0
           rdx = 0x00007fff5fbff8b0
           rdi = 0x0000000100000f7b  "Usage: inferior < sleep | loop | function | crash >\n"
           rsi = 0x00007fff5fbff8a0
           rbp = 0x00007fff5fbff880
           rsp = 0x00007fff5fbff818
            r8 = 0x0000000000000000
            r9 = 0x00007fff7164b0c8  atexit_mutex + 24
           r10 = 0x00000000ffffffff
           r11 = 0x0000000100001008  (void *)0x0000000000000000
           r12 = 0x0000000000000000
           r13 = 0x0000000000000000
           r14 = 0x0000000000000000
           r15 = 0x0000000000000000
           rip = 0x00007fff8c2a7148  libdyld.dylib`dyld_stub_binder
        rflags = 0x0000000000000246
            cs = 0x000000000000002b
            fs = 0x0000000000000000
            gs = 0x0000000000000000

Credits
-------
[richo](https://github.com/richo) deserves many beers for his efforts