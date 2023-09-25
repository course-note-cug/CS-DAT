# The Git 

Create a new branch called `pa0`. 
```shell
git checkout -b pa0
```

Switch between versions, i.e. switch to `pa0` 
```shell
git checkout pa0
```

# The construction of the project
- KConfig: handle the dependencies between the `#define`s.

## Makefile 

- symbols `$<` and `$@` are used as automatic variables to represent specific values.

I. Some variables

1. `$<`: This variable represents the first prerequisite (dependency) of a target in a rule.

Example: 
```makefile
target: dependency1 dependency2
    <command>
```
this will say dependency 1. 

2. `$@`: This variable represents the target (output) of a rule. It refers to the file being built or processed by the rule. 

```makefile
target: dependency
    <command> -o $@
```
refer to the target file target.

II. Observe its execution

Using `make -nB` will do. Also, these can be observe with `-D`. 

#  First Guest program

## Read the program into mem

I. ISA 

II. Memory 

- using `uint8_t` as mem 
- See `nemu/src/memory/paddr.c` 
- Guest at `RESET_VECTOR` in `paddr.h`. 

## Init Registers

# Bonus

How to push NJUPA to my own Git repo? 

You may set the following: 
```shell
git remote set-url --push <remote_name> <new_remote_url>
```

# Vim tips

Use `Ctrl+a` to increment a number, `Ctrl+x` to decrement a number. 

# The genexpr 

## Generate random int 

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main() {
    // Seed the random number generator with the current time
    srand(time(NULL));

    // Define the range of the random number
    int min = 1;
    int max = 100;

    // Generate a random integer within the range
    int randomInt = (rand() % (max - min + 1)) + min;

    // Output the generated random integer
    printf("Random integer: %d\n", randomInt);

    return 0;
}
```

## Count lines 

```shell
find ./src -name '*.c' | xargs wc -l
```

