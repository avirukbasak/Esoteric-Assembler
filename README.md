# Esoteric-Assembler
An interpreter with assembly like syntax.

## Index
1. [Download links](#download-links)
2. [How to use?](#how-to-use)
3. [The test files](#the-test-files)
4. [Source files](#source-files)
5. [How to code?](#how-to-code)
6. [Contribute](https://github.com/AvirukBasak/Esoteric-Assembler/blob/main/docs/CONTRIBUTE.md)

## Download links
- Get out our latest release [here](https://github.com/avirukbasak/Esoteric-Assembler/releases/tag/v2021.3.10.3). You'll find installation instructions there.
- Download a ZIP file from [here](https://github.com/avirukbasak/Esoteric-Assembler/archive/main.zip).

## How to use?
- Once installed, run `asm path/to/file` where `asm` is the linux command (or the path to the binary executable).
- Windows users need to compile the source manually as of now.
- Enter `asm --help` or `asm -h` for help text.

## The test files
- Folder [Tests](https://github.com/AvirukBasak/Esoteric-Assembler/tree/main/Tests) contains codes for trial.
- Visit [ASM-Tests](https://github.com/AvirukBasak/ASM-Tests) repository for more test codes.

## Source files
- [global.c](https://github.com/AvirukBasak/Esoteric-Assembler/blob/main/src/global.c) Global variable declaration and general functions.
- [main.c](https://github.com/AvirukBasak/Esoteric-Assembler/blob/main/src/main.c) Main and CLI argument evaluation functions.
- [input.c](https://github.com/AvirukBasak/Esoteric-Assembler/blob/main/src/input.c) File handling and input functions.
- [interpreter.c](https://github.com/AvirukBasak/Esoteric-Assembler/blob/main/src/interpreter.c) Interpreter functions.

## How to code?
```
Usage:
  asm [filepath]
  asm [OPTION]
  asm [OPTION] [filepath]

Options:
  -h, --help          | Display this message
  -l, --labels        | Display declared labels in tabular form
  -c, --console       | Console mode to execute codes from stdin
  -v, --version       | Display version information
  -d, --dev           | Developer mode to debug interpreter I/O

Operands:             |
  %x                  | Indicates register 'x'
  &ad                 | Indicates address 'ad' of RAM
  $no                 | Decimal or hex literal 'no'
  ptr                 | RAM data pointer

Escape sequences:     |
  \"                  | Quote escape
  \t                  | Tab escape
  \n                  | Newline feed escape
  \r                  | Carriage return escape
  \b or \<space>      | Space escape
  \<any character>    | Same as writing without '\'

Registers:            |
  operand, x = [a, d] | For storage

RAM:                  |
  (32 x 1048576) bits | For storage (4MB)

Mnemonics:            |
  /*comment*/         | Multi line comment
  Label:              | Labels
  set op1 op2         | Set op1 to value of op2
  add op1 op2         | Add op2 to op1 and store in op1
  sub op1 op2         | Subtract op2 from op1 and store in op1
  mul op1 op2         | Multiply op1 by op2 and store in op1
  div op1 op2         | Divide op1 by op1 and store in op1
  mod op1 op2         | Mod op1 by op2 and store in op1
  ieq op1 op2         | If operands are equal, set FLAG true
  ige op1 op2         | If op1 is greater or equal, set FLAG true
  ile op1 op2         | If op1 is lesser or equal, set FLAG true
  igt op1 op2         | If op1 is greater, set FLAG true
  ilt op1 op2         | If op1 is lesser, set FLAG true
  and op1 op2         | Set op1 to AND value of operands
  or  op1 op2         | Set op1 to OR value of operands
  xor op1 op2         | Set op1 to XOR value of operands
  com op              | Set operand to its 32 bit 1's complement
  jmp lbl             | Jump (unconditional) to a line after lbl
  jit lbl             | Jump if (FLAG) true to line after lbl
  jif lbl             | Jump if (FLAG) false to line after lbl
  call lbl            | Call label as function
  calt lbl            | Call label as function if FLAG true
  calf lbl            | Call label as function if FLAG false
  inp op              | Input decimal number to operand
  prn op              | Print operand as decimal number
  prc op              | Print operand as character
  prs "str"           | Print str as string
  nwl                 | Print new line character
  ret                 | Return from function
  hlp                 | Display help text (only console mode)
  end                 | End execution

NOTE: $20 will be parsed as decimal. For hex, use $0x20. This is
      not necessary for $0a. Same goes for &. Also, 'ptr' can be
      modified as $ptr and used as &ptr.
```

## More notes
1. Interpreter uses a top-to-bottom code interpretation.
2. All codes are read directly from file buffer instead of a string.
3. Jump and function calls are disabled in console mode.
4. Opcode `inv` exists but has been deprecated since `jif` got introduced alongside `jit`.
5. Although both `jumps` and `calls` operate on `labels`, using `ret` after a `jump` will terminate current function.
6. If `ret` is used without a `call`, program will simply quit.
7. Strings are supported as an operand only by `prs`.
6. `hlp` is activated only in `console` mode.
7. Not having `end` causes error `5`.

## Delimiting characters
Tabs, spaces, commas, newlines, carriage returns and 
semicolons are used to delimit parts of the code. In 
reality, these characters are ignored.

## Strings
The following code is valid
```
`1. "set" %a "$5"`
```
This code sets decimal 5 to register `a`.
<br>
<br>
<br>
The following code is invalid
```
1. set "%a $5"
```
Output
```
ERR> [LINE: 1] Invalid operand '%a $5' for opcode 'set'
```
The trouble is that the delimiting space b/w the two 
operands of `set` doesn't get ignored and the whole 
thing hence becomes a single operand.

## List of all errors
```
LBL    CODE    FILE          ERROR

E1     1       main          Too many or too few CLI arguments
E2     2       main          No file path entered
E3     3       main          Invalid option
E4     4       input         Can't read file
E5     5       input         Unexpected EOF
E6     6       input         Invalid new line escape seq
E7     7       input         Invalid carriage return escape seq
E8     8       input, 
               interpreter   Input exceeded limit or too long
E9     9       interpreter   Invalid register name
E10    10      interpreter   Invalid RAM address
E11    11      interpreter   Address out of bounds
E12    12      interpreter   Invalid literal
E13    13      interpreter   Can't assign to literal
E14    14      interpreter   Invalid operand
E15    15      interpreter   Duplicate labels
E16    16      interpreter   No such label
E17    17      interpreter   Can't divide by zero
E18    18      interpreter   Invalid decimal input
E19    19      interpreter   Invalid opcode
E20    20      global        Failed to allocate memory
```

## List of all warnings
``` 
LBL    FILE          WARNING

W1     main          Dev mode
W2     interpreter   Opcode is disabled
W3     interpreter   Opcode 'inv' is deprecated

```
