# Your first Program


## Your first register

The challenge simply instructs us to write a simple assembly instruction to a file called `rax-challenge.s` which moves the immediate value `60` to the general-purpose register `rax`

I ran:-

```bash
~$ touch rax-challenge.s; echo "mov rax, 60" > rax-challenge.s
~$ /challenge/check rax-challenge.s

```

and got the flag.

flag: `pwn.college{k0UvP9LZHIEnlDI1yXT4J-IHSQy.dNDN4UDL2MDO0czW}`


## Your first syscall

The challenge explained syscalls to us, which are calls made by a program to interact with the OS. There are many types of syscalls and we were told about `60` which represents the `exit` syscall

The challenge instructs us to write instructions which move `60` to `rax` signifying the syscall number of `exit` and then invoke a `syscal1` instruction

I did this by:-

```bash
~$ echo "mov rax, 60" > rax-challenge.s
~$ echo "syscall" >> rax-challenge.s
~$ /challenge/check rax-challenge.s

```

flag: `pwn.college{E_ueYsV-UpdIlqoDQrkLJ8luSkh.dhjN4UDL2MDO0czW}`


## Exit codes


Every program after running exits with an exit code, this challenge tells us that we can set the exit code for a program by moving the value of the exit code to the `rdi` register (42 is the exit code for this chall).

I did this by:- 


```bash
~$ echo "mov rdi, 42" > rax-challenge.s
~$ echo "mov rax, 60" >> rax-challenge.s
~$ echo "syscall" >> rax-challenge.s
~$ /challenge/check rax-challenge.s

```

flag: `pwn.college{A67l3K7M8SihWvLk0uNxIY42LgV.dBzN4UDL2MDO0czW}`	


## Building executables

This challenge instructs us to build our own `.s` file into an executable. First I opened and edited my `rax-challenge.s` program to be:-


```asm
.intel_syntax noprefix
.global _start
_start:

mov rdi, 42
mov rax, 60
syscall
```

the first line tells the assembler we are using intel syntax.
the second and third lines denote the entrypoint for the code when it's being executed.

then we make an executable using:-


```bash
~$ as -o rax-challenge.o rax-challenge.s
~$ ld -o exe rax-challenge.o
~$ ./exe

```

the first `as -o` command assembles the file into the required binary code but it needs to be linked into a single executable (usually multiple object files are linked to form a single executable) by the `ld -o` command

on running `~$ ./exe` we get the flag:-

flag: `pwn.college{cp7fB-9axgsWdM5WQRxxG5lSEL2.QXwcjMwEDL2MDO0czW}`


## Tracing syscalls

This chall tells us about `strace` which reports system calls and tells us what syscalls were made and executed and if they were successful or not.

I proceed with the challenge instructions and run then get:-


```
~$ strace /challenge/trace-me

execve("/challenge/trace-me", ["/challenge/trace-me"], 0x7ffd34100670 /* 15 vars */) = 0
alarm(28143)                            = 0
exit(0)                                 = ?
+++ exited with 0 +++
```

and I can see that `28143` is the parameter which was passed to the `alarm` system call. This is the number I have to submit.

I run: `~$ /challenge/submit-number 28143` and get the flag:-

flag: `pwn.college{kD9Q6B34fAqr1ENhn6RquR9wZEq.dFzN4UDL2MDO0czW}`


## Moving between registers

We are told of another register called `rsi` where we can park data. Now a secret value is stored in the `rsi` register which we have to use as the exit code of our program. Now we are told that the `exit` syscall can only use the `rdi` register as a parameter so we will need to figure out a way to use that `rsi` register value in place of `rdi` register value.

I edit the `rax-challenge.s` file to:-

```asm
mov rdi, rsi
mov rax, 60
syscall

```

and I get the flag on running: `~$ /challenge/check rax-challenge.s`

flag: `pwn.college{gxOrx-aAztWpN8t_v6731aIrkti.dlDN1YDL2MDO0czW}`	


NOTE: On researching on my own I found, when talking about syscalls the `rdi` register usually stores the first parameter to a syscall and `rsi` stores the second parameter to a syscall.
