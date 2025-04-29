# Overview

# Prerequisites

# References

* [Zephyr's Getting Started Guide](https://docs.zephyrproject.org/latest/develop/getting_started/index.html)

# Installation

With the Software Manager, install:

* CMake (version 3.28.3 at the time of writing)
* Device-tree-compiler (version 1.7.0 at the time of writing)
* Git (version 2.43.0 at the time of writing)
* Ninja-build (version 1.11.1-2 at the time of writing)

Then, [install some Python dependencies and get Zephyr](https://docs.zephyrproject.org/latest/develop/getting_started/index.html#get-zephyr-and-install-python-dependencies).

[Install the Zephyr SDK](https://docs.zephyrproject.org/latest/develop/getting_started/index.html#install-the-zephyr-sdk).

> [!NOTE]
> The `west sdk install` command fails with the following error message:
> 
> ```
> Installing 'xtensa-espressif_esp32_zephyr-elf' toolchain ...
> ERROR: Toolchain download failed
> FATAL ERROR: command "/home/developer/zephyr-sdk-0.17.0/setup.sh -t all -h" failed
> ```

I had to re-run `/home/developer/zephyr-sdk-0.17.0/setup.sh -t all -h` and then toinstall the udev rules, as documented in part 4 of [this section](https://docs.zephyrproject.org/latest/develop/toolchains/zephyr_sdk.html#zephyr-sdk-installation).

[Build the *blinky* sample](https://docs.zephyrproject.org/latest/develop/getting_started/index.html#build-the-blinky-sample).

Connect the EFR32xG24 Dev Kit (xG24-DK2601B). Configure the virtual machine in order to capture the related USB port.

Install [J-Link Software and Documentation pack](https://www.segger.com/downloads/jlink/#J-LinkSoftwareAndDocumentationPack), using the 64-bit DEB Installer in our case (version 8.28 at the time of writing).

Flash the board:

```
$ west flash
```