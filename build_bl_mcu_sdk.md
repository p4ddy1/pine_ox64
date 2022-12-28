# Building the examples of the bl_mcu_sdk on macOS

## Prerequisites
* A working Xuantie GNU Toolchain ([Building the Toolchain on macOS and Apple Silicon](build_toolchain_macos.md))
* Git
* cmake

## Sources
* [bl_mcu_sdk](https://github.com/bouffalolab/bl_mcu_sdk)

## Install dependencies
`$ brew install cmake`

## Check out the sources

```
$ git clone https://github.com/bouffalolab/bl_mcu_sdk.git
$ cd bl_mcu_sdk
```

## Configure cmake

The SDK provides a cmake binary, but if you are using macOS this won't work.
Open the file `project.build` in the repository root and replace the CMAKE path with the path of the cmake binary installed
trough homebrew:

```
...

ifeq ($(OS),Windows_NT)
CMAKE = $(BL_SDK_BASE)/tools/cmake/bin/cmake.exe
else
CMAKE = /opt/homebrew/bin/cmake
endif

...
```

## Configure Board

As this is SDK comes straight from Boufallo the configuration for the board does not quite fit to the Ox64.
UART3 for example, which is used by the C906 core (D0), uses GPIO8 for TX. GPIO8 is not connected on the Ox64, 
so you can never see the output. You can change the GPIO in the file `board/bl808dk/board.c`. I used GPIO32.
See `board.patch`

`$ git apply board.patch`

For the future it would be best to create a separate configuration for the Ox64 and I think there will be more changes
necessary for everything to work. This is just what I found out the first time I tried to run the examples

## Build an example

I'm using the bl808_dualcore example to verify that the two cores are working

```
$ cd examples/bl808_dualcore
$ make
```

You will now find two binaries that you can flash to the chip in the following directories:

`helloworld_d0/build/build_out/helloworld_bl808_d0.bin`
`helloworld_m0/build/build_out/helloworld_bl808_m0.bin`

Flash them to the chip, and you should see different messages via UART on GPIO14 and GPIO32.

If you want to build another example which is not preconfigured for the BL808 you can do it like this:

```
$ cd examples/helloworld
$ make CHIP=bl808 BOARD=bl808dk CPU_ID=d0
$ make CHIP=bl808 BOARD=bl808dk CPU_ID=m0
```

As you may have guessed, `CPU_ID=d0` will create a binary for the C906 core and `CPU_ID=m0` will create a binary for the
E907 core.

See [Flashing using picoprobe](build_bl_mcu_sdk.md) on how to flash these binaries to the Ox64 using picoprobe