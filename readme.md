 <a href="https://www.42.fr/">
    <p><img src="https://www.universfreebox.com/UserFiles/image/site_logo.gif" alt="42 logo" title="42" align="right" /></p>
</a>

<p align="center">
    <img src="https://img.shields.io/badge/Skill%201-Security-9cf">
    <img src="https://img.shields.io/badge/Skill%202-Unix-blue">
    <img src="https://img.shields.io/badge/Objectives-Perl/Python/Shell%20scripts-brightgreen">
</p>

<br/>

This project introduces key computer security concepts by solving practical challenges. **SnowCrash** guides you through various security scenarios using real-world examples, enhancing your understanding of common vulnerabilities found in programming languages like ASM, Perl, and PHP. You'll sharpen your logic and analytical skills, essential for identifying and preventing security breaches.

Each level (14 total) features a unique security vulnerability that you must exploit to progress to the next level.

<br/>

## General instructions

You will use a VM (64-bit) provided with an ISO file. Booting the VM correctly will show a prompt similar to:

![alt tag](https://user-images.githubusercontent.com/34480775/100728223-923e5880-33c7-11eb-8188-e360404180bf.JPG)

Log in with:
```
level00:level00
```
Connect via SSH on port `4242`:
```
ssh level00@localhost -p 4242
```

To move forward, discover the password for each "flagXX" account, corresponding to each level number. Once logged into a "flagXX" account, use:
```
getflag
```

This will provide the password needed to access the next level. If direct access isn't possible, alternative techniques such as command injection may be required.

Example session:

![alt tag](https://user-images.githubusercontent.com/34480775/100729210-c403ef00-33c8-11eb-95d5-ff44954aa1d1.JPG)

Use external tools (such as SCP) when necessary.

## Project Structure

The project is divided into:
- **Mandatory (levels 00–09)**
- **Bonus (levels 10–14)** *(for additional credit beyond mandatory completion)*

Each level folder structure:

![image](https://github.com/user-attachments/assets/554ee2f6-97d6-4747-90cf-3b53cb036b8d)

- **flag** *(Solution token or explanation)*
- **resources** *(Scripts, documentation, and explanations)*
  - `readme.md`: Detailed solution guide

## Setup Requirements

Instead of using VirtualBox, the VM for this project was configured using a convenient Makefile leveraging **QEMU**:

```makefile
# Fetch the current directory name
# In this project it should be "snowcrash"
CURRENT_DIR_NAME := $(shell basename $(shell pwd))

# Find the first iso file in the directory
ISO ?= $(shell ls *.iso 2>/dev/null | head -n 1)

# URL to download the iso if not available in current dir
ISO_URL := https://cdn.intra.42.fr/isos/$(CURRENT_DIR_NAME).iso

.PHONY: run
run:
	@if [ -z "$(ISO)" ]; then \
		echo "No ISO found in the directory."; \
		echo "Downloading from $(ISO_URL)..."; \
		curl -f -o "$(CURRENT_DIR_NAME).iso" "$(ISO_URL)" || { \
			echo "The ISO file couldn't be downloaded"; \
			exit 1; \
		}; \
		echo "Downloaded successfully."; \
		ISO="$(CURRENT_DIR_NAME).iso"; \
	else \
		echo "Using iso : $(ISO)"; \
		ISO="$(ISO)"; \
	fi; \
	if [ ! -f "$$ISO" ]; then \
		echo "ISO file '$$ISO' not found."; \
		exit 1; \
	fi; \
	echo "Starting $$ISO using QEMU"; \
	sudo qemu-system-x86_64 -nic user,hostfwd=tcp::4242-:4242 -enable-kvm -m 2G -boot d -cdrom "$$ISO" -vga virtio
```

Ensure `qemu-system-x86_64` and `curl` are installed on your system.

Enjoy and have fun securing your software!