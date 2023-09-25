# Why Studying this course? 

- From abstraction to reality. 
- Knowing how bugs are made and how to avoid them. 

# Some weird programs

## Ex1. 

```c
typedef struct{
    int a[2];
    double d;
} struct_t;

double fun(int i){
    volatile struct_t s;
    s.d = 3.14;
    s.a[i] = 1073741824;
    return s.d;
}

```

Explanation: 

![Img](https://s2.loli.net/2023/05/08/6oFft5qlLirvUSC.png)

- box = 4 bytes
## Ex 2. 

![Img](https://s2.loli.net/2023/05/08/tymKa63v87d4r2O.png)

The first is 100x faster than the second! 