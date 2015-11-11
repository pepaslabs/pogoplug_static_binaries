# pogoplug_static_binaries
Statically-linked binaries compiled for the stock busybox-based Linux distribution which comes with Pogoplug Mobile (also works with Pogoplug Series 4).

* from wget-1.12
  * [wget](https://github.com/pepaslabs/pogoplug_static_binaries/blob/master/bin/wget?raw=true) (with HTTPS support)

* from util-linux-2.17.2:
  * [fdisk](https://github.com/pepaslabs/pogoplug_static_binaries/blob/master/bin/fdisk?raw=true)
  * [sfdisk](https://github.com/pepaslabs/pogoplug_static_binaries/blob/master/bin/sfdisk?raw=true)
  * [mkswap](https://github.com/pepaslabs/pogoplug_static_binaries/blob/master/bin/mkswap?raw=true)
  * [ionice](https://github.com/pepaslabs/pogoplug_static_binaries/blob/master/bin/ionice?raw=true)

* from binutils-2.20.1
  * [ar](https://github.com/pepaslabs/pogoplug_static_binaries/blob/master/bin/ar?raw=true)

### Build procedure (general)

All of these binaries were built in a Debian squeeze chroot on a Pogoplug Mobile (booted into the stock Pogoplug linux installation).

In general, the build procedure is:

```
apt-get source foo
apt-get build-dep foo
cd foo-0.1
./configure LDFLAGS='-static'
make
```

### Build procedure (wget)

`wget` had to be built using the following steps.

```
apt-get build-deb wget
apt-get source wget
cd wget
```

```
./configure LDFLAGS="-static" --with-ssl
make
```

The above `make` command will fail.  I had to do this instead (thanks to http://stackoverflow.com/a/9833936):

```
env CPPFLAGS="-I/usr/include" LDFLAGS="-L/usr/lib/ssl" ./configure --with-ssl=openssl
make CPPFLAGS="-I/usr/include" LDFLAGS="-L/usr/lib -L/usr/lib/ssl -static"
```

However, that still fails to link, with a bunch of errors like this:

```
(.text+0x9b0): undefined reference to `inflateInit_'
/usr/lib/libcrypto.a(c_zlib.o): In function `zlib_stateful_init':
(.text+0x9fc): undefined reference to `deflateInit_'
collect2: ld returned 1 exit status
```

I had to repeat the the last `gcc` call manually with the addition of an extra `-lz` at the end of the command line:

```
cd src
gcc -O2 -Wall -L/usr/lib -L/usr/lib/ssl -lz -static -o wget cmpt.o connect.o convert.o cookies.o ftp.o css.o css-url.o ftp-basic.o ftp-ls.o hash.o host.o html-parse.o html-url.o http.o init.o log.o main.o netrc.o progress.o ptimer.o recur.o res.o retr.o snprintf.o spider.o url.o utils.o exits.o build_info.o  version.o ftp-opie.o openssl.o http-ntlm.o gen-md5.o ../lib/libgnu.a  -lssl -lcrypto -ldl -lrt -lz
```

You should now have a working wget static binary (with HTTPS support).
