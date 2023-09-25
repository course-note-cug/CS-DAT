# Mem orientation
## Byte-oriented mem orgs 

![Img](https://s2.loli.net/2023/05/08/FqdYpuHg5PUfJNt.png)

- prog transfer data by addrs
    - see it as large array of bytes
    - like index into an array
- system provides *private* addrs for each progs

## Machine words 

- Any given comp has a Word size 
    - normal val of int-val data and addrs 
    - recently we have 1 word = 8 bits 
- bits
    - 64 bits = 18 PB mems max,
    - 32 bits = 4 GB  mems max.

## Word ori mem org 

![Img](https://s2.loli.net/2023/05/08/PuBrWeUyMjsAvki.png)

This way it can run faster

## Byte ordering
multibyte things order in mems? 

Ways:
- Big Endian
- little endian

![Img](https://s2.loli.net/2023/05/08/YeifoqK23pkjJ6c.png)

Another example: 

![Img](https://s2.loli.net/2023/05/08/uVQiDaF2rbeGTZ5.png)

# Examining data reprs

casting to unsigned char * allows to treat as a byte array 

```
typedef unsigned char *pointer;
void show_bytes(pointer start, size_t len){
    size_t = i;
    for(int i=0; i<t; i++){
        printf("%p\t0x%2.x\n", start+i, start[i]);
    }
}
```

# Repr strs

![Img](https://s2.loli.net/2023/05/08/Gh2nriRmFY8Ipy1.png)

