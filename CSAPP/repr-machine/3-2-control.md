# Control: Condition codes 

## Processor's states
1 bit flags (green below)

![Img](https://s2.loli.net/2023/05/10/ShYGP82UqxzKwB3.png)

- rsp = current stack top: where prog is executed
- rip = location of current code control code: instruction pointer 

## Conditional codes 
- Single bit registers 
    - CF = carry flag (2 unsigned number)
    - SF = sign flag
    - ZF = zero flag (computed is 0 - arith instr)
    - OF = overflow flag (for signed)
- Implicit setting by arith ops 
    - addq SRC DST <--> t=a+b 
    - CF set when carry out from MSB 
    - ZF if `t==0`
    - SF if `t<0` as signed 
    - OF if `a>0, b>0, t<0` or vise versa
- not set in leaq instr 
- Explicit setting by compare instr 
    - cmpq Src2 Src1 
    - cmpq b a like computing a-b without setting destination
    - CF set if carry out from MSB(unsigned)
    - ZF set if a==b
    - SF set if `(a-b)<0`
    - OF set `a>0, b<0, (a-b)<0 || a<0, b>0, (a-b)>0`
    - same general idea 
- Explicit setting by setting 
    - testq SRC2 SRC1 
    - sets condition codes based on value of SRC1 and SRC2
    - Useful to have one of the operands at mask.
    - ZF set when a&b==0
    - SF set when `a&b<0`

## Reading cond codes 

![Img](https://s2.loli.net/2023/05/10/hx4u3kNVza5rleT.png)

We can access low byte like this 

![Img](https://s2.loli.net/2023/05/10/p4TmoCXwKRHPdiY.png)
- l = low 
- one of addressable byte regs 
    - does not alter remaining bytes 
    - typically use movezbl to finish the job
        - 32 bit instr also set upper 32 bits to 0


Example. 
```c
int gt(long x, long y){
    return x>y;
}
```

compiled code: 
```asm
// %rdi = Argument x
// %rdi = Argument y 
cmpq %rsx, %rdi  # compare x:y
setg %al         # set when > 
movzbl %al, %eax # zero at rest of %rax
// move zero extension from byte to long
ret 
```

# Conditional branches 

## Jumping 

According to different cond flags , Jumping

![Img](https://s2.loli.net/2023/05/10/m87wxryVo15XRti.png)

uncond 
- no conds 

conds 
- jump accr to conds 

Example: conditional branch example 

```c
long absdiff(long x, long y){
    long res;
    if(x>y) res = x-y;
    else res=y-x;
    return res 
}
```

```asm
rdi = arg x 
rsi = arg y 
rax = ret val 
absdiff: 
    cmpq %rsi, %rdi 
    jle .L4
    movq %rdi, %rax 
    subq %rsi, %rax
    ret 

.L4
    movq %rsi, %rax 
    subq %rdi, %rax 
    ret 
```

We can express it like goto 

```c  
long absdiff_j(long x, long y){
    long res; 
    int ntest = x<=y;
    if(ntest) goto Else;
    res = x-y;
    goto Done;
    Else: 
        result = y-x;
    Done: 
        return result; 
}
```

## General conditional expr using branches 

C : `val = test ? then:else`

We have goto version: 
```c 
ntest = !Test;
if(ntest) goto Else;
val = Then_Expr;
goto Done;
Else: val = Else_Expr;
Done: 
...
```

## Using conditional Moves 
- Cond move instrs
    - instr supports `if(Test) Dest <= src`
    - supported in post-1995 x86 processors 
- Reason
    - Branches are very disruptive to instruction flow through pipelines 
    - cond moves dont require control transfer 

Example. 
```asm
movq %rdi, %rax     x:y
subq %rsi, %rax     result = x-y
movq %rsi, %rdx     
subq %rdi, %rdx     eval = y-x
cmpq %rsi, %rdi     x:y
cmovle %rdx, %rax   if <=, result = eval 
ret
```

## Bad cases for Cond move
```
val = Test(x) ? Hard1(x):Hard2(x)
```
- both vals are computed 
- only make sense when computations are very simple 

Risky computations 
```
val = p ? *p:0
```
- Both val gets computed 
- may have undesirable effects

Computaion with side effects
```
val = x>0 ? x*=7 : x+=3
```
- must be side-effect free

# Loops 

## Do-while loop

example: 

![Img](https://s2.loli.net/2023/05/10/KBx3yH7GAr5n8wJ.png)

```asm 
    movl $0, %eax    result=0
.L2                  loop:
    movq %rdi, %rdx       
    andl $1, %edx        t= x & 0x1
    addq %rdx, %rax      result+=t
    shrq %rdi            x>>=1
    jne .L2              if(x) goto loop 
    rep; ret
```

Translations: 

- do-while

![Img](https://s2.loli.net/2023/05/10/ywrL8ntDOjqlK64.png)


## While loop 
- while loop: jump to middle loop

![Img](https://s2.loli.net/2023/05/10/pTMAaOvNPZdnYfU.png)

- another generation: generate do-while 

![Img](https://s2.loli.net/2023/05/10/GlwSRJdW1sj6hV8.png)
![Img](https://s2.loli.net/2023/05/10/316rVZKBSeiGAIY.png)


Example 

![Img](https://s2.loli.net/2023/05/10/c3K97ksSiraZtzE.png)

## For loop 

- general form: `for(init; test; update) body`

translation 

![Img](https://s2.loli.net/2023/05/10/L1IaZ4rq89jDOoy.png)

- optimization 

![Img](https://s2.loli.net/2023/05/10/HSfwXIsURvLcZKT.png)


# Switch 

## Switch stat example 

![Img](https://s2.loli.net/2023/05/10/p7Yex1yV2qBsZMU.png)

![Img](https://s2.loli.net/2023/05/09/YAfKgpnvucs9STq.png)
## Jump table structure 

![Img](https://s2.loli.net/2023/05/10/6MNPKUDgYw23Sje.png)

Example. 

![Img](https://s2.loli.net/2023/05/10/lwrQXy4F1LJcVem.png)

![Img](https://s2.loli.net/2023/05/10/2JyAob8lLEHqGud.png)

![Img](https://s2.loli.net/2023/05/10/Gh7XMjwHCKszREU.png)


- ja = jump above: unsigned value x > 6, also it will jump less than 0
- jmp line is indirect jump 
- dealing with fallthrough is complex 

![Img](https://s2.loli.net/2023/05/10/eRo2P7mDEf1xcij.png)

- compilers can decide the rewrite of if-else 

# Summarizing 

![Img](https://s2.loli.net/2023/05/10/awqGs36vE2KhM8e.png)



$$
...\text{Original notes are placed below}...
$$


# Control

Objective: conditionals, loops, and switches, require conditional execution

Mechanisms:  it tests data values and then alters either the control flow or the data flow based on the results of these tests.

## Condition Codes

- Condition Codes
    - Description: CPU maintains a set of single-bit describing attributes of the most recent arithmetic or logical operation
    - idea: These registers can then be tested to perform conditional branches
    - consist: 
        - CF: Carry flag. Used to detect overflow for unsigned operations
        - ZF: Zero flag. The most recent operation yielded zero
        - SF: Sign flag. The most recent operation yielded a negative value
        - OF: overflow flag.  The most recent operation caused a two's- complement overflow—either negative or positive.(signed)
