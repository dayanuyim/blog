---
title: linux-kernel-compile
date: 2024-05-21 10:21:34
categories: linux
tags:
  - linux
  - kernel
---

## toolchains

Install the toolchain, including `gcc` and `binutils`.

for cross-compile, be aware of the name convention

>  XXX-arch[-vendor][-os]-abi
>  `vendor` and `os` can be omitted or marked as `-none`

```bash
sudo apt install gcc binutils
sudo apt install gcc-arm-linux-gnueabihf binutils-arm-linuxgnueabihf  # arm 32
sudo apt install make gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu  # arm 64
sudo apt install gcc-8-aarch64-linux-gnu  # specify gcc version
```

## Download linux kernel

```bash
# clone only the specified version
git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git --depth 1 --branch v6.6

# clone all and checkout to the specified version
git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
cd linux
git checkout v6.6
```

## Compile

- 設定環境變數

```bash
export CROSS_COMPILE=aarch64-linux-gnu
export ARCH=aarch64
```

- clean

```bash
make distclean
```

- 套用config

  可從defconfig載入，或由running系統中取得

```bash
# 1. from arch/<arch>/configs
make <xxx_defconfig>

# 2. from the running system
cp /boot/config-`uname -r` .config

# 3. from the running system, if IKCONFIG_PROC is enabled
zcat /proc/config.gz > .config
```

# set customed parameters
```bash
make menuconfig  # ncurses-based
make xconfig     # X-based
```

```bash
# compile
make -j<n>    # n is usually set to 1.5x ~ 2.0x
# or
make zImage
make dtbs
make modules
```

## Installation

其中`INSTALL_MOD_PATH`為輸出modules所需要，一般會指到準備porting的fsroot

```bash
# install kernel
make install

# install modules
export INSTALL_MOD_PATH=<dir>
make modules_install
```

- /boot/vmlinuz
- /boot/System.map
  - /proc/kallsyms consists of /proc/System.map and loaded kernel modules.
- /boot/config.map
- /lib/modules/$(uname -r)/kernel/


## Configurations

- **.config** 目前版本
- **.config.old** 之前版本，`make <defconfig>`後產生
- **defconfig** 同.config，但不包含預設值，由`make savedefconfig`產生。