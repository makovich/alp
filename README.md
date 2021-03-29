# alp

This is a fancy [QEMU](https://www.qemu.org/) wrapper for [Alpine Linux](https://alpinelinux.org/) that let you spin a virtual machine in few seconds.

## Requirements

* Bash
* QEMU
* Alpine Linux virtual edition (https://alpinelinux.org/downloads/)

## Usage

To install, just download [`alp`](https://raw.githubusercontent.com/makovich/alp/master/alp):
```sh
$ wget -O /usr/local/sbin/alp https://raw.githubusercontent.com/makovich/alp/master/alp
$ chmod +x /usr/local/sbin/alp
$ cd
$ mkdir alpine-img
$ cd alpine-img
$ wget https://dl-cdn.alpinelinux.org/alpine/v3.13/releases/x86_64/alpine-virt-3.13.3-x86_64.iso
$ export ALPINE_ISO_DIR=$HOME/alpine-img
$ alp
# ...
Welcome to Alpine Linux 3.13
Kernel 5.10.16-0-virt on an x86_64 (/dev/ttyS0)

localhost login:
```

Alternatively, use `ALPINE_ISO` environment variable to point out image:
```sh
$ export ALPINE_ISO="$HOME/Downloads/alpine-virt-3.13.2-x86_64.iso"
$ alp
```

By default, `alp` mounts current directory as a USB drive, so you can put [`apkovl`](https://wiki.alpinelinux.org/wiki/Alpine_local_backup) file there and it'll be picked up at boot:
```sh
$ ls
vm.apkovl.tar.gz
$ alp
# ...
Welcome to Alpine Linux 3.13
Kernel 5.10.16-0-virt on an x86_64 (/dev/ttyS0)

vm login: root
Welcome to Alpine!
# ...
user@vm $ mount /media/usb
user@vm $ ls /media/usb
vm.apkovl.tar.gz
user@vm $ echo "From VM to host with love" > /media/usb/hello.txt
user@vm $ ls /media/usb
hello.txt
vm.apkovl.tar.gz
user@vm $ umount /media/usb
# Ctrl+a x
QEMU: Terminated
$ ls
hello.txt
vm.apkovl.tar.gz
```

It is possible to use apkovl's from `sys` mode (i.e. [System Disk Mode](https://wiki.alpinelinux.org/wiki/Installation#Installation_Overview)). It will be repacked to `./alp/alp.apkovl.tar.gz` and run from `.alp`. Same rule applies to [`bkp`](https://github.com/makovich/bkp) directory (or any directory containing `etc/apk` file in it).

```sh
$ scp user@myhost:/path/to/myhost.apkovl.tar.gz .
$ OVL=. alp
# or
$ OVL=$HOME/backups/host-abc.apkovl.tar.gz alp
```

Repack basically installs `etc/init.d/apkovl` service which:
* apply `perms.bkp` for apkovl files (if found, see [`bkp`](https://github.com/makovich/bkp))
* make `root` login passwordless
* setup VM's network interfaces
* allow `root` to ssh into VM
* bind apk cache directory host's $PWD/.alp/cache
* install all `etc/apk/world`

## License

MIT/Unlicense
