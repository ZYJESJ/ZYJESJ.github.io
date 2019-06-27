RISC-V GNU Compiler Toolchain
=============================

[![Build Status](https://travis-ci.org/riscv/riscv-gnu-toolchain.svg?branch=master)](https://travis-ci.org/riscv/riscv-gnu-toolchain)

This is the RISC-V C and C++ cross-compiler. It supports two build modes:
a generic ELF/Newlib toolchain and a more sophisticated Linux-ELF/glibc
toolchain.

###  Getting the sources

This repository uses submodules. You need the --recursive option to fetch the submodules automatically

    $ git clone --recursive https://github.com/riscv/riscv-gnu-toolchain
    
Alternatively :

    $ git clone https://github.com/riscv/riscv-gnu-toolchain
    $ cd riscv-gnu-toolchain
    $ git submodule update --init --recursive
    
    

### Prerequisites

在Ubuntu上配置k8s环境，先要安装docker,官方提供了安装脚本，在root模式下一条命令即可完成安装。

    $ curl -s https://get.docker.com/ | sudo sh

然后安装kubernetes，由于kubernetes部件所需的官方镜像在 http://gcr.io(Google Cloud Container Registry)上，该网站被墙了。先要用国内的一个镜像来替换掉官方景象就可以了。以Ubuntu为例，替换方法如下：

    $ cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    $ deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
    $ EOF
替换完整后执行下面两条命令：
    $ apt-get update
    $ apt-get install -y kubelet kubeadm kubectl
    $ EOF
On OS X, you can use [Homebrew](http://brew.sh) to install the dependencies:

    $ brew install gawk gnu-sed gmp mpfr libmpc isl zlib expat

To build the glibc (Linux) on OS X, you will need to build within a case-sensitive file
system.  The simplest approach is to create and mount a new disk image with
a case sensitive format.  Make sure that the mount point does not contain spaces. This is not necessary to build newlib or gcc itself on OS X.

This process will start by downloading about 200 MiB of upstream sources, then
will patch, build, and install the toolchain.  If a local cache of the
upstream sources exists in $(DISTDIR), it will be used; the default location
is /var/cache/distfiles.  Your computer will need about 8 GiB of disk space to
complete the process.

### Installation (Newlib)

To build the Newlib cross-compiler, pick an install path.  If you choose,
say, `/opt/riscv`, then add `/opt/riscv/bin` to your `PATH` now.  Then, simply
run the following command:

    ./configure --prefix=/opt/riscv
    make

You should now be able to use riscv64-unknown-elf-gcc and its cousins.

### Installation (Linux)

To build the Linux cross-compiler, pick an install path.  If you choose,
say, `/opt/riscv`, then add `/opt/riscv/bin` to your `PATH` now.  Then, simply
run the following command:

    ./configure --prefix=/opt/riscv
    make linux

The build defaults to targetting RV64GC (64-bit), even on a 32-bit build
environment.  To build the 32-bit RV32GC toolchain, use:

    ./configure --prefix=/opt/riscv --with-arch=rv32gc --with-abi=ilp32d
    make linux

Supported architectures are rv32i or rv64i plus standard extensions (a)tomics,
(m)ultiplication and division, (f)loat, (d)ouble, or (g)eneral for MAFD.

Supported ABIs are ilp32 (32-bit soft-float), ilp32d (32-bit hard-float),
ilp32f (32-bit with single-precision in registers and double in memory, niche
use only), lp64 lp64f lp64d (same but with 64-bit long and pointers).

### Installation (Linux multilib)

To build the Linux cross-compiler with support for both 32-bit and
64-bit, run the following commands:

    ./configure --prefix=/opt/riscv --enable-multilib
    make linux

The multilib compiler will have the prefix riscv64-unknown-linux-gnu-,
but will be able to target both 32-bit and 64-bit systems.

### Advanced Options

There are a number of additional options that may be passed to
configure.  See './configure --help' for more details.

### Test Suite

The DejaGnu test suite has been ported to RISC-V.  This can run with GDB
simulator for elf toolchain or Qemu for linux toolchain, and GDB simulator
doesn't support floating-point.
To test GCC, run the following commands:

    ./configure --prefix=$RISCV --disable-linux --with-arch=rv64ima # or --with-arch=rv32ima
    make newlib
    make report-newlib

    ./configure --prefix=$RISCV
    make linux
    make report-linux
