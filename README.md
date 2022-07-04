# Morello board getting started
A significant and comprehensive amount of documentation has been already completed to a particularly high standard on the topic of Morello architecture, commonly reported concerns, and solutions. So as to not duplicate work in this already strong knowledge base, the DsBD team has collated and condensed all the challenges reported by users that we have become aware of, either directly or from other participants. If you have a concern that has not been described here, please kindly email the DSbD Programme at **dsbdprogramme@digicatapult.org.uk**.

In this guide you will find:
- Links to key documents about the Morello project challenges and solutions
- Common issues that have been highlighted
- What to expect and how to build your software on Morello board
- Installation instructions for CheriBSD

> As DSbD is a relatively new project, related documentation is fragmented around the internet, and as such, end-users may not be aware of all the documents already in existence. This user guide combines several of these documents together, broken down into smaller and more accessible paragraphs to allow end users to get started quickly.


### Project insights
Choosing the system that will best suit your needs is dependent upon the software stack chosen. Currently, there is a particularly high focus placed by industry on CheriBSD. As such, nearly 50% of packages, have already been made compatible.

> Currently, Morello supports three systems:
- CherisBSD - most advanced
- Linux - to be fair we would need to confirm how much progress has been made on Linux, as far as we know only C compiler works
- Android - as far as we are aware this is has got a fairly good coverage but if you can run same stack in CheriBSD, then our recomendation would be to use CheriBSD


## Connection

### a) Hardware
Some users have reported difficulties with the anode display, which can be mistaken for an error code. In some cases, this will indicate that machine is starting or started. More information and a detailed guide for setting up Morello hardware can be found [here](https://developer.arm.com/documentation/den0132/0100/Setting-up-the-Morello-Hardware-Development-Platform).


### b) UART/SERIAL - via USB
This is the first step, and in some cases the trickiest, as not being able to connect prevents interaction with the Morello board or receiving video output. In order to connect to the machine, please follow the below guide, extracted from [here](https://git.morello-project.org/morello/docs/-/blob/morello/mainline/user-guide.rst#id32)

- Connect a USB-B cable between the host machine and Morello's USB DBG port on the back panel.
- Connect a LAN cable to the PCIe GbE LAN port that is above the 2 USB3 host ports.
- Connect an HDMI cable if display is required. - **This wasn't working when we tried so please beware**
- Connect the supplied C13 power cable to the C14 socket on the rear of the platform.
- Switch-on the ATX PSU using the switch next to the C14 socket at the rear ofthe platform.
- After power on, the board will enumerate 8 COM ports (user might need to wait for a few minutes, depending on the host system).
- Note down the newly enumerated COM port numbers. Assuming that the COM ports start at ttyUSB<n>, following is the COM port assignment:

> This can become a tricky part on OS X system because they follow a different pattern. For example in our case we have had `tty.usbserial-<n>`. so it might be a good idea to if running linux/unix system to run `sudo ls -la /dev | wc -l` - this will return a total number of items found in /dev so if Morello board has been recognised the number should increase and then finding new connections should be straight forward. However, Windows users would need to into Device Manager and confirm in there that a new device has been detected. Below you will find a table with some port examples. In order to connect via USB we have used `tty.usbserial-****B0`. on OS X system You might be required to check every port to confirm if serial connection is working and you can access the Morello board

| COM PORT           | FIDI device serial number/port | Description                                 |
|--------------------|:------------------------------:|--------------------------------------------:|
| ttyUSB<n>          | 00FT<platform-serial-num>B(0)  | Motherboard Configuration Controller(MCC)   | 
| ttyUSB<n+1>        | 00FT<platform-serial-num>B(1)  | Platform Controller Chip(PCC)               |
| ttyUSB<n+2>        | 00FT<platform-serial-num>B(2)  | Application Processor(AP)                   |
| ttyUSB<n+3>        | 00FT<platform-serial-num>B(3)  | System Control Processor(SCP)               |
| ttyUSB<n+4>        | 00FT<platform-serial-num>A(0)  | Manageability Control Processor(MCP)        |
| ttyUSB<n+5>        | 00FT<platform-serial-num>A(1)  | IOFPGA UART0                                |
| ttyUSB<n+6>        | 00FT<platform-serial-num>A(2)  | IOFPGA UART1                                |
| ttyUSB<n+7>        | 00FT<platform-serial-num>A(3)  | AP Secure UART                              | 

Open the MCC, AP and SCP COM ports using a serial port application such asminicom with the following settings:
- 115200 baud
- 8-bit word length
- No parity
- 1 stop bit
- No flow control

> Note: Some serial port applications, including minicom, refer to this as `115200 8N1`; or similar. Other tools also could be used go establish a serial connection. Just please make sure it's configured using the above settings. 

> An example usage to open the MCC console using minicom is as follows:
- (Ensure to have minicom package pre-installed to the host using "sudo apt-get install minicom") `sudo minicom -s /dev/ttyUSB<n>`
- Select Serial port setup from the user interface and configure the port settings mentioned above. Select Exit to apply the settings and to proceed with the port connection.
- Refer to the COM port nomenclature in the table above to choose the desired port to establish the connection with. For more information on minicom usage, refer to minicom Manual.

In the MCC console, run USB_ON command. This will launch the microSD card
in the Morello board as a mass storage device in the host PC.

```sh
Cmd> USB_ON
```

> Enter the following command on the MCC console window to ensure date and time is correctly set, based on the UTC. This is a one time setting and is required when booting the board for the first time. If the date and time values are incorrect, set them to the correct UTC-based values.
```sh
Cmd> debug
Debug> time
Debug> date
Debug> exit
```
- windows / putty - TODO
- COM ports - TODO


### Updating firmware
Although this task may be the most time-consuming task, it is required in order to get most of Morello board and CheriBSD. Please note that downloading from source code can take up to 10 hours and in some cases even longer. There are multiple source repos so, if an android or busy box is not required, it would be advisable not to use one. A full guide can be found [here](https://git.morello-project.org/morello/docs/-/blob/morello/mainline/user-guide.rst). This is very important step as it unlocks all the latest feature available.

For flashing the onboard SD card please follow [this](https://developer.arm.com/documentation/den0132/0100/Flash-the-onboard-SD-card) guide. Please note that the dependencies are for ubuntu/debian systems, but these are the standard packages that every distribution should have. For example, the guide says: 

```sh
sudo apt-get update
sudo apt-get install autoconf autopoint bc bison build-essential \
    ca-certificates cpio curl device-tree-compiler dosfstools doxygen \
    fdisk flex gdisk gettext-base git libncurses5 libssl-dev libtinfo5 \
    linux-libc-dev-arm64-cross lsb-release m4 mtools  pkg-config python \
    python3-distutils rsync snapd unzip uuid-dev wget
```
So for OSX you should be able to use `brew` like:
```sh
sudo brew install autoconf autopoint bc bison build-essential \
    ca-certificates cpio curl device-tree-compiler dosfstools doxygen \
    fdisk flex gdisk gettext-base git libncurses5 libssl-dev libtinfo5 \
    linux-libc-dev-arm64-cross lsb-release m4 mtools  pkg-config python \
    python3-distutils rsync snapd unzip uuid-dev wget
```
For other systems please use system's package manaer e.g. `yum` 

```sh
-g bsp (Only downloads software components required for BSP)
-g busybox (Downloads software components for both BSP and BusyBox)
-g android (Downloads software components for both BSP and Android)
-g busybox,android (Downloads software components for BSP, BusyBox and Android)
```

After the installation of dependencies please carry on following steps in [here](https://git.morello-project.org/morello/docs/-/blob/morello/mainline/user-guide.rst).


## Installing OS
Once the firmware has been updated, installing an operating system on the Morello board will take place.

> It is recommended to update to the latest firstmaware, as hardware comes with the firmaware from early January. There active development is currently underway on this topic, so in order to see the full potential of Morello, please update the firmware prior to starting.
  
### Building and running CheriBSD
> To build CheriBSD run cheribuild.py cheribsd-, with architecture being one of
- `riscv64:` Kernel and userspace are RISC-V without CHERI support.
- `riscv64-hybrid:` Kernel is RISC-V with CHERI support (hybrid), but most programs built as plain RISC-V.
- `riscv64-purecap:` Kernel is RISC-V with CHERI support (hybrid), and all userspace programs built as pure-capability CHERI binaries.
- `mips64:` Kernel and userspace are MIPS without CHERI support.
- `mips64-hybrid:` Kernel is MIPS with CHERI support (hybrid), but most programs built as plain RISC-V.
- `mips64-purecap:` Kernel is MIPS with CHERI support (hybrid), and all userspace programs built as pure-capability CHERI binaries.
- `aarch64:` Kernel and userspace are AArch64 without CHERI support.
- `morello-hybrid:` Kernel is AArch64 with CHERI (Morello) support (hybrid), but most programs built as plain AArch64.
- `morello-purecap:` Kernel is AArch64 with CHERI (Morello) support (hybrid), and all userspace programs built as pure-capability CHERI binaries.
- `amd64:` Kernel and userspace are 64-bit Intel x86.

#### Disk image
The disk image is created by the `cheribuild.py disk-image-<architecture>` target and can then be used as a boot disk by QEMU.
In order to customize the disk image it will add all files under (by default) `~/cheri/extra-files/`
to the resulting image.

> A suitable `/etc/rc.conf` and `/etc/fstab` will also be added to this directory and can then be customized.
> The default path for the disk image is ~/cheri/output/cheribsd-<architecture>.img, i.e. `cheribsd-riscv64-purecap.img` for pure-capability **RISC-V** or `cheribsd-mips64.img` for **MIPS** without **CHERI** support.

##### CheriBSD SSH ports
It will print a message such as `Listening for SSH connections on localhost:12374`. This can be changed using `cheribuild.py --run/ssh-forwarding-port <portno> run-<architecture>` 

##### Speeding up SSH connections
Connecting to CheriBSD via ssh can take a few seconds. Further connections after the first can be sped up by using the openssh ControlMaster setting:
```sh
Host cheribsd-riscv
  User root
  Port 12345
  HostName localhost
  ControlPath ~/.ssh/controlmasters/%r@%h:%p
  ControlMaster auto
  StrictHostKeyChecking no
  
Host cheribsd-riscv-purecap
  User root
  Port 12346
  HostName localhost
  ControlPath ~/.ssh/controlmasters/%r@%h:%p
  ControlMaster auto
  StrictHostKeyChecking no
```
#### A complete guide and source code can be found [here](https://github.com/CTSRD-CHERI/cheribuild)

    
#### Android
https://git.morello-project.org/morello/docs/-/blob/morello/mainline/user-guide.rst#id15 - to be updated after some research...

### TODO
- [ ] - Document Androind
- [ ] - Update connection paragragh to include `windows` and `putty` (get feedback from RealVNC)
- [ ] - Ask Konrad to take a look and get any other relevant links/docs
    
## List of useful documentation
- [Arm Morello Program](https://www.morello-project.org/)
- Flashing SD Card - [Flash SD Card](https://developer.arm.com/documentation/den0132/0100/Flash-the-onboard-SD-card)
- DSbD and CheriBSD - [DSbD Morello](https://www.cl.cam.ac.uk/research/security/ctsrd/pdfs/20210507-dsbd-cheri-morello.pdf)
- Getting started with CheriBSD - [Getting Started Guide](https://ctsrd-cheri.github.io/cheribsd-getting-started/cover/index.html)
- Installing Morello CheriBSD on Morello board - [Installing on Morello board](https://ctsrd-cheri.github.io/cheribsd-getting-started/installing/index.html)
- CheriBSD source code - [GitHub](https://git.morello-project.org/university-of-cambridge/mirrors/cheribsd)
- C/C++ Programming Guide - [C/C++ Programming Duide](https://www.cl.cam.ac.uk/techreports/UCAM-CL-TR-947.pdf)
- Other
  - [CHERI R&D](https://cheri-cpu.org/)
  - [CHERI desktop report](http://www.capabilitieslimited.co.uk/pdfs/20210917-capltd-cheri-desktop-report-version1-FINAL.pdf)
  - Run an instance of CheriBSD/Morello in QEMU - `./cheribuild.py --include-dependencies run-morello-purecap https://github.com/CTSRD-CHERI/cheribuild`
  - [Try CHERI training exercises for developers](https://ctsrd-cheri.github.io/cheri-exercises/)
  - [Talk to the CHERI community](https://ctsrd-cheri.github.io/cheribsd-getting-started/support/)
  - [GitHub organisation](https://github.com/CTSRD-CHERI)
