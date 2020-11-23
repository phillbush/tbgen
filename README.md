# tbgen

tbgen is a public domain testbench generator in AWK for Verilog modules

After generating the testbench with tbgen, compile it with iverilog(1)
to generate the vvp file; then run the compiled vvp file with vvp(1) to
generate the vcd file; then run gtkwave with the generated vcd to get
the waveform.

	$ ./tbgen <mymodule.v >testbench.v
	$ iverilog -o testbench.vvp testbench.v mymodule.v
	$ vvp testbench.vvp </dev/null
	$ gtkwave testbench.vcd

The generated testbench works by, at each simulation step, setting each
input to a random value, until the duration of the test is over.

WARNING!
tbgen is not a Verilog parser, it expects the module file to be well written.

## Usage

**tbgen** should be called as follows:
The first group of arguments are `VARIABLE=VALUE` pairs preceded by `-v`.
The second group of arguments are input specifications.
The third kind argument is the filename to be read.
All group of arguments are optional.
If the filename is omitted, a module is read from standard input.

	tbgen [-v VARIABLE=VALUE]... [INPUTSPEC]... [MEMDUMP]... [FILE]


## Variables

Variables can be set with the -v option, as in -v VARIABLE=VALUE.
The following variables are supported by tbgen:

* `tbname`:     Name of the testbench file to generate (default: testbench)
* `vcdfile`:    Name of the vcd file (default: testbench.vcd)
* `dumplevel`:  If set to 0, dump all variables in all lower level modules,
  if set to 1, dump all variables in the top-level module (the testbench),
  if set to 2, dump all variables in the top-level module and the modules instantiated by it (the dut),
  etc (default: 1).
* `timeunit`:   Base unit of time (default: "ns").
* `timescale`:  Time scale in the base unit of time (default: 1).
* `duration`:   Duration of test in multiples of timescale (default: 100).
* `step`:       Duration of each step in multiples of timescale (default: 1).
* `module`:     Name of the module (default based on the input).
* `clock`:      Name of the clock input (default to "clk").
* `reset`:      Name of the reset input (default to "rst").
* `incdir`:     Directory used to search for included files (tbgen only support one incdir)

For example:

	$ ./tbgen -v duration=100 -v step=2 mymodule.v >testbench.v


## Input specification

Rather than random values, the value of an input can be defined explicitly
during tbgen invocation by calling ./tbgen with an argument of the form
`INPUTNAME:VALUE`.  For example, the following command sets the input
`data` to the constant value of `4b'1010`:

	$ ./tbgen "data:4b'1010" mymodule.v >testbench.v

Also, the values of an input can increase or decrease over time.
Just invoke ./tbgen with the argument `INPUTNAME:i` for increasing values,
or with "INPUTNAME:d" for decreasing values.
For example, the following command makes the value of input `data` increase
from `4'b0000` to `4'b1111` (and back again) over time:

	$ ./tbgen "data:i" mymodule.v >testbench.v

Rather than changing value one step at a time you can set the value of
an input to change at a given number of steps; just append the argument
specifier with ":NSTEPS".  For example, the following command increases
the value of `dataA` at each step, and increases the value of `dataB` at
each 16 steps.

	$ ./tbgen dataA:i:1 dataB:i:16 mymodule.v >testbench.v

The clock and the reset inputs are special inputs.
The input for the clock is alternated from 1 to 0 and back again at each step.
The input for the reset begins 1 and is set to 0 after the first step.
Both clock and reset inputs do not need to be declared as input specification.


## Memory dump

The testbench can dump the content of a memory of a given module at the end of the simulation.
To specify a memory to be dump, call ./tbgen with an argument of the form `dump:MEMORY:FILE`.
For example, the following command dumps the content of the memory `data` in the module `ram`
at the end of the simulation.

	$ ./tbgen dump:ram.data:memdump.txt mymodule.v >textbench.v

Note that the module `ram` is instanciated in the module under test `mymodule.v`.
If the memory is declared directly in `mymodule.v`, use the following call instead:

	$ ./tbgen dump:data:memdump.txt mymodule.v >textbench.v


## License

This software is in public domain and is provided AS IS, with NO WARRANTY.
