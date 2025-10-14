# Assembly Crash Course

## set-register

Challenge instructs us to `rdi = 0x1337`, I got confused after just putting the instruction and trying to assemble the file but I got the error that the input is not an assembled binary


I assembled the following assembly code:-

```asm

.intel_syntax noprefix
.global _start
_start:

mov rdi, 0x1337

```

flag: `pwn.college{UGMU5woKCgX7NmQAjX-y6dIhFa-.0FN5EDL2MDO0czW}`


## set-multiple-registers

same shit, different code

```asm

.intel_syntax noprefix
.global _start
._start:

mov rax, 0x1337
mov r12, 0xCAFED00D1337BEEF
mov rsp, 0x31337

```

flag: `pwn.college{QHqSksYY3oYzAi3Bd7eyN6kRse6.dBTM4MDL2MDO0czW}`


## add-to-register

We have to add `0x331337` to the `rdi` register

```asm

.intel_syntax noprefix
.global _start
._start:

add rdi, 0x331337

```

flag: `pwn.college{YJ4-f_FJEk_BEfKzbCkCm7YmmZ7.0VN5EDL2MDO0czW}`


## linear-equation-registers

We have to compute a linear equation, `f(x) = mx + b` where `m` is in `rdi`, `x` is in `rsi` and `b` is in `rdx`

We have to place the result in `rax`:-

NOTE: `mul` -> unsigned multiplication, `imul` -> signed multiply


```asm
.intel_syntax noprefix
.global _start
._start:


mov rax, 0
add rax, rsi
imul rax, rdi
add rax, rdx

```

flag: `pwn.college{YIagHF19G5yU2JZngOSavLJCJu7.0lN5EDL2MDO0czW}`


## integer-division

A few things to note, `div` always works on `rax`, it can divide a 128-bit dividend by a 64-bit divisor:-

It does integer division and stores the value in `rax` and the remainder in `rdx`

```asm

mov rax, reg1
div reg2        ; reg1/reg2  

```

does:-

```asm

rax = (rdx:rax) / reg     ; rdx:rax used to store a 128-bit operand
rdx = remainder

```

we have to compute `speed = distance / time`

- distance = `rdi`
- time = `rsi`
- speed = `rax`


code:-

```asm
.intel_syntax noprefix
.global _start
._start:


mov rdx, 0;       ; rdx must be zeroed
mov rax, rdi
div rsi

```

flag: `pwn.college{wawtBQDmPk2SVfqQJHF2EalgYsg.01N5EDL2MDO0czW}`

## modulo-operation

Need to calculate `rdi % rsi`, but the `div` operation works on `rax` so I'll set `rdx` to 0 and `mov rax, rdi`

after doing `div rsi` the remainder will be stored in `rdx`


```asm

mov rdx, 0
mov rax, rdi
div rsi
mov rax, rdx

```

need to place the remainder (`rdx`) in `rax`

flag: `pwn.college{kKmBHzoA6B7yQ5n036gMjTf9-Op.0FO5EDL2MDO0czW}`


## set-upper-byte

bruh.

```asm

mov ah, 0x42

```

flag: `pwn.college{4t5VS7TaYDvVTltRTEsPeiTWfkR.dFTM4MDL2MDO0czW}`


## efficient-modulo

apparently using `div` to compute, the modulo is slow as hell.

the trick is if `x % y` and `y = 2^n` then the result will be lower `n` bits of `x`

we need to compute:-

```
rax = rdi % 256
rbx = rsi % 65536

```

256 -> 8th power of 2
65536 -> 16th power of 2

`rax` should take in the lower 8 bits of `rdi`
`rbx` should take in the lower 16 bits of `rsi`

lower 8 bits of `rdi` -> `dil`
lower 16 bits of `rsi` -> `si`

```asm

mov rax, 0
mov rbx, 0

mov al, dil
mov bx, si

```

Incorrect tangent:-


Won't work due to the second and fourth instruction, can't put a smaller register into a bigger one. To do that `movzx`, should be used which clears the higher bits


```asm

mov rax, rdi
mov rax, al

mov rbx, rsi
mov rbx, bx
  
```

## byte-extraction

`shr`, `shl` shifts the values to right and left

need to set `rax` to the 5th LSB of `rdi`

`rdi = | B7 | B6 | B5 | **B4** | B3 | B2 | B1 | B0 |`

OH WE ARE DEALING WITH BYTES NOT BITS!


```asm

mov rax, rdi
shl rax, 24
shr rax, 56

```

flag: `pwn.college{8iUlPuED1l_m50Ar9O9nNhAvkis.0FMwIDL2MDO0czW}`


## bitwise-and

this works (we need to write `rdi & rsi` to `rax` without using `mov`, `xchg`) 

NOTE: `lea` works as `mov`, also it doesn't dereference the register it just loads the value stored

```asm

and rdi, rsi
lea rax, [rdi]

```

## check-even

Have to implement this-

```
if x is even then
  y = 1
else
  y = 0

```

where `x = rdi`, `y = rdx`

code:-

```asm

and rdi, 1
xor rdi, 1
xor rax, rax
or rax, rdi

```
need to `and rdi, 1` first because if we straight up `xor` it then the 0s in 1's 64bit representation will mess up rdi, on being `xor`'ed, we won't be just left with the LSB

- `xor`ing with `1` just flips the bit

flag: `pwn.college{4i0L32WT0rIEHOTQHndKhDKCugB.0lMwIDL2MDO0czW}`


## memory-read

NOTE:-

1. `mov rax, [some_address]` <=> Moves the thing at 'some_address' into `rax`

2. `mov rax, [rdi]` <=> Moves the thing stored at the address of what `rdi` holds to `rax`

3. `mov [rax], rdi` <=> Moves `rdi` to the address of what `rax` holds.


Place the value stored at `0x404000` into `rax`. Make sure the value in `rax` is the original value stored at `0x404000`.

```asm
mov rax, [0x404000]
```

flag: `pwn.college{UD_ZT52atqP2b9bU-IiUMTLBglk.dJTM4MDL2MDO0czW}`

## memory-write

Place the value stored in `rax` to `0x404000`.

code:-

```asm

mov rbx, 0x404000
mov [rbx], rax

```

flag: `pwn.college{Qo35tx9ZR0pdntFUYnGNqu952hV.dNTM4MDL2MDO0czW}`


## memory-increment

- Place the value stored at `0x404000` into `rax`.
- Increment the value stored at the address `0x404000` by `0x1337`.

```asm

mov rax, [0x404000]
mov rbx, rax
add rbx, 0x1337
mov qword ptr [0x404000], rbx

```

flag: `pwn.college{cOH4wL7cJzUzL0qgyHlGTc4x-zx.01MwIDL2MDO0czW}`


## byte-access

- mov al, [address] <=> moves the least significant byte from address to rax
- mov ax, [address] <=> moves the least significant word from address to rax
- mov eax, [address] <=> moves the least significant double word from address to rax
- mov rax, [address] <=> moves the full quad word from address to rax
- moving into smaller register doesn't clear the upper bytes


we need to set `rax` to the byte at `0x404000`, I assume the byte referred to is LSB because memory addresses are in little-endian (LSB first)

code:-


```asm
xor rbx, rbx
mov bl, [0x404000]

xor rax, rax
mov al, bl
```

flag: `pwn.college{IHx1e_9ra-flguDU4tNPfpxfMm_.dRTM4MDL2MDO0czW}`


## memory-size-access


Please perform the following:

- Set `rax` to the byte at `0x404000`
- Set `rbx` to the word at `0x404000`
- Set `rcx` to the double word at `0x404000`
- Set `rdx` to the quad word at `0x404000`


code:-

```asm
xor rax, rax
mov al, [0x404000]

xor rbx, rbx
mov bx, [0x404000]

xor rcx, rcx
mov ecx, [0x404000]

xor rdx, rdx
mov rdx, [0x404000]
```

`xor`ing is done to zero out the registers before doing anything

flag: `pwn.college{Eqbc44MH6OUjkGGrjF0oB1WymEP.0FNwIDL2MDO0czW}`


## little-endian-write

If you examined how it actually looked in memory, you would see:

```md

[0x1330] = 0xde
[0x1331] = 0xc0
[0x1332] = 0xad
[0x1333] = 0xde
[0x1334] = 0x00
[0x1335] = 0x00
[0x1336] = 0x00
[0x1337] = 0x00

```

This format of storing things in 'reverse' is intentional in x86, and it's called "Little Endian".

Using the earlier mentioned info, perform the following:

- Set `[rdi] = 0xdeadbeef00001337`
- Set `[rsi] = 0xc0ffee0000`


code:-

```asm
.intel_syntax noprefix
.global _start
._start:


xor rax, rax
xor rbx, rbx

mov rax, 0xdeadbeef00001337
mov rbx, 0xc0ffee0000

mov [rdi], rax
mov [rsi], rbx
```


## memory-sum

Say we access the quad word at `0x1337`:

`[0x1337] = 0x00000000deadbeef`
The real way memory is laid out is byte by byte, little endian:

```asm
[0x1337] = 0xef
[0x1337 + 1] = 0xbe
[0x1337 + 2] = 0xad
...
[0x1337 + 7] = 0x00
```

Say you want the 5th byte from an address, you can access it like:

```asm
mov al, [address+4]
```

Remember, offsets start at 0.

Perform the following:

- Load two consecutive quad words from the address stored in rdi.
- Calculate the sum of the previous steps' quad words.
- Store the sum at the address in rsi.


code:-

```asm
mov rax, qword ptr [rdi]
mov rbx, qword ptr [rdi+8]
mov rcx, rax
add rcx, rbx
mov [rsi], rcx
```

flag: `pwn.college{cSG4DnR-21KLHldvq12AxD9YGwP.0lNwIDL2MDO0czW}`

i tried this but this failed:-

```asm
mov rax, qword ptr [rdi]
mov rbx, qword ptr [rdi+8]
mov [rsi], 0
add [rsi], rax
add [rsi], rbx
```

NOTE: `requires that the memory at [rsi] is actually writable and properly aligned`, I was getting some similar error saying size was ambiguous


## stack-subtraction

Introduction to the stack! 

1. LIFO memory structure, rsp points to the top of the stack
2. push rax, makes space in stack (rsp-8) and `mov [rsp], rax`
3. pop rbx, loads [rsp] to rbx and shrinks stack (rsp+8)
4. stack grows downwards! thats why + for shrinking, - for making space


Using these instructions, take the top value of the stack, subtract rdi from it, then put it back.

code:-

```asm
pop rbx
sub rbx, rdi
push rbx
```
flag: `pwn.college{wqjtmVTImzpy7wRwA6WO5Ycz5Pq.01NwIDL2MDO0czW}`

## swap-stack-values

Using only the following instructions:-

- push
- pop

Swap values in rdi and rsi.

code:-

```asm
push rdi
push rsi
pop rdi
pop rsi
```

flag: `pwn.college{MZO0igkLIXXS-TYPKkL8Aeqva4F.0FOwIDL2MDO0czW}`

## average-stack-values

Without using pop, please calculate the average of 4 consecutive quad words stored on the stack. Push the average on the stack.

Hint:

- `RSP+0x?? Quad Word A`

- `RSP+0x?? Quad Word B`

- `RSP+0x?? Quad Word C`

- `RSP Quad Word D`

code:-

```asm
mov rax, qword ptr [rsp]
add rax, qword ptr [rsp+8]
add rax, qword ptr [rsp+16]
add rax, qword ptr [rsp+24]
mov rdx, 0
mov rcx, 4
div rcx
push rax
```

flag: `pwn.college{wNUk3NwHEMqPKvDoy2XoLoNLBC3.0VOwIDL2MDO0czW}`

## absolute-jump

two types of jump

1. Unconditional: always trigger
2. Conditional: conditional jumps

In x86, absolute jumps (jump to a specific address) are accomplished by first putting the target address in a register reg, then doing jmp reg.

In this level, we will ask you to do an absolute jump. Perform the following: Jump to the absolute address 0x403000.

code:-

```asm
mov rax, 0x403000
jmp rax
```

flag: `pwn.college{MteDTWwAQJiSeELdIcSGlzIHfO_.dVTM4MDL2MDO0czW}`
