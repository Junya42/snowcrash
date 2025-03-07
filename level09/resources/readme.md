# SnowCrash

## Level09

```sh
level09@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag09 level09 7640 Mar  5  2016 level09
----r--r-- 1 flag09 level09   26 Mar  5  2016 token
```
We have a binary file 'level09' and a 'token' file

Let's check the token file and the executable
```sh
level09@SnowCrash:~$ cat token
f4kmm6p|=�p�n��DB�Du{��
```


```sh
level09@SnowCrash:~$ ./level09 
You need to provied only one arg.
```

```sh
level09@SnowCrash:~$ ./level09 $(cat token)
f5mpq;v�E���|���~����[��`������
```
It seems to produce non-sense, let's try to analyze the exe behaviour

```sh
level09@SnowCrash:~$ ./level09 a
a
level09@SnowCrash:~$ ./level09 ab
ac
level09@SnowCrash:~$ ./level09 abc
ace
level09@SnowCrash:~$ ./level09 aaaaa
abcde
```

It looks like the exe file is printing the argument and adding the current character index to it's value
The token value might have been hashed using this algorithm, let's reverse it

We first copy the token file in our host, in order to code something that will reverse it

```sh
junya in ~/projects/42/cybersec/snowcrash/level09 λ scp -P 4242 level09@localhost:/home/user/level09/token .
```

Here is the code
```c
#include <stdio.h>

int main(int ac, char **av) {

	int i = 0;
	while (av[1][i])
		printf("%c", av[1][i] - i++);
	printf("\n");
}
```

Let's compile it and run the algorithm on the token
```sh
junya in ~/projects/42/cybersec/snowcrash/level09 λ gcc decypher.c
junya in ~/projects/42/cybersec/snowcrash/level09 λ ./a.out $(cat token)
f3iji1ju5yuevaus41q1afiuq
```

```sh
level09@SnowCrash:~$ su flag09
Password: 
Don't forget to launch getflag !
flag09@SnowCrash:~$ getflag
Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z
```