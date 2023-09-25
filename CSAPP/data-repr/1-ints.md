# Counting in binary-like systems
- Conversion from 10 to 2
- Use base-16 a-f

Techique: get hex to bin: 
- First get 0-9's bin repr
- special cases: 
    - 1010 = a
    - 1100 = c
    - 1111 = f

byte: 8 bits

length in C on x86-64 systems

![Img](https://s2.loli.net/2023/05/08/isBY7F8tGUaEMOp.png)

64 bit machine = the address space has 64 bits

# Bit operations in C


*Example*. Manipulating and representing sets

Bit-level ops in C 
- range: to integer-like values (int long short char unsigned)
- syntax: apply bitwize

Difference between logic ops (&& || !)
- 0 = false
- else is true
- always return 0 or 1
- **early return**
    - avoiding dereferencing nullptrs

Shifting ops
- left shift x<<y
    - shift bit vec x left to y bits
        - throw away extra bits on the lft 
    - fill 0 on the right
- right shift x>>y
    - shift bit vec x right y pos 
        - throw away out pos on the right 
    - Logical shift: fill with 0s on left 
    - Arith shift: replace most significant bits on the lft 
- UB: shift amount <0 or >= word size

*Example*. 

![Img](https://s2.loli.net/2023/05/08/cQEHz5Cf4uWRMdq.png)

# Number repr 

Encoding ints 
- unsigned 

$$
B2U = \sum_{i=0}^{w-1} x_i 2^i
$$

- signed 
$$
B2U = -x_{w-1}2^{w-1}+\sum_{i=0}^{w-2} x_i 2^i
$$

*Example*. 

![Img](https://s2.loli.net/2023/05/08/LalYP6AZVrhFR4H.png)


Numeric ranges 
- unsigned vals 
    - min = 0, max = $2^w-1$.
- 2's compl vals 
    - min = $-2^{w-1}$, max = $2^{w-1}-1$
- other
    - -1 = (11111111)$_2$. (all ones)


*Example*. 

![Img](https://s2.loli.net/2023/05/08/5NQpjiK2q6W9TD4.png)

![Img](https://s2.loli.net/2023/05/08/K97DsjSJpBnvlF8.png)


Fun fact: Signed & unsigned vals 
- same enc for non neg vals 
- uniq 
- can make invert mappings $f \circ f^{-1} = e$
- Remember: Info is bits + contexts! 

*Visualize*: 

![Img](https://s2.loli.net/2023/05/08/MaOj5XfCvl9NxD7.png)


Signed & unsigned in C 
- consts: suffix unsigned U
- casting: (int) (unsigned)
    - implicit occurs: if mixed of signed and unsigned, then **sign will be cast to unsigned** 

*Example*. 
```c
for(unsigned i=n-1; i>=0; i--) f(a[i]);
```
will cause mem fault. i is always >= 0 since i is unsigned. 

sizeof() will also return a unsigned val, which is bad. 


# Sign extension 

Task: Given w-bit signed int x, convert it to w+k bit int with the same val

Do: make k copies of signed bit at the first bit. 

![Img](https://s2.loli.net/2023/05/08/5Y1iavAO6RVFI7k.png)

flipping back can make the cases back. 

# Addition 

## Unsigned addition 

- Mode
![Img](https://s2.loli.net/2023/05/08/C1YUnZh3PAaLWFQ.png)

- when overflow: no warning! 
- same as mod arith

Intunitive meaning: observe 4 bit case

![Img](https://s2.loli.net/2023/05/08/lErYHTFZ5XvVyxw.png)

![Img](https://s2.loli.net/2023/05/08/62AUjyLrIdtFcpx.png)

## 2's compl addition 

![Img](https://s2.loli.net/2023/05/08/FmpDnfJiQkSsBTX.png)


Tadd and Uadd will have identical bit behavior 

Example: -5 + 3 = -2
```
         -8 4 2 1
 -5  =    1 0 1 1
 2   =    0 0 1 0
------------------
 -3  =    1 1 0 1
```

- overflow 
    - pos + $+$ + ==> -.
    - neg - $+$ - ==> +.

- function 
    - w+1 bits 
    - drop MSB 
    - treat as 2's compl

![Img](https://s2.loli.net/2023/05/08/VG8jvacnRifrKML.png)

## Multiplication 
- Goal: computing prod of w-bit numbers x and y (signed or unsigned)

Unsigned case 

![Img](https://s2.loli.net/2023/05/08/8bNRcvpSCaDzW5k.png)

- ignores high w bits. i.e. uv mod 2$^w$.

Example: -3$\times$6

Trick: power of 2 multiply with shift
- u<<k gives u $\times$ 2$^k$. 

![Img](https://s2.loli.net/2023/05/08/zDQ5yvx37EMOp4Y.png)

## Unsigned powof2 divide with shift

- u>>k gives $\lfloor$ u/2$^k\rfloor$

with arith right shift, it can be extended, but rounding to -$\infty$. 

Fix: want to round to 0, add a bias 

## Ways to negate number 

Goal: x $\to $ -x

Method: flip all the bits then add 1. 

## When to use unsigned 
- Dont use while unknown to casts 
- use size_t or sth instead

use unsigned as loop idx: 
```cpp
for(unsigned i=cnt-2; cnt>i; i--)...
```

- do use unsigned: modular arit
- do use when performing bits ops
