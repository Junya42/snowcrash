# SnowCrash

## Level13

```sh
level13@SnowCrash:~$ ls -l
total 8
-rwsr-sr-x 1 flag13 level13 7303 Aug 30  2015 level13
```

```sh
level13@SnowCrash:~$ ./level13 
UID 2013 started us but we we expect 4242
```

```sh
(gdb) disas main
Dump of assembler code for function main:
	...
   0x08048595 <+9>:     call   0x8048380 <getuid@plt>
   0x0804859a <+14>:    cmp    $0x1092,%eax
   0x0804859f <+19>:    je     0x80485cb <main+63>
	...
End of assembler dump.
```

Let's bypass the cmp instruction

We need to create a breakpoint where the cmp instruction occurs
We then need to change the return value of getuid by changing the value of $eax

Since the error told us that it expect 4242, lets set it to 4242
```sh
(gdb) b *0x804859a
Breakpoint 1 at 0x804859a
(gdb) run
Starting program: /home/user/level13/level13 

Breakpoint 1, 0x0804859a in main ()
(gdb) set $eax=4242
(gdb) continue
Continuing.
your token is 2A31L79asukciNyi8uppkEuSx
```