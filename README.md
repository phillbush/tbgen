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

## Variables

Variables can be set with the -v option, as in -v VARIABLE=VALUE.
The following variables are supported by tbgen:

* `tbname`:     Name of the testbench file to generate (default: testbench)
* `vcdfile`:    Name of the vcd file (default: testbench.vcd)
* `timescale`:  Time scale in ns (default: 1).
* `duration`:   Duration of test in multiples of timescale (default: 100).
* `step`:       Duration of each step in multiples of timescale (default: 1).
* `module`:     Name of the module (default based on the input).

For example:

	$ ./tbgen -v duration=100 -v step=1 <mymodule.v >testbench.v

## Values

Rather than random values, the value of an input can be defined explicitly
during tbgen invocation by calling ./tbgen with an argument of the form
"INPUTNAME:VALUE".  For example, the following command sets the input
"A" to the constant value of 4b'1010:

	$ ./tbgen "A:4b'1010" <mymodule.v >testbench.v

Instead of random values, the values of an input can increase or
decrease over time.  Just invoke ./tbgen with the argument "INPUTNAME:i"
for increasing values, or with "INPUTNAME:d" for decreasing values.  For
example, the following command makes the value of input "A" increase
from 4'b0000 to 4'b1111 (and back again) over time:

	$ ./tbgen "A:i" <mymodule >testbench.v

Rather than changing value one step at a time you can set the value of
an input to change at a given number of steps; just append the argument
specifier with ":NSTEPS".  For example, the following command increases
the value of A at each step, and increases the value of B at each 16
steps.

	$ ./tbgen A:i:1 B:i:16 <mymodule >testbench.v

## License

This software is in public domain and is provided AS IS, with NO WARRANTY.
