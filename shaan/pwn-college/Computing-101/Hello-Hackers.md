# Hello Hackers

## Somethings to note before we begin

1. syscall is triggered by the specified value in `rax`
2. arguments -> `rdi`, `rsi`, `rdx`, `r10`, `r8`, `r9`
3. return value in rax
4. some system arguments take 'string' arguments, just specify the `rsp` to `rdi` or wherever required. `rsp` having the starting address of the string on stack

5. some system calls have archaic "constants"

## Writing output

Used the following assembly code for `write(1, 1337000, 1)`


```asm

mov rax, 1
mov rdi, 1
mov rsi, 1337000
mov rdx, 1
syscall

````

prints a single character.

flag: `pwn.college{AZcIoCBhVfrXS-Z-21BiQVm2ujE.dBTN1YDL2MDO0czW}`


## Chaining syscalls

same as previous but this time to prevent the CPU from crashing we should also exit with the code `42`

```asm

mov rax, 1
mov rdi, 1
mov rsi, 1337000
mov rdx, 1
syscall

mov rax, 60
mov rdi, 42
syscall

````

flag: `pwn.college{Q-AMHib-v2fAqa1q1VFpFprRtOd.dFTN1YDL2MDO0czW}`

## Writing strings

easy again.

```asm

mov rax, 1
mov rdi, 1
mov rsi, 1337000
mov rdx, 14
syscall

mov rax, 60
mov rdi, 42
syscall

```

flag: `pwn.college{A9feE_SNrlqVxhKDxQrqMLVg4D0.QX3MTMwEDL2MDO0czW}`


## Reading Data

in this we read 8 bytes, write 8 bytes then exit with code `42`

read -> 0
write -> 1

```asm

mov rax, 0
mov rdi, 0
mov rsi, 1337000
mov rdx, 8
syscall

mov rax, 1
mov rdi, 1
mov rsi, 1337000
mov rdx, 8
syscall

mov rax, 60
mov rdi, 42
syscall

```
flag: `pwn.college{czpNtMOWgXg79xKClHg2oiONUqj.QX0ATN1EDL2MDO0czW}`
