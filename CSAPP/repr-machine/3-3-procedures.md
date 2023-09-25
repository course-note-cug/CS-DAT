# Intro
- ABI : interface of machine level programming 
- Machanics in procedures 
    - passing ctrls 
        - know how to start proced call 
        - know how to return it 
    - passing data 
        - procedure args 
        - return val
    - memory management
        - allocation during the procedure call 
        - deallocate upon return 
    - machine all implenent with machine instrs 

# Stack structure 

![Img](https://s2.loli.net/2023/05/12/FrRxZP4VlfSvi7T.png)
- adding an element = decrease the stack pointer 

## push 

- pushq SRC 
    - Fetch operand at SRC
    - decrease %rsp by 8
    - write operand at address given by %rsp 

- popq DST 
    - read val at addr given by rsp 
    - increase %rsp by 8
    - store value at dest(must be register)

# Calling convensions 

Examples: 

![Img](https://s2.loli.net/2023/05/12/ZX1nDxEzKlU9j8k.png)

## Procedure control flow 
- use stack to support procedure call and return 
- procedure call: call, label
    - push return address to stack 
    - jump to label 
- return address 
    - address of the next instr right after the call 
    - example from disassembly
- procedure return : ret 
    - pop addr from stack 
    - jump to addr 

Example: we assume the stack top part is at 0x120, 

![Img](https://s2.loli.net/2023/05/12/SXNHnMftp2wqxDb.png)

![Img](https://s2.loli.net/2023/05/12/Cfo2WyhtF9BG38w.png)
- increase the stack pointer
- write the addr of the following instruction (going to return )
- change the dest addr to rip 

![Img](https://s2.loli.net/2023/05/12/rHp7QojZGXIB9lh.png)
- after ret instr is hit
- assume the top of stack has an address 
- pop that addr
- jump to the addr


## Passing data 

According to ABI: 

![Img](https://s2.loli.net/2023/05/12/BQ8gDd4YolyPHJa.png)
- for ints and pointers 

Example: 

![Img](https://s2.loli.net/2023/05/12/tLm4RSOpQjHnPTu.png)


## Managing local data

stack based languages 
- languages that supports recursion 
    - code must be reentrant((of an angle) pointing inward)
        - multiple simultaneous instantiations of single procedure
    - need some space to store data of each one 
        - args 
        - local vars 
        - return ptr
- stack descipline 
    - state for given procedure need for limited time 
        - from when called to when return 
    - callee returns before caller does 
- stacks allocated in frames
    - state for single procedure instantiation

example: 

![Img](https://s2.loli.net/2023/05/12/3MtHAqglTI1WE8L.png)

stack frames 

![Img](https://s2.loli.net/2023/05/12/G1vuCzmaAtiUR5I.png)


- contents 
    - return info 
    - local stoarge (if needed )
    - temp space (if needed )
- mamagement 
    - space allocated when enter procedure 
        - set-up mode 
        - includes push by call instr 
    - deallocated when return 
        - finish code 
        - includes pop by ret instr 

Example: 

![Img](https://s2.loli.net/2023/05/12/3NwubPAkvZsa6fV.png)

![Img](https://s2.loli.net/2023/05/12/AgazjkfXMsnwqbi.png)

![Img](https://s2.loli.net/2023/05/12/kxmiPjsu1MyzVOG.png)

![Img](https://s2.loli.net/2023/05/12/ba1U5odQc6wjnGg.png)

![Img](https://s2.loli.net/2023/05/12/kxmiPjsu1MyzVOG.png)

![Img](https://s2.loli.net/2023/05/12/AgazjkfXMsnwqbi.png)

![Img](https://s2.loli.net/2023/05/12/3NwubPAkvZsa6fV.png)

![Img](https://s2.loli.net/2023/05/12/GmRFSotNyvg254V.png)

![Img](https://s2.loli.net/2023/05/12/5xt6jPEmHXhagri.png)


- each one has its local state 

stack frames looks like: 

![Img](https://s2.loli.net/2023/05/12/oiY7WfdzEvj3luB.png)

Example: 

![Img](https://s2.loli.net/2023/05/12/EGLM2o6ZIpaFlQA.png)

![Img](https://s2.loli.net/2023/05/12/cflsFt4ITU5Q1Ok.png)
- red one allocs states 

![Img](https://s2.loli.net/2023/05/12/PErD9ayxC8AwIc7.png)
- movl stores the 2nd instr of the call 
- lea is used to create pointers 
    - copy the computed address to rdi

then call instr 

![Img](https://s2.loli.net/2023/05/12/ua9gT5xJEUFjXWh.png)

![Img](https://s2.loli.net/2023/05/12/jo1OhixRwvpZ8Pu.png)

![Img](https://s2.loli.net/2023/05/12/EK6QiqdXsTpH751.png)


register saving convensions 
- may be overwritten 

![image.png](https://s2.loli.net/2023/05/12/dhGm8vSwbOZalky.png)

![Img](https://s2.loli.net/2023/05/12/sTy8pvAchMW3Lnq.png)

x86-64 register usage 

![image.png](https://s2.loli.net/2023/05/12/Zed2SLzGwWKpOgs.png)

![Img](https://s2.loli.net/2023/05/12/981pPSl234aFQYu.png)

Example; 

![Img](./FILES/3-3-procedures.md/img-20230512105605.png)

![Img](./FILES/3-3-procedures.md/img-20230512105705.png)

# Recursion example 

![Img](./FILES/3-3-procedures.md/img-20230512105953.png)

![Img](./FILES/3-3-procedures.md/img-20230512110054.png)

## Observations about recursion 

![Img](./FILES/3-3-procedures.md/img-20230512110627.png)

# Summary 

![Img](./FILES/3-3-procedures.md/img-20230512110844.png)

    