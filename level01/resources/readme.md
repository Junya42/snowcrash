# SnowCrash

## Level01

Remember level00 we tried cat /etc/passwd and we got this result

```
level00@SnowCrash:~$ cat /etc/passwd
...
...
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
...
```

username:password:UID:GID:.....

It looks like we find our password or at least an hashed version of it.
We'll need to crack it.

The best way to do it is to use 'John The Ripper' which is a famous tool for cracking passwords

First let's extract the file /etc/passwd from the VM

```
scp -P 4242 level01@localhost:/etc/passwd .
```

We'll then need to run a docker image of linux to have the rights to install JTR
Kali is the best choice because we'll only need to install JTR without it's dependencies

```
docker run --rm -v .:/data -it kalilinux/kali-rolling /bin/bash
```

This command will run a kali docker and create a volume between ${pwd} and /data to be able to access our 'passwd' file

Let's install JTR and run it

```
┌──(root㉿630f85230c9d)-[/]
└─# apt-get update && apt-get install john -y

┌──(root㉿630f85230c9d)-[/]
└─# john /data/passwd
Created directory: /root/.john
Using default input encoding: UTF-8
Loaded 1 password hash (descrypt, traditional crypt(3) [DES 256/256 AVX2])
Will run 24 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Warning: Only 642 candidates buffered for the current salt, minimum 6144 needed for performance.
Proceeding with wordlist:/usr/share/john/password.lst
```
:memo: <span style="color:red">abcdefg          (flag01)</span>
```
1g 0:00:00:00 DONE 2/3 (2025-02-19 19:04) 10.00g/s 1405Kp/s 1405Kc/s 1405KC/s 123456..Sssing
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
...
```

Let's try the password

```
level01@SnowCrash:~$ su flag01
Password: abcdefg
Don't forget to launch getflag !
flag01@SnowCrash:~$ getflag
Check flag.Here is your token : f2av5il02puano7naaf6adaaf
```