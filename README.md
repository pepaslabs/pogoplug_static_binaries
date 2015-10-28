# pogoplug_static_binaries
Statically-linked binaries compiled for the stock busybox-based Linux distribution which comes with Pogoplug Mobile (also works with Pogoplug Series 4).

* from wget-1.12
  * wget (with HTTPS support)

* from util-linux-2.17.2:
  * fdisk
  * sfdisk
  * mkswap
  * ionice

* from binutils-2.20.1
  * ar

All of these binaries were built in a Debian squeeze chroot on a Pogoplug Mobile (booted into the stock Pogoplug linux installation).

In general, the build procedure is:

```
apt-get source foo
apt-get build-dep foo
cd foo-0.1
./configure LDFLAGS='-static'
make
```
