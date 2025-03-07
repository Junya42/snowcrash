PASSWORD=f2av5il02puano7naaf6adaaf

getpass:
	@echo password is ${PASSWORD}
	scp -P 4242 level02@localhost:/home/user/level02/level02.pcap .

kali:
	docker run --rm -v .:/data -it kalilinux/kali-rolling /bin/bash
