# Morello board getting started
There are plenty of usefull documentation that has been already done to the very high standard and we did not wanted to copy and paste. Instead, we have combined and highlighted all the obstacles we have came across ourselves or heard from other participants. In case your issue has not been described here we would like to kindly ask you to email us **DSBD Programme <dsbdprogramme@digicatapult.org.uk>**. In this guide you will find:
- links to the valuable documents in regards to Morello project
- common issues highlighted
- what to expect and what do you need to do to be able to build your software on Morello board
- installation of CheriBSD
> Since this is a new project the documentation is scattered around the internet and in some cases the end-user might not even be aware that such documents do already exists. We have combined a number of documents and broken down into smaller paragraph so you don't have to read through all of it in order to get started.

### List of TODOs
- [ ] - Document Androind
- [ ] - Document Linux
- [ ] - Update connection paragragh to include `windows` and `putty` (get feedback from RealVNC)
- [ ] - Ask Konrad to take a look and links to any other relevant material


### Some insights
To be fair it depends on the software stack which system would suit you the most. At the moment very high focus is on CheriBSD so when it comes to packages as far as we know nearly 50% have been made compatible already

> Currently Morello supports three systems
> - CherisBSD - most advanced
> - Linux - to be fair we would need to confirm how much progress have been made of this one
> - Android - as far as we are aware this is has got a fairly good coverage but if you can run same stack in CheriBSD, then our recomendation would be to use CheriBSD


## a) Connection (Hardware)
There is nothing special about this step but we have found a few people getting confused by the anode display, thinking as of error code. But in some cases this will indicate that machine is starting or started. Here is a detailed guide in regards to setting up Morello [hardware](https://developer.arm.com/documentation/den0132/0100/Setting-up-the-Morello-Hardware-Development-Platform)


## b) Connection (UART/SERIAL) - via USB
This is the first step and in some cases it might be trickiest as without being able to connect you can not interact with Morello board also at this point you can't not get any video output. In order to connect to the machine you would require to follow the below guide. It has been extracted from [here](https://git.morello-project.org/morello/docs/-/blob/morello/mainline/user-guide.rst#id32)

- Connect a USB-B cable between the host machine and Morello's USB DBG port on the back panel.
- Connect a LAN cable to the PCIe GbE LAN port that is above the 2 USB3 host ports.
- Connect an HDMI cable if display is required. - **This wasn't working when we tried so please beware**
- Connect the supplied C13 power cable to the C14 socket on the rear of the platform.
- Switch-on the ATX PSU using the switch next to the C14 socket at the rear ofthe platform.
- After power on, the board will enumerate 8 COM ports (user might need to wait for a few minutes, depending on the host system).
- Note down the newly enumerated COM port numbers. Assuming that the COM ports start at ttyUSB<n>, following is the COM port assignment:

> This can become a tricky part on OS X system because they follow a different pattern. For example in our case we have had `tty.usbserial-<n>.` so it might be a good idea to if running linux/unix system to run `sudo ls -la /dev | wc -l` - this will return a total number of items found in `/dev` so if Morello board has been recognised the number should increase and then finding new connections should be straight forward. However Windows users would need to into `Device Manager` and confirm in there that a new device has been detected. Below you will find a table with some port examples. In order to connect via USB we have used `tty.usbserial-0.` on OS X system
> You might be required to check every port to confirm if serial connection is working and you can access the Morello board

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

> Note: Some serial port applications, including minicom, refer to this as "115200 8N1" or similar. Other tools also could be used go establish a serial connection. Just please make sure it's cofigured using the above settings.

An example usage to open the MCC console using minicom is as follows:
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
This is the most time consuming task but in order to get most of Morello board and CheriBSD this is something you **must** do. Please note that downloading from source code can take up to 10 hours and maybe in some cases even longer. There are multiple source repos so if you don't need any android or busy box I would strong advise to build with `none`. A full guide can be found -> [Firmware Update Guide](https://git.morello-project.org/morello/docs/-/blob/morello/mainline/user-guide.rst). This is very important step as it unlocks all the latest feature available.

And for flashing the onboard SD card please follow [this](https://developer.arm.com/documentation/den0132/0100/Flash-the-onboard-SD-card) guide. It's fairly well written as we were able to follow it without any obstacles.

The guide is quite well detailed. However, the dependencies are for ubuntu/debian systems, but those are the standard packages so every distribution should have. For example in the guide it says
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

After the installation of dependencies please carry on following steps in -> [guide](https://git.morello-project.org/morello/docs/-/blob/morello/mainline/user-guide.rst).


## Installing OS
Once you have updated the firmware it's time to put some operating system on the Morello board.
> we would strongly recommend to update to the latest firstmaware as hardware comes with the firmaware from early January. There is a lot of active development going on at this moment so in order to see full potential of Morello please update the firmware before starting.
  
#### CherisBSD
For this step you will be required an USB stic
  
#### Android
> to be updated... after giving a go

#### Linux
> to be updated... after giving a go

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
