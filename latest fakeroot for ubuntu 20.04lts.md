
---

# Issue 3: Installing the Latest fakeroot (Version 1.37.1.2) on Ubuntu 20.04 LTS

I needed to install `fakeroot` version greater than 1.28 (specifically the latest, 1.37.1.2) on Ubuntu 20.04 LTS, since the stock version available via apt was 1.24-1. Installing newer versions through `apt` led to dependency conflicts because they required `libc6 >= 2.34`, whereas Ubuntu 20.04 ships with `libc6 2.31`.

To resolve this, I built `fakeroot` 1.37.1.2 manually from the Debian source while keeping everything compatible with Ubuntu 20.04’s libc version.

## Prerequisites

First, I installed the necessary build dependencies:

```bash
sudo apt update
sudo apt install fakeroot build-essential debhelper libcap2-dev procps sharutils docbook-xml docbook-xsl xsltproc libxml2-utils po4a devscripts m4 texinfo perl
```

To satisfy the required debhelper compatibility level (13), I enabled the focal-backports repository and installed the backported `debhelper`:

```bash
sudo add-apt-repository 'deb http://archive.ubuntu.com/ubuntu focal-backports main restricted universe multiverse'
sudo apt update
sudo apt install debhelper/focal-backports
```

Since the build also required Autoconf 2.71 and Ubuntu 20.04 only includes version 2.69, I built Autoconf from source:

```bash
mkdir ~/autoconf-build && cd ~/autoconf-build
wget https://ftp.gnu.org/gnu/autoconf/autoconf-2.71.tar.xz
tar xf autoconf-2.71.tar.xz
cd autoconf-2.71
./configure --prefix=/usr
make
sudo make install
```

Then I confirmed the installation:

```bash
autoconf --version
```

This output confirmed version 2.71.

## Building fakeroot

With all dependencies ready, I downloaded and built fakeroot:

```bash
mkdir ~/fakeroot-build && cd ~/fakeroot-build
wget http://deb.debian.org/debian/pool/main/f/fakeroot/fakeroot_1.37.1.2-1.dsc
wget http://deb.debian.org/debian/pool/main/f/fakeroot/fakeroot_1.37.1.2.orig.tar.gz
wget http://deb.debian.org/debian/pool/main/f/fakeroot/fakeroot_1.37.1.2-1.debian.tar.xz
dpkg-source -x fakeroot_1.37.1.2-1.dsc
cd fakeroot-1.37.1.2
debuild -us -uc
```

This generated `.deb` files in the parent directory.

## Installing the Built Packages

I installed the `.deb` packages:

```bash
cd ..
sudo dpkg -i libfakeroot_1.37.1.2-1_amd64.deb fakeroot_1.37.1.2-1_amd64.deb
```

Then I confirmed the installation:

```bash
fakeroot --version
```

The output showed version 1.37.1.2.

## Summary

This method allowed me to use the latest `fakeroot` version on Ubuntu 20.04 LTS without needing to upgrade `libc6` or the entire OS. If you're facing similar version conflicts, building from Debian source while managing compatible toolchain versions is a clean and stable solution.

## Tags

`ubuntu-20.04` `fakeroot` `build-from-source` `libc6` `autoconf` `dpkg` `debian-source` `manual-install`
