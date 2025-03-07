# SnowCrash

## Level02

ls

level02.pcap

pcap = format for storing packed captures

tcpick = command to analyse pcap files in human readable format

apt update

apt install tcpick -C -yU -r level02.pcap

Password:
f
t
_
w
a
n
d
r
<7f>
<7f>
<7f>
N
D
R
e
l
<7f>
L
0
L

<00>
<01>
<00>

<7f> is hex value for DEL so password is actually:
ft_waNDReL0L

level02@SnowCrash:~$ su flag02
Password: ft_waNDReL0L
Don't forget to launch getflag !
flag02@SnowCrash:~$ getflag
Check flag.Here is your token : kooda2puivaav1idi4f57q8iq