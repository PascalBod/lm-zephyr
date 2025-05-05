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
* Software development comptencies:
  * Basic knowledge of Git - [Git user manual](https://git-scm.com/docs/user-manual)
  * Basic knowledge of GitHub - [About GitHub and Git](https://docs.github.com/en/get-started/start-your-journey/about-github-and-git)
  * Basic knowledge of Linux (knowing the most common commands...) - [An Introduction to Linux Basics](https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-basics), from DigitalOcean
  * Basic knowledge of VirtualBox (knowing how to create a virtual machine...) - [VirtualBox end-user documentation](https://www.virtualbox.org/wiki/End-user_documentation)

# References

* [Zephyr's Getting Started Guide](https://docs.zephyrproject.org/latest/develop/getting_started/index.html)
* Zephyr - Supported boards - [EFR32xG24 Dev Kit (xG24-DK2601B)](https://docs.zephyrproject.org/latest/boards/silabs/dev_kits/xg24_dk2601b/doc/index.html)
* [How to Configure VS Code for Zephyr Development](https://blog.golioth.io/how-to-configure-vs-code-for-zephyr-development/) - Jonathan Beri

# VM configuration

Once the Linux Mint Cinnamon 22.1 VM is ready, configure it according to the instructions below.

## Zephyr development environment

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
$ west boards -n xg24
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
$ west flash
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

## Integrated Development Environment (IDE)

### VS Code installation

The IDE we will use is [Visual Studio Code](https://code.visualstudio.com/) (VS Code), from Microsoft.

Download the [Ubuntu package](https://code.visualstudio.com/Download) and install it.

### VS Code configuration

We will use the configuration produced by Jonathan Beri, and made available in [this GitHub repository](https://github.com/beriberikix/zephyr-vscode-example). The configuration will be done for the *thread* sample project.

Clone the repository. Then, adhere to the following steps (slightly different from the ones presented by the repository):

1. Turn on Compilation Database with  `west config build.cmake-args -- -DCMAKE_EXPORT_COMPILE_COMMANDS=ON`
2. Copy the `zephyr-linux.code-workspace` file to `~/zephyrproject/zephyr/samples/basic/thread`
3. Edit the workspace file in the following way:

* Update the Zephyr SDK path in `C_Cpp.default.compilerPath` value: `${userHome}/zephyr-sdk-0.17.0/arm-zephyr-eabi/bin/arm-zephyr-eabi-gcc`
* Modify the board name for the *West Build* task, setting it to `xg24_dk2601b`
* Modify the default board name in the `inputs` array, setting it to `xg24_dk2601b`
* Modify the device of the *Launch* configuration, setting it to `EFR32MG24BxxxF1536`
* Update the Zephyr SDK path in `gdbPath` of the *Launch* configuration
* Perform the above two steps for the *Attach* configuration

> [!NOTE]
> The device name can be found from the [list of supported devices provided by Segger](https://www.segger.com/supported-devices/search/).

Now, start VS Code and open the above workspace file (**File > Open Workspace from File...**).

After a few seconds, VS Code proposes to install the extensions recommended by the workspace file. Install them.

> [!NOTE]
> The *Python Environment Manager* extension is deprecated. I'm not sure yet if it can be replaced by the extension proposed by Microsoft.

Build the sample application, for instance with **Terminal > Run Task...**, then selecting **West Build**.

Ensure the board is connected to a PC's USB port, and attached to the VM. Flash the board, for instance with **Terminal > Run Task...**, then selecting **West Flash**.

To run the sample application in debug mode, select **Run > Start Debugging**.