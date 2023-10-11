# Building the Xuantie GNU Toolchain on macOS and Apple Silicon

I'm going to show you how to build the Xuantie GNU Toolchain which is required for building software for the Pine Ox64.
At the moment there are only prebuild toolchains available for windows and linux. 
It is possible to build a toolchain on a Mac with an Apple Silicon CPU but there are a few obstacles to overcome.
I used a MacBook Pro with a M1 Pro.
This could probably also be turned into a homebrew formula. Let's get started.

## Prerequisites
* Homebrew (https://brew.sh)
* XCode
* Git

## Sources
* [Xuantie GNU Toolchain](https://github.com/T-head-Semi/xuantie-gnu-toolchain)

## Install dependencies

### Xuantie GNU Toolchain
`$ brew install python3 gawk gnu-sed gmp mpfr libmpc isl zlib expat`

### GNU coreutils

The build requires some of the GNU coreutils and not the BSD equivalents that come with macOS.


`$ brew install coreutils`

### textinfo

The build require textinfo to build

`$ brew install texinfo`

## Check out the sources

```
$ git clone --recurse-submodules https://github.com/T-head-Semi/xuantie-gnu-toolchain.git
$ git clone https://github.com/bouffalolab/bl_mcu_sdk.git
```

## Patching newlib

Apply the following patch from this repo to the riscv-newlib repo. For some reason they used invalid relative paths
on an include.

```
$ cd xuantie-gnu-toolchain
$ cd riscv-newlib
$ wget https://raw.githubusercontent.com/p4ddy1/pine_ox64/main/riscv-newlib.patch
$ git apply riscv-newlib.patch
```

## Patching gcc

We need to make a change to gcc for a successful build (See this [issue](https://github.com/riscv-software-src/homebrew-riscv/issues/47))
```
$ cd ..
$ sed -i '' "s/.*=host-darwin.o$//" riscv-gcc/gcc/config.host
$ sed -i '' "s/.* x-darwin.$//" riscv-gcc/gcc/config.host
```

## Prepare the environment

Create a folder where the toolchain should be installed to

```
$ sudo mkdir /opt/riscv-toolchain
$ sudo chown -R $USER /opt/riscv-toolchain
```

Add the folder to your PATH variable. There will be another folder where the toolchain will be located which is not yet
created. You can also add this to your `~/.profile` if you want to keep the toolchain in your PATH.

`export PATH=$PATH:/opt/riscv-toolchain/xuantie/bin`

Add the GNU coreutils to your path, otherwise the build will fail

`export PATH=$(brew --prefix)/opt/coreutils/libexec/gnubin:$PATH`

## Start the build

Multilib is required, because the Ox64 also has 32-bit cores. GDB is optional.

```
$ ./configure --prefix=/opt/riscv-toolchain/xuantie --with-cmodel=medany --enable-multilib --enable-gdb
$ make newlib -j$(nproc)
```

The toolchain is now installed. Don't forget to keep `/opt/riscv-toolchain/xuantie/bin`
in your PATH if you want to start building a firmware.

See [Building the examples of the bl_mcu_sdk on macOS](build_bl_mcu_sdk.md) on how to build the examples
