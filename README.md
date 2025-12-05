### TSiC
TSiC - Type Safety in C - A minimalistic system for packing pointers and metadata into numbers!

### About
A pointer in 64-bit systems is 64 bits, but only 48 bits are actually used for addressing...
This means we can pack these 48 bits plus 16 bits of metadata into a standard 64-bit unsigned integer!

Why? Well... Metadata can store the validity of a pointer, the type of resource it points to, and much more!
Essentially... We get the ability to perform fast (A few processor cycles) checks, significantly increasing code security!
With no overhead)

### Example
```c
#include <stdio.h>

typedef unsigned long long TSiC;

int main() {
    int D = 72;
    void* P = &D;
    
    unsigned long long A = (TSiC)P;
    unsigned long long B = A & 0x0000FFFFFFFFFFFFULL;
    
    unsigned short T = 0xABCD;
    unsigned long long H = B | ((TSiC)T << 48);
    
    printf("Original - %p\n", P);
    printf("TSiC - 0x%016llX\n", H);

    TSiC E = H & 0x0000FFFFFFFFFFFFULL;
    
    if (E & (1ULL << 47)) {
        E |= 0xFFFF000000000000ULL;
    }
    
    void* R = (void*)E;
    T = (unsigned short)(H >> 48);
    
    printf("Restored - %p\n", R);
    printf("Type - 0x%04X\n", T);
    
    if (T == 0xABCD) {
        printf("Type matches! Safe to use!\n");
        int* F = (int*)R;
        printf("Data - %d\n", *F);
    } else {
        printf("Type mismatch! Access denied...\n");
    }
    
    return 0;
}
```
