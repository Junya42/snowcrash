# SnowCrash

## Level00

```
level00@SnowCrash:~$ ls -la
total 12
dr-xr-x---+ 1 level00 level00  100 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-xr-x---+ 1 level00 level00  220 Apr  3  2012 .bash_logout
-r-xr-x---+ 1 level00 level00 3518 Aug 30  2015 .bashrc
-r-xr-x---+ 1 level00 level00  675 Apr  3  2012 .profile
```

Nothing exploitable here, let's try to check basic password related files
```
level00@SnowCrash:~$ cat /etc/passwd
...
...
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
...
```
Nothing about the user flag00, but let's remember about the flag01 for the next level

```
level00@SnowCrash:~$ cat /etc/shadow
cat: /etc/shadow: Permission denied
```

No permission on /etc/shadow let's try to other methods
```
grep -rnw "password" / 2>/dev/null
```

```
grep -rnw "flag" / 2>/dev/null
```

```
find / -user flag00 2>/dev/null

/usr/sbin/john
/rofs/usr/sbin/john
```

```
cat /usr/sbin/john
cdiiddwpgswtgt
```

```
su flag00
password: cdiiddwpgswtgt

su: Authentication failure
```

Let's analyze the string "cdiiddwpgswtgt".
- Contains only lower-case letters (no digits, punctuation or special symbols).
- Has a somewhat random look but sticks to a-z
- Contains repeating letter sequences which suggest a cipher (Other encryption method (AES, RSA, ...) should look completly random)
- It doesn't check basic encoding such as Base64, Hex etc...

Let's find out if it's using the caesar cipher encryption.
https://www.dcode.fr/caesar-cipher

This website allows you to bruteforce the decryption by trying all the possible number keys.

```
↑↓	↑↓
🠞15 (🠜11)	nottoohardhere
🠞14 (🠜12)	opuuppibseifsf
🠞21 (🠜5)	hinniibulxbyly
🠞2 (🠜24)	abggbbunequrer
🠞11 (🠜15)	rsxxsslevhlivi
🠞4 (🠜22)	yzeezzslcospcp
🠞22 (🠜4)	ghmmhhatkwaxkx
🠞24 (🠜2)	efkkffyriuyviv
🠞5 (🠜21)	xyddyyrkbnrobo
🠞8 (🠜18)	uvaavvohykolyl
🠞10 (🠜16)	styyttmfwimjwj
🠞1 (🠜25)	bchhccvofrvsfs
🠞3 (🠜23)	zaffaatmdptqdq
🠞17 (🠜9)	lmrrmmfypbfcpc
🠞25 (🠜1)	dejjeexqhtxuhu
🠞18 (🠜8)	klqqllexoaebob
🠞12 (🠜14)	qrwwrrkdugkhuh
🠞19 (🠜7)	jkppkkdwnzdana
🠞20 (🠜6)	ijoojjcvmyczmz
🠞9 (🠜17)	tuzzuungxjnkxk
🠞7 (🠜19)	vwbbwwpizlpmzm
🠞16 (🠜10)	mnssnngzqcgdqd
🠞23 (🠜3)	fgllggzsjvzwjw
🠞6 (🠜20)	wxccxxqjamqnan
🠞13 (🠜13)	pqvvqqjctfjgtg
#25
```

The key 15 correspond to an human readable string, let's try "nottoohardhere" as the password

```
su flag00
password: nottoohardhere
Don't forget to launch getflag !
Check flag.Here is your token : x24ti5gi3x0ol2eh4esiuxias
```