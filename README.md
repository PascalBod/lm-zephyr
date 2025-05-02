# Overview

This tutorial describes a way to make a Linux virtual machine (VM) configured for developing applications built upon [Zephyr RTOS](https://www.zephyrproject.org/). The virtualization is done by [VirtualBox](https://www.virtualbox.org/).

The target board we'll use is the [Silicon Labs EFR32xG24 Dev Kit](https://www.silabs.com/development-tools/wireless/efr32xg24-dev-kit?tab=overview), based on the [EFR32MG24 wireless System on Chip (SoC)](https://www.silabs.com/wireless/zigbee/efr32mg24-series-2-socs). It could be replaced by any [board supported by Zephyr](https://docs.zephyrproject.org/latest/boards/index.html#).

Software versions will be:

* [Linux Mint](https://linuxmint.com/) Cinnamon 22.1 (based on Ubuntu 24.04 Noble)
* Zephyr 4.1.99
* Zephyr SDK 0.17.0

# Prerequisites

* A Linux Mint Cinnamon 22.1 VM. Create it according to [these instructions](https://github.com/PascalBod/lm-vm)
* The EFR32xG24 Dev Kit mentionned above

# References

* [Zephyr's Getting Started Guide](https://docs.zephyrproject.org/latest/develop/getting_started/index.html)
* Zephyr - Supported boards - [EFR32xG24 Dev Kit (xG24-DK2601B)](https://docs.zephyrproject.org/latest/boards/silabs/dev_kits/xg24_dk2601b/doc/index.html)

# VM configuration

Once the Linux Mint Cinnamon 22.1 VM is ready, configure it according to the instructions below.

With the Software Manager, install:

* CMake (version 3.28.3 at the time of writing)
* Device-tree-compiler (version 1.7.0 at the time of writing)
* Git (version 2.43.0 at the time of writing) (in addition to what the Zephyr's Getting Started Guide mentions)
* Ninja-build (version 1.11.1-2 at the time of writing) (in addition to what the Zephyr's Getting Started Guide mentions)

Then, [install some Python dependencies and get Zephyr](https://docs.zephyrproject.org/latest/develop/getting_started/index.html#get-zephyr-and-install-python-dependencies).

[Install the Zephyr SDK](https://docs.zephyrproject.org/latest/develop/getting_started/index.html#install-the-zephyr-sdk).

> [!NOTE]
> The `west sdk install` command failed with the following error message:
> 
> ```
> Installing 'xtensa-espressif_esp32_zephyr-elf' toolchain ...
> ERROR: Toolchain download failed
> FATAL ERROR: command "/home/developer/zephyr-sdk-0.17.0/setup.sh -t all -h" failed
> ```
>
> The reason was probably a temporary network problem. 
> I had to re-run `/home/developer/zephyr-sdk-0.17.0/setup.sh -t all -h` and then to install the udev rules, as
> documented in part 4 of [this section](https://docs.zephyrproject.org/latest/develop/toolchains/zephyr_sdk.html#zephyr-sdk-installation).

[Build the *blinky* sample](https://docs.zephyrproject.org/latest/develop/getting_started/index.html#build-the-blinky-sample). The board name is `xg24_dk2601b`. It can be found with the `west boards` command:

```
west boards -n xg24
```

Connect the EFR32xG24 Dev Kit. Check that the virtual machine can see it, with **Devices > USB**. A new USB device should be visible: **Silicon Labs J-Link OB**. Tick the associated checkbox.

You can assign the board to the virtual machine on a permanent basis with **Devices > USB > USB Settings...**

Install [J-Link Software and Documentation pack](https://www.segger.com/downloads/jlink/#J-LinkSoftwareAndDocumentationPack), using the 64-bit DEB Installer (version 8.28 at the time of writing).

> [!NOTE]
> The above step (installing J-Link) allows west to flash the EFR32xG24 Dev Kit. Another board could require another software.

> [!NOTE]
> It could be that the EFR32xG24 Dev Kit does not contain the latest version of J-Link firmware. This could prevent a successful flashing operation. In this case, the firmware should be updated using Simplicity Studio. This [repository](https://github.com/PascalBod/lm-efr32-simplicityStudio) explains how to install Simplicity Studio.

Flash the board:

```
west flash
```

The red LED should blink.

Open a new terminal window, and run Miniterm:

```
$ pyserial-miniterm /dev/ttyACM0 115200
```

Miniterm should display log messages:

```
...
LED state: OFF
LED state: ON
LED state: OFF
LED state: ON
...
```

> [!NOTE]
>
> To exit from Miniterm, use the following key combination: CTRL + ] (CTRL + ALT-GR + ) on an AZERTY keyboard).