# Computer Memory

## Loading from Memory

The challenge instructs us to retrieve a secret number stored at the memory adress `133700` and use it as the exit code for our program.


I used the following assembly code to achieve this:-


```asm

mov rdi, [133700]
mov rax, 60
syscall

```

got the flag on running `~$ /challenge/check rax-challenge.s`

flag: `pwn.college{IvcQERQSAWb90ZNMvAFgvUYChRy.dRjM5UDL2MDO0czW}`


## More Loading Practice

Exactly the same as the previous challenge, except for this time the secret value was in `123400`

flag: `pwn.college{0qZ0ftU7v_lvlp3KG83Pue3kjK0.dBzM5UDL2MDO0czW}`


## Dereferencing Pointers

This challenge educates about registers such as `rax` which are general purpose reigsters and only hold the memory addresses at where the actual data is stored.

We are instructed to dereference `rax` into `rdi` so we can retrieve the secret data `rax` is pointing to.

```asm

mov rdi, [rax]
mov rax, 60
syscall

```

flag: `pwn.college{E6v5nQCjGjnrxmg2NPcbQ3wLoQM.dFzM5UDL2MDO0czW}`


## Dereferencing yourself

Challenge instructs us to exit with the value which `rdi` points to. i.e. `rdi` is the pointer to that secret value

basically `rdi` will need to dereference the memory address it's pointing to and store it in itself.

```asm

mov rdi, [rdi]
mov rax, 60
syscall

```

flag: `pwn.college{4NVOj-Yno5cpO--yyi_fcyIXrH1.dJzM5UDL2MDO0czW}`


## Dereferencing with Offsets

Now the secret value is at an offset of 8 bytes to the memory address stored in `rdi` 


```asm

mov rdi, [rdi+8]
mov rax, 60
syscall

```

this retrieves the secret value by dereferencing the pointer to the memory address + 8.

flag: `pwn.college{MAhS0H6bU7TXyjYvv_NEtToacXa.dVDN5UDL2MDO0czW}` 


## Stored Addresses

We are instructed to find the memory address which stores our secret value, the memory address itself is stored at the address `567800` and we need to get that secret value and exit with that as the exit code.


```asm

mov rdi, [567800]
mov rdi, [rdi]
mov rax, 60
syscall

```

flag: `pwn.college{YCel60AflXRnDvTvmATtrRyDxYK.dNzM5UDL2MDO0czW}`


## Double Dereference

Similar to the previous one, this time our secret value is at some `SECRET_LOCATION1` and that address itself is stored in address `SECRET_LOCATION2` and the address of `SECRET_LOCATION_2` is stored in `rax` and that secret value is used as the exit code.


```asm

mov rdi, [rax] ; gets SECRET_LOCATION_1 NOT 2! for 2: mov rdi, rax
mov rdi, [rdi] ; gets SECRET_VALUE
mov rax, 60
syscall

```

flag: `pwn.college{AopurXGn-scE_5TeJs1AxNi0XsW.dRzM5UDL2MDO0czW}`


## Triple dereferencing

Same as the previous one but this time, we have one more layer and the first address to be dereferenced is stored in `rdi`


```asm

mov rdi, [rdi] ; gets SECRET_LOCATION_2 
mov rdi, [rdi] ; gets SECRET_LOCATION_1
mov rdi, [rdi] ; gets SECRET_VALUE     
mov rax, 60
syscall

```

flag: `pwn.college{wKD3Ut8Afa34zSdOEeZl35hqj4p.dNDN5UDL2MDO0czW}`
