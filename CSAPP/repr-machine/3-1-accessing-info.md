# History 
- Intel's x86 processors 
- RISC vs CISC

# Intro to asm codes

Architectures(also ISA): The parts of a processor design that one needs to understand or write asm/machine code. 

Microarch: Implementation of an arch 

Code forms
- machine code the byte-lvl progs that a pc executes 
- asm codes: a text repr of machine code 

## Machine view 


![Img](https://s2.loli.net/2023/05/06/NMpolivdrQkhqJO.png)

Programmer visible state 

- PC: program counter 
    - address of next instruction 
    - called RIP on x86-64
- Register file: 
    - Heavily used program data 
- Condition codes 
    - Store status info about most recent arith or logical op
    - used for conditional branching 
- memory 
    - byte addressable array 
    - code and user data 
    - stack to support procedures 

## Turing C into obj code 
- code in two files 
- compile ...

![Img](https://s2.loli.net/2023/05/09/ojMt1Ifkb3NnA6V.png)

Example: 

![Img](https://s2.loli.net/2023/05/09/bvq34QWfyBJi2SZ.png)

- pushq = push something onto 
- move = move sth frm one place to another 
- call = call some procedure 
- pop = unpush 
- ret = return to the outer part of the func 
- commands start with . = send msgs for debuggers  

# Prop in asm 

## Data types 

- int data as 1, 2, 4, 8 bytes 
    - data vals 
    - addrs (untyped pointers) 
- floating point datas are 4, 8, 10 bytes 
    - they will be processed on a diff set of regs 
- code: byte seqs encoding series of instrs 
- No aggregate encoding series of instrs 
    - just continuously allocated bytes in mem 

## Operation 
- perform arith fun on register or mem data 
- transfer data between mem and reg 
    - load data from mem to reg 
    - store data into mem 
- tranfer ctrl 
    - unconditional jump to/frm procedures 
    - conditional branches 

Example : 

C code: `*dest = t`: store t where designated by dest 

Asm code: `movq %rax, (%rbx)`, 
- move 8 bytes from rax reg to memory referenced by value in rbx 

Obj code: `0x40059e: 48 89 03` (3-byte code)
- 3 byte instr 
- stored at addr 0x40059e

## Disasmer : objdump

Use command `objdump -d ...` to do it. 

# Registers 

![Img](https://s2.loli.net/2023/05/09/VPGEMs8dC9AR5kU.png)

Obs: 
- use `r` for 64 bits, use `e` for 32 bits. 

Name's History: 
- Origianlly, IA32 registers has these 8 regs. 
- GPR's name
    - a = accumulate 
    - c = counter 
    - d = data 
    - b = base 
    - si = source index 
    - di = destination index 
- important and crucial 
    - esp = stack pointer 
    - ebp = base pointer  

![Img](https://s2.loli.net/2023/05/09/qXU5AtjFnzBC8bH.png)

# Moving datas

- Moving data: `movq SRC, DST`
- operand types 
    - Immediate: const int data 
        - e.g. `$0x400, $-533`
        - like c const, but with dollar
        - encoded with 1, 2, 4 bytes
    - register: one of 16 integer regs 
        -  e.g. %rsp , %r13
        - but %rsp is reserved for special purpose 
        - others have special uses for paritcular instrs 
    - memory: 8 consecutive bytes from mem at addr given by reg 
        - simplest val (%rax)
        - various other addr modes 

![Img](https://s2.loli.net/2023/05/09/w58yqsFX276JQOn.png)

simple addressing modes 
- normal (R) Mem[Reg[R]]
    - register R specifies mem addr 
    - That's pointer dereference 
    - like `movq (%rcx) %rax`
- displacement D(R) Mem[Reg[R]+D]
    - Register R specifies start of mem region 
    - const displacement D specifies offset 
    - like `movq 8(%rbp) %rax`

Example. SWAP 

![Img](https://s2.loli.net/2023/05/09/Jx6Tow5DdRIaPXh.png)
- rdi will be the 1st register 
- rsi will be the 2nd register 

Complete Mem allocating modes: 
- Most general D(Rb, Ri, S) Mem[Reg[Rb]+S*Reg[Ri]+D]
    - D: const displacement 1,2,4 bytes 
    - Rb: base reg: any of 16 bit ints 
    - Ri: index register 
    - S: scale 1,2,4,8
- Special cases
    - (Rb, Ri) Mem[Reg[Rb]+Reg[Ri]]
    - D(Rb, Ri) Mem[Reg[Rb]+Reg[Ri]+D]
    - (Rb, Ri, S) Mem[Reg[Rb]+S*Reg[Ri]]

Examples: 

![Img](https://s2.loli.net/2023/05/09/adQRHlM2AysWX5i.png)

# Addr and logical instrs 

## Addr computation 
leaq src dst 
- src is address mode expression 
- set dst to address denoted by expr 

Uses  
- Computing addr without mem ref 
    - Eg. `p = &x[i]`.
- computing ari expr of the form `x+ky`
    - k=1,2,4,8

Example.

![Img](https://s2.loli.net/2023/05/09/dv95ZQw7PWFkCJO.png)

## Arith command

![Img](https://s2.loli.net/2023/05/09/XKQzpfCmV3P2EAr.png)

![Img](https://s2.loli.net/2023/05/09/hZuL8S1mEwUygXA.png)

## Example 

![Img](https://s2.loli.net/2023/05/09/XOA46qYZvjDUVyB.png)

Summary: 

![Img](https://s2.loli.net/2023/05/09/RkhNaybVg1Codi5.png)


$$
\text{...original notes starts below...}
$$


# Accessing Information

## Types of C data types in x86-64

```
| C declaration | Intel data type | Assembly code | Size(bytes)
| --      | --   | -- | --|
| `char`  | Byte             | b | 1|
| `short` | Word             | w | 2|
| `int`   | Double Word      | l | 4|
| `long`  | Quad word        | q | 8|
| `char *`| Quad word        | q | 8|
| `float` | Single precision | s | 4|
| `double`| Double precision | l | 8|
```

## Registers

An x86-64 CPU contains a set of 16 *general purpose registers*(GPRs). 

```
63      31      15    7     0
%rax    %eax    %ax   %al      Return val
%rbx    %ebx    %bx   %bl      Callee saved
%rcx    %ecx    %cx   %cl      4th arg
%rdx    %edx    %dx   %dl      3rd arg
%rsi    %esi    %si   %sil     2nd arg
%rdi    %edi    %di   %dil     1st arg
%rbp    %ebp    %bp   %bpl     Callee saved
%rsp    %esp    %sp   %spl     Stack Pointer
%r8     %r8d    %r8w  %r8b     5th arg
%r9     %r8d    %r9w  %r9b     6th arg
%r10    %r8d    %r10w %r10b    Caller saved
%r11    %r8d    %r11w %r11b    Caller saved
%r12    %r8d    %r12w %r12b    Caller saved
%r13    %r8d    %r13w %r13b    Caller saved
%r14    %r14d   %r14w %r14b    Caller saved
%r15    %r15d   %r15w %r15b    Caller saved
```

The low-ordered portions of all 16-bit regs can be accessed as byte(8 bit), word(16 bit), double word(32 bit), and quadword(64 bit).

## Operand Specs

1. Immediate: 
    1. the real number(Literal) and does not change with variables. 
    2. Noted as `$Imm`. 
    3. We may note in forms of $\textit{Imm}$
2. Register
    1. denotes the contents of the register
    2. Noted as $r_\alpha$ .
    3. Operand value is $R[r_\alpha]$ .
3. Memory (Reference)
    1. access some momory locatoin according to a computed address. (Effective address)
    2. Notation: $M_b [Addr]$ means reference to a b-byte value storing at the address Addr. 
    3. General form of $Imm(r_b,r_i,s)$ means the we refer to: Immediate offset(Imm), a base register ($r_b$) and an index register($r_i$), a scale factor s(s=1,2,4,8). Then it is computed as $Imm+R[r_b]+R[r_i]\cdot s$. 

# Data movement Instructions

The MOV instruction. 

1. Command: MOV(SRC, DST) (movement)
    1. SRC is one of IMM, REG or MEM 
    2. DST is one of REG or MEM.
    3. But both cant be together MEMs. 
2. Schematics: Copy the value from source to destination.(of the same size)
3. Process: load to register and then write it into the destination. 
4. Possible suffixes: 
    1. b = byte
    2. w = word
    3. l = long = quad word
    4. q = quad word
    5. absq = absolute quad word

The MOVZ instruction.

1. Schematics: Move zero-extended number into the destination register. (filled without sign extensions, all 0s. )
2. Suffix: 
    1. bw = byte to word 
    2. bl = byte to double word(long)
    3. wl = word to double word(long)
    4. bq = byte to quad word
    5. wq = word to quad word 
6. Meaning: replace the whole instead of substituting some of them. 

The MOVS instruction.

1. Schematics: Same as MOVZ, but filled by signs. 

## Pushing and poping stack datas

The stack memory model. 

Instruction PUSHQ(S): 

1. Effect: `R[%rsp]=R[%rsp]-8; M[R[%rsp]]=S.`
2. Description: push quad word. 

Instruction POPQ(D): 

1. Effect: `D=M[R[%rsp]]; R[%rsp]=R[%rsp]+8.`
2. Description: pop quad word. 

## Arithematics and logic operations

![Img](https://s2.loli.net/2023/03/28/wUYyhsm8oB2WMdC.png)



Instruction LEAQ(src, dest)

1. Schematics: a varient of movq. reads from memory to register without referencing memory at all. 
2. Syntax: The dest must be an register.


Note: multiplying two 64-bit signed or unsigned integers can yield a product that requires 128 bits to represent

