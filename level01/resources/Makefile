PASSWORD=x24ti5gi3x0ol2eh4esiuxias

getpass:
	@echo password is ${PASSWORD}
	scp -P 4242 level01@localhost:/etc/passwd .

kali:
	docker run --rm -v .:/data -it kalilinux/kali-rolling /bin/bash