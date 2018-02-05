# Integral VM

Integral VM is a bytecode interpreter that performs arithmetic operations on integers of arbitrary precision.
It is a stack based virtual machine.

## Bytecode specification

The VM processes a self describing sequence of bytes that specify integer literals, arithmetic operations,
and commands that manipulate the stack.

### Integer literals

Bytecodes [0x00, 0x40) represent the two's complement number of the same bit pattern (0 through 63).
Similarly, 0xFF stands for -1.

The bytecode 0x40 is followed by an integer literal. That integer, which must be non-negative,
indicates how many of the following bytes should be interpreted as the two's complement
representation of an integer literal. For example, the bytecode 0x40 could be followed by 0x06,
which would indicate that the succeeding six bytes should be interpreted as a two's complement
representation of an integer.

The bytecode 0x41 is followed by an integer literal. That integer, which must be non-negative,
indicates how many of the following bytes should be interpreted as ascii characters representing
an integer in base ten. The bytecode 0x42 is similar, but is followed by two integer literals.
The first integer, which must be [2, 36], specifies the base of the ascii character representation
of the integer. The second integer specifies how many of the following bytes should be interpreted
as the ascii character encoding of the integer literal. For example, the bytecode 0x42 could be
followed by 0x10, then 0x05. This indicates that the following five bytes represent a number
in hexadecimal representation.

An integer literal encountered by the VM is pushed onto the top of the stack.

### Arithmetic operations

Unary operations consume the top value on the stack, and push the result.

* Absolute value 0x50
* Negate 0x51
* Not 0x52
* Sign 0x53, gives [-1, 0, 1]

Binary operations consume the top value on the stack, we'll call it x, and the next value on the stack,
we'll call it y. These operations then push their result.

* Add 0x54, x + y
* And 0x55, x & y
* Subtract 0x56, x - y
* Multiply 0x57, x * y
* Truncating divide 0x58, x / y
* Remainder 0x59, x % y
* Mod 0x5A, x mod y
* GCD 0x5B, gcd(x, y)
* Compare 0x5C, sign(x - y)
* Max 0x5D
* Min 0x5E
* Mod Inverse 0x5F, x^-1 mod y
* Or 0x60, x | y
* Shift left 0x61
* Shift Right 0x62
* Xor 0x63

Ternary operations consume the top three values on the stack, x y and z. These operations push their result.

* Mod Power 0x64, x^y mod z

### Stack manipulation

These operations manipulate the state of the stack.

* Duplicate 0x70, push a copy of the top value onto the stack
* Swap Two 0x71, swap the top two values on the stack

Operations need thought, probably after implementing parts of the bytecode encoding programs.

### IO operations

Operations for taking input and producing output (printing, encoding). Needs thought.


## Software components needed

* VM: stack simulation and operation processing
* Encoder/decoder to go back and forth between bytecode and a text representation of the codes
* Compiler to take text written by humans, and create appropriate bytecodes to compute the result
* Interactive session to evaluate typed input
