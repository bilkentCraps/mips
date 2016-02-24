# Mips

http://bilkentcraps.github.io/mips

Little mips assembler for verilog.

Updates as you type in realtime.

Works with base 10 and lab5a(sec2) instructions.

Tested with Mr. Sawyer's test instruction (instructions in imem.v file)

---

### Features:

- creates verilog code ready to copy to Xilinx IDE for labs
- customizable instruction definitions
- works with negative numbers as two's complements
- text editor with line numbers which is helpful for jumps and branches
- reference jump and brach targets with line numbers just like labels
- creates direct (adr) and relative (radr) addresses
- sign/zero extension on the fly
- allow comments
- syntax checker

Use jumps and branches with decimal line numbers just like labels in mars.

(adr = j type, 26 bit and reference address directly, radr = 16 bit and reference address relatively)

Use it in any modern browser: http://bilkentcraps.github.io/mips

It is simple little extensible utility running on web browsers, completely on client side. (nothing in server, everything happens in user's web browser.)
For such static (from the server point of view) applications Github allows free hosting without any limit or expiration.
So it is alive for everyone forever at http://bilkentcraps.github.io/mips

It consists of just 3 textareas, one for specifying instruction formats, one for user's assembly code and one for the result as a verilog code which is ready to copy to Xilinx IDE.
As you type instruction formats or assembly codes, hex based verilog result updates in real time.

It is possibly to add new instructions just by adding new lines to first textarea. I already add base 10 and lab5a(sec2) instructions.

---

### Formats:

- For instructions with `rs rt rd` like `and $a1, $v1, $a0`:

  - When the following line added into format textarea (first textarea):

    ```
    and  rd rs rt   = 000000 rs rt rd 00000 100100
    ```

  - And when the following instruction added into assembly textarea (second textarea):

    ```
    and $a1, $v1, $a0
    ```

  - Program matches `and  rd rs rt` (left side of the equal sign):

    ```
    and with and
    $a1 with rd
    $v1 with rs
    $a0 with rt
    ```

  - And according the specified format `000000 rs rt rd 00000 100100` (right side of the equal sign) it creates:

    ```
    000000 $v1 $a0 $a1 00000 100100
    ```

  - Of course `$v1, $a0, $a1 (rs, rt, rd)` is expressed as their 5 bit representations.

  - Finally after converting into hex, result is displayed in the verilog textarea (third textarea) as:

    ```
    8'h--: instr = 32'h00642824;
    ```

  - `--` is replaced automatically with correct line number.

- For instructions with imm like `addi $a3, $v1, -9` or `sw $a3, 68($v1)`

  - It works similarly with following formats:

    ```
    addi rt rs imm  = 001000 rs rt imm
    sw   rt imm rs  = 101011 rs rt imm
    ```

  - imm are replaced as two's complements and sign-extended into 16 bit values.

- For instruction `lui $v0, 1` and `nop`

  - Following formats are used:

    ```
    lui  rt imm     = 001111 00000 rt imm
    nop             = 010110 00000000000000000000000000
    ```

- For instructions with adr `j 26`

  - Following format is used:

    ```
    j    adr        = 000010 adr
    ```

  - In instruction `j 26`, 26 represents the decimal line number in the assembly program just like labels in Mars.

  - Line numbers starts with zero. For this instruction, `0800001a` is created.

- For instructions with relative addresses like `beq $a1, $a3, 17`

  - Following format is used:

    ```
    beq  rs rt radr = 000100 rs rt radr
    ```

  - In instruction `beq $a1, $a3, 17`, just like in j instruction, 17 is the line number in the assembly program just like label.

  - But this time since beq uses relative addressing hex representation is created with the difference between beq's line and line 17.

- So in short this utility knows about:

  - rt, rs, rd and their 5 bit representations
  - imm and represents it as two's complement as 16 bit value
  - adr and creates 26 bit value from label which is represented as a decimal line numbers
  - a special imm called radr and creates 16 bit value as relative reference from numeric label

---

- I wrote this under 3 hours so maybe code can be seen as dirty.

  - Everything is just one file.
  - To use it, just go to: http://bilkentcraps.github.io/mips
  - Or if you don't like it on Github, it is possible to copy paste source code into simple texteditor. (for windows, notepad)
  - And saving it as "index.html" (quotation marks are required otherwise it will be saved as index.html.txt we need it to be saved as index.html)
  - Then double click to index.html to open it in your favourite web browser. (safari, chrome, etc.)

- All source code is around 200 lines in one file and can be seen at: https://github.com/bilkentCraps/mips/blob/gh-pages/index.html

- Also thanks to Cheol Woo and Ferit, they reminded me how much time that I spend just for writing hex instructions in the lab and as Cheol Woo suggested, I started coding from scratch without knowing he already have some code for this purpose so unfortunately I didn't use his code but all credit belongs to Cheol Woo for idea and motivating me :)

---

### Example:

##### From this:

```
addi   v0, zero, 5     #begin mr.sawyer
addi   v1, zero, 12
addi   a3, v1, -9
or     a0, a3, v0
and    a1, v1, a0
add    a1, a1, a0
beq    a1, a3, 17      #goto line17
slt    a0, v1, a0
beq    a0, zero, 10    #goto line10
addi   a1, zero, 0
slt    a0, a3, v0
add    a3, a0, a1
sub    a3, a3, v0
sw     $a3, 68($v1)    #use $ or not
lw     v0, 80(zero)
j      17              #goto line17
addi   v0, zero, 1
sw     v0, 84(zero)    #end mr.sawyer
nop                    #nothing
lui    v0, 1           #load 65536
sw     v0, 84(zero)    #65536 -> 84
addi   v0, zero, 100
sw     v0, 0(zero)     #store 100
jalm   v1, 0(zero)     #goto line 100/4
sw     zero, 0(zero)
sw     v1, 0(zero)
j      26              #goto line26
```

##### It gives this:

```
8'h00: instr = 32'h20020005;
8'h04: instr = 32'h2003000c;
8'h08: instr = 32'h2067fff7;
8'h0c: instr = 32'h00e22025;
8'h10: instr = 32'h00642824;
8'h14: instr = 32'h00a42820;
8'h18: instr = 32'h10a7000a;
8'h1c: instr = 32'h0064202a;
8'h20: instr = 32'h10800001;
8'h24: instr = 32'h20050000;
8'h28: instr = 32'h00e2202a;
8'h2c: instr = 32'h00853820;
8'h30: instr = 32'h00e23822;
8'h34: instr = 32'hac670044;
8'h38: instr = 32'h8c020050;
8'h3c: instr = 32'h08000011;
8'h40: instr = 32'h20020001;
8'h44: instr = 32'hac020054;
8'h48: instr = 32'h58000000;
8'h4c: instr = 32'h3c020001;
8'h50: instr = 32'hac020054;
8'h54: instr = 32'h20020064;
8'h58: instr = 32'hac020000;
8'h5c: instr = 32'h54030000;
8'h60: instr = 32'hac000000;
8'h64: instr = 32'hac030000;
8'h68: instr = 32'h0800001a;
```

##### With this:

```
nop             = 010110 00000000000000000000000000
lui  rt imm     = 001111 00000 rt imm
jalm rt imm rs  = 010101 rs rt imm
j    adr        = 000010 adr
addi rt rs imm  = 001000 rs rt imm
beq  rs rt radr = 000100 rs rt radr
sw   rt imm rs  = 101011 rs rt imm
lw   rt imm rs  = 100011 rs rt imm
or   rd rs rt   = 000000 rs rt rd 00000 100101
and  rd rs rt   = 000000 rs rt rd 00000 100100
add  rd rs rt   = 000000 rs rt rd 00000 100000
slt  rd rs rt   = 000000 rs rt rd 00000 101010
sub  rd rs rt   = 000000 rs rt rd 00000 100010
```
