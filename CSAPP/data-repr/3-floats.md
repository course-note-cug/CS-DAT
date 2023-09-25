# Fixed point float numbers

![Img](https://s2.loli.net/2023/05/09/f7hWAvtQakCHZU4.png)

$$
\sum_{k=-j}^i b2^k
$$

Observation: 
- /2 <==> shift right (unsigned)
- *2 <==> shifting left
- Numbers of form (0.111111)$_2$ is less than 1.0
    - use 1.0-eps

Limitation
- can only repr num of form $x/2^k$, other ones cant be done
- just one setting of bin point within the w bits

# IEEE Floating point 

![Img](https://s2.loli.net/2023/05/09/vMyg3wDWOY98iIn.png)


- Numerical Form: $(-1)^s M 2^E$
    - sign bit s determin whether the number is neg 
    - significand M normally a frac val between 1 and 2
    - Exp E weights val by pow of 2
- Encoding 
    - MSB s is the sign bit of s
    - exp field encodes E(not equal to E)
    - frac field encodes M (not eq to M)

## Different precisions

![Img](https://s2.loli.net/2023/05/09/XYwDiSRQVJFGqEx.png)

## Normalized values
- Def. when exp != 00000000 and exp != 1111111
- Exponent coded as a biased value: E=Exp-Bias
    - Exp: unsigned val of exp field
    - Bias: 2$^k-1$-1, k is the num of exp bits
        - Single precision 127 (Exp:1..254, E:-126..127)
        - Double precision 1023 (Exp:1..2046, E:-1022..1023)

Why not using 2's comp in exp field? to make sure the comparson is ok. 

- Significand coded with implied leading 1: M=1.xxxxxx
    - xxx: bits of frac field 
    - Min: 0000000000, M=1.0
    - Max: 1111111111, M=2.0-eps 
    - get extra leading bit for free

Example: 

Keep in mind that: 
- $v=(-1)^s M 2^E$
- E=Exp-Bias

`F=15213.0`: 
- 15213=11101101101101$_2$
- 1.1101101101101$_2$ $\times 2^{13}$
- Significand 

```
M     = 1.1101101101101
frac  =   11011011011010000000000

E     = 13
Bias  = 127
Exp.  = 140 = 10001100

Result: 0 10001100 1101101101101000..0
        s.  exp.        frac.  
```

Q: What's the range val of exp, E? 
- $0\leq $ EXP $\leq 2^8-1=255$
- $-127 \leq E \leq 128$

repr nums close to 0 will limit us. 

## Denorm vals 
- no implied 1 

$$v=(-1)^sM2^E, E=1-\text{Bias}$$

- cond: exp=000...0
- Exp val: E=1-Bias (instead of 0-Bias)
- Significand coded with implied leading 0: M = 0.xx..x 
    - xxx..x: bits of frac
- Cases 
    - exp=000..0, frac = 000.0
        - repr 0 val 
        - distinct val: +0, -0
    - exp = 000..0, frac ≠ 000...0
        - numner clostst to 0.0
        - equispaced

## Special vals 
- Condition: exp = 111..1
- Case: exp = 111..1, frac=000..0
    - represents $\infty$
    - op that overflows
    - both pos and neg 
    - eg: 1.0/0.0 = +$\infty$, 1.0/-0.0=-$\infty$. 
- Case: exp=111..1 frac ≠ 000..1
    - Not a number 
    - case when no numeric value can be determinated,
    - sqrt(-1), $infty-infty$, $infty \times 0$

Visualizing floating point encodings: 

![Img](https://s2.loli.net/2023/05/09/EdCmNIZ5aAtpToL.png)

# Example and prop

Basic setup: tiny fp example: 

```
 s   eeee fff
sgn  exp  frac
 1    4     3
```

- Explanation: 
    - the sign bits is the MSB 
    - the next 4 are exp, with bias of 7
    - the last 3 are frac 
- same general foem as IEEE format

![](https://s2.loli.net/2023/05/09/CfTbD5iHPnQRJmd.png)

## Distribution of vals 

![Img](https://s2.loli.net/2023/05/09/Iuf3CkFSyieXjYL.png)

- dense around 0

![Img](https://s2.loli.net/2023/05/09/Xpr15cwAaUeO2dS.png)

## Special props of IEEE enc 

- FP zero == Int zero (all bits = 0)
- can almost use unsigned integer comparsion
    - must first compare sign bits 
    - must consider -0 = 0
    - NaNs are problematic
    - o.w. ok.

## Floating point operations: Basic ideas 

Basic idea: 
- first compute exact result 
- make it fit into desired precision 
    - possibly overflow if expoment too large
    - possible round to fit into frac 

Rounding 

![Img](https://s2.loli.net/2023/05/09/egPGROHKrFMUd3C.png)

- by default round to Nearest even(deals with middle part)

Round to even 
- Default rounding mode 
    - sums are staistically biased 
- applying to other decimal places/ bit pos
    - when exactly halfway between  2 possible vals 
        - round so that least significant digit is even 

Example. round to nearest hundredth
![Img](https://s2.loli.net/2023/05/09/6RYfuaxDXk7LT4v.png)

![Img](https://s2.loli.net/2023/05/09/mPgnsStJvkVliEA.png)

## FP Mul 

- Exact Res: (-1)^s^ M 2^E^
    - sign s = s1 ^ s2 
    - significand M : M1 * M2 
    - expoment E: E1+E2
- Fixing 
    - if M≥2, shift M right, increment E 
    - if E out of range, overflow 
    - round M to fit frac precision 
- implementation: biggest chore is multipluing significanads

## FP addition 

![Img](https://s2.loli.net/2023/05/09/VdHr49Job3g72LC.png)

But still some props... 

![Img](https://s2.loli.net/2023/05/09/3AMztZDmL5uvWYr.png)

## FP in C 

![Img](https://s2.loli.net/2023/05/09/tvTUVoDlFE2m3e7.png)
