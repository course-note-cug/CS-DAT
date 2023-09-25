# Background 

## mask 
In computer science, a mask is a binary pattern used to select or filter specific bits in a value. Masks are often created by using bitwise operators, such as the bitwise AND &, bitwise OR |, bitwise XOR ^, and bitwise negation ~.

To create a mask that selects specific bits in a value, you can use the bitwise AND operator &. For example, to extract the least significant 4 bits of a value x, you can use the mask 0xF (which is binary 1111), like this: x & 0xF. This will return a value with only the least significant 4 bits set, and all other bits set to 0.

To create a mask that toggles specific bits in a value, you can use the bitwise XOR operator ^. For example, to toggle the least significant bit of a value x, you can use the mask 1 (which is binary 0001), like this: x ^ 1. This will flip the least significant bit of x.

To create a mask that sets all bits in a value to 1 or 0, you can use the bitwise negation operator ~. For example, to create a mask that sets all bits to 1, you can use ~0 (which is binary 1111...1111), and to create a mask that sets all bits to 0, you can use ~(~0) (which is binary 0000...0000).

# AsciiDigit

一开始想画树状图做, 但是指令超限. 于是考虑使用异或语句和一些特定的pattern. 

问题: 
- How to represent digits with fixed pattern? 
- How to represent digit that relys on previous digits?
- How to represent digit that is unrestricted?

# Conditional 

