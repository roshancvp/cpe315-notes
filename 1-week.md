## Instructions
`<opcode> <dest> <src 1> <src 2>`

### Arithmetic
* add
* sub
* mult
* div

### Logic
* and 
* not
* or
* xor
* nor
* nor

### Other
* cmp - compare
* mov - move

#### Example: Adding Two Numbers:
`add r0,r1,r2 @r0=r1+r2`

### Memory
* load
* store
* push
* pop

### Control Flow
* br - branch - if, while
* bl - branch and link - subroutine call
* bx - return 

#### Example: If Statement
```c
if (x == y) 
	equal = 1
else 
	equal = 0
```

```
@ armcode
@ x is in R0
@ y is in R1
@ equal is in R2

cmp R0, R1	@ x == y?
bne else    	@ else is a label where code execution continues
mov R2, #1  	@ equal = 1
b endif	   
else
	mov R2, #0 	@ equal = 0
endif
```

- - - -

```c
sum = 0
for (int i = 0; i < 100; i++) 
	sum += 1
```

```
@ assembly
@ sum is R0
@ i is R1

mov R0, #0
mov R1, #0
mov R2, #100		@ setting a constant 
loop cmp R1, R1	@ i < 100
	beq exitfor		@ beq - branch if equal
	add r0, r0, #1 	@ sum += 1
	add r1, r1, #1	@ i += 1
	b loop
exitfor
```

### Do-while
```c
sum = 0
i = 0
loop {
	sum += 1
	i += 1
} do while (i < 100)
```

```
mov R0, #0 
mov R1, #0
mov R2, #100
loop add R0, R0, #1
	add R1, R1, #1
	cmp R2, R1
	bne loop
end loop 
```

- - - -

```c
int sum(int x, int y) {
	return x + y
}
```

```
@assembly
.global sum  @ directive, indivating label sum is a directive, doesn't create the label
sum
	add r0, r0, r1
	bx lr				@ branch to linked register
@ end sum
```


## Haiku - lab01
There are only three lines, totaling 17 syllables.
The first line is 5 syllables.
The second line is 7 syllables.
The third line is 5 syllables like the first.
Punctuation and capitalization are up to the poet, and need not follow rigid rules used in structuring sentences.
A haiku does not have to rhyme, in fact usually it does not rhyme at all.
Some haiku can include the repetition of words or sounds

- - - -
## Registers
r0-r3	-> parameters
r4-r12 	-> callee save (variables) - save on the stack if calling another function
r0, r1 	-> return value registers
r13 		-> stack pointer (sp)
r14 		-> link pointer  (lr)
r15 		-> program counter (pc)

- - - -

```c
void swap(int *x, int *y) {
	int temp;
	temp = *x;
	*x = *y;
	*y = temp
}
```

```
@ arm assembly
.text 
.glabal swap
swap	ldr r3, [r0]		@ r0 -> r3, get *x
		ldr r2, [r1]		@ get *y
		str r2, [r0]
		str r3, [r1]
		bx lr				@ RET, branch to contents of register
@ end swap
.global sum
	...
```

- - - -

```c
extern int swap(int *x, int *y);
extern int sum(int, int);
int func(int a, int b) {
	swap(&a, &b);
	return sum(a, b);
}
```

```
@ assembly
@ sp - stack pointer
.text
.global func
func: 
	add sp, sp, #-4
	str lr, [sp]
	sub sp, sp, #12
	str r0, [sp, #4]
	add r3, sp, #8
	str r1, [r3, #-8]!		@ ! - ++x, pseudo = str r1, [r3 = r3 - 8]
	mov r1, r3
	add r0, sp, #4
	bl swap 	
	ldr r1, [sp]
	ldr r0, [sp, #4]
	bl sum
	add sp, sp, #12
	ldr pc, [sp], #4		@ equivalent to ++x 
```
