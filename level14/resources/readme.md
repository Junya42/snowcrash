# SnowCrash

## Level14

```sh
level14@SnowCrash:~$ ls -l
total 0

level14@SnowCrash:~$ find / -user level14 2> /dev/null | grep -v /proc

level14@SnowCrash:~$ find / -user flag14 2> /dev/null | grep -v /proc
```

Nothing is available to us here, we'll try to use the level13 method on the getflag program directly

level14@SnowCrash:~$ id flag14
uid=3014(flag14) gid=3014(flag14) groups=3014(flag14),1001(flag)

level14@SnowCrash:~$ which getflag
/bin/getflag

```sh
level14@SnowCrash:~$ gdb /bin/getflag

(gdb) disas main
Dump of assembler code for function main:
	...
   0x08048989 <+67>:    call   0x8048540 <ptrace@plt> # Usually used to check if the program is being run using a debugger
   0x0804898e <+72>:    test   %eax,%eax # check the return value of ptrace (-1 if GDB is detected)
   0x08048990 <+74>:    jns    0x80489a8 <main+98> #jns = jump if not signed which is not the case when $eax=-1
   #The jump will not occur here
   0x08048992 <+76>:    movl   $0x8048fa8,(%esp) # pushing the error message onto the stack ("You should not reverse this")
   0x08048999 <+83>:    call   0x80484e0 <puts@plt> # priting the error message
   0x0804899e <+88>:    mov    $0x1,%eax
   0x080489a3 <+93>:    jmp    0x8048eb2 <main+1388> # jumping to END of the program
	...
	...
   0x08048afd <+439>:   call   0x80484b0 <getuid@plt> # Retrieve the real user id
   0x08048b02 <+444>:   mov    %eax,0x18(%esp) # store the return value of getuid into the stack
   0x08048b06 <+448>:   mov    0x18(%esp),%eax # put it back in %eax
	...
	...
   0x08048eb2 <+1388>:  mov    0x11c(%esp),%edx # END
   0x08048eb9 <+1395>:  xor    %gs:0x14,%edx
   0x08048ec0 <+1402>:  je     0x8048ec7 <main+1409>
   0x08048ec2 <+1404>:  call   0x80484a0 <__stack_chk_fail@plt>
   0x08048ec7 <+1409>:  mov    -0x4(%ebp),%ebx
   0x08048eca <+1412>:  leave
   0x08048ecb <+1413>:  ret
End of assembler dump.
```

Since getuid is used, we need to know the real user id of user flag14
```sh
level14@SnowCrash:~$ id flag14
uid=3014(flag14) gid=3014(flag14) groups=3014(flag14),1001(flag)
```

Let's first get rid of ptrace by changing it's return value
We put a breakpoint to the test condition at 0x0804898e

```sh
(gdb) b *0x0804898e
Breakpoint 1 at 0x804898e
(gdb) run
Starting program: /bin/getflag 

Breakpoint 1, 0x0804898e in main ()
(gdb) set $eax=0
# At this point we effectively bypassed ptrace
# Now we need to take care of getuid
# Let's create a new breakpoint after the call to getuid where we mov %eax into 0x18(%esp) at 0x08048b02
(gdb) b *0x08048b02
Breakpoint 2 at 0x8048b02
(gdb) continue
Continuing.

Breakpoint 2, 0x08048b02 in main ()
(gdb) set $eax=3014
(gdb) continue
Continuing.
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
```

```sh
level14@SnowCrash:~$ su flag14
Password: 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
Congratulation. Type getflag to get the key and send it to me the owner of this livecd :)
flag14@SnowCrash:~$ getflag
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
```