# ground-robot-communication

This repository contains information on the communication between the ground robot `3pi+`, the `nrf52840 dongle`, and the `Crazyradio 2.0`.

## Table of Contents


- [ground-robot-communication](#ground-robot-communication)
  - [Table of Contents](#table-of-contents)
  - [Project Overview](#project-overview)
  - [nrf52840 dongle](#nrf52840-dongle)
    - [Power Supply](#power-supply)
    - [UART Communication](#uart-communication)
    - [Programming and Flashing](#programming-and-flashing)
    - [Debugging](#debugging)
  - [Tasks](#tasks)
  - [References](#references)
  - [Helpful Links](#helpful-links)

## Project Overview

- goal: establish a communication between the `3pi+ robot` and the `Crazyradio 2.0` via the `nrf52840 dongle` in order to control the `3pi+ robot` from the `Crazyradio 2.0` for experiments
- members: `julien`, `dennis`
- system overview: `3pi+ <==> nrf52840 dongle <==> Crazyradio 2.0 (PC)`
- task assignment: 
  - `julien`
    - working out the details of programming the `3pi+ robot`
    - get the information on UART communication for the `3pi+ robot`
    - porting the code from the summer school to the `3pi+ robot`
  - `dennis`
    - working out the details of programming the `nrf52840 dongle`
    - get the information on UART communication for the `nrf52840 dongle`
    - work out the details of radio communication between the `nrf52840 dongle` and the `Crazyradio 2.0`

## nrf52840 dongle

This section contains helpful information on the `nrf52840 dongle` that has been gathered during the project.

> Note: The following information applies to Windows. The installation steps may vary for other operating systems but this will be mentioned in the referenced materials.

### Power Supply

The dongle can be powered in two ways:

1. USB power supply
2. External power supply

External power can be connected to the pin `VDD OUT` of the `nrf52840 dongle`. The allowed voltage ranges from 1.8V to 3.6V with a maximum current of 50mA. On the `nrf52840 dongle`, the `nrf52840` is configured in high voltage mode. To enable an external regulated source you will have to cut the board pad `SB2` and solder `SB1`. [[1](#references)]

The `nrf52840` can operate in two modes [[2](#references)]:

1. High voltage mode: Supply voltage ranges from 2.5V to 5.5V with a typical voltage of 3.7V.
2. Normal voltage mode: Supply voltage ranges from 1.7V to 3.6V with a typical voltage of 3.0V.

After correctly configuring the `nrf52840 dongle`, it could be connected to the pin `3V3` pin of the `3pi+ robot`. The `3pi+ robot` provides a regulated 3.3V supply voltage when powered by the battery or USB port. When supplied by a battery, it is able to deliver up to 1.5A via the `3V3` pin. [[3](#references)] 

### UART Communication

The `nrf52840 dongle` provides two UART interfaces, `UARTE0` and `UARTE1`. 

- `UARTE0`: id=`2`
- `UARTE1`: id=`40`

The UART pins have to be configured for the `nrf52840`. Here is the pin list:

- `RTS`
- `CTS`
- `TXD`
- `RXD`

### Programming and Flashing

The `nrf52840 dongle` can be programmed by using VS Code and the extension `nRF Connect for VS Code Extension Pack`. Please follow the following steps which are also outlined [here](https://docs.nordicsemi.com/bundle/ncs-latest/page/nrf/installation/install_ncs.html):

1. Install the package `nRF Command Line Tools` for your operating system.
2. Install the programm `nRF Connect for Desktop` for your operating system. You can find it [here](https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-Desktop)
3. Proceed with installing the VS Code extension `nRF Connect for VS Code Extension Pack`.
4. Open the `nRF Connect for VS Code Extension Pack` by clicking on the icon in the left sidebar. You can now install the toolchain and SDK. I donwloaded the newest version of both which was `2.6.0`. This may take a while. You can find more information [here](https://nrfconnect.github.io/vscode-nrf-connect/get_started/quick_setup.html).
5. After installing the toolchain and nRF SDK, you can browse and open a sample. I used the `blinky` example to test the flashing process. 
6. Build the project by adding a build configuration. To build programs for the `nrf52840 dongle` the board name `nrf52840dongle_nrf52840` has to be used. You can leave the optimization level as it is for now. This will become important when debugging the program later.
7. The built programm will be located in the `build` directory of the project, e.g. `build\zephyr\zephyr.hex`. Other files like `.bin` or `.elf` are also available.
8. Flash the application `build\zephyr\zephyr.hex` to the `nrf52840 dongle` using the `nRF Connect for Desktop` tool. 
   1. You will need to connect the `nrf52840 dongle` to your computer's USB port and press the reset button to switch into bootloader mode [[1](#references)]. 
   2. Open the `nRF Connect for Desktop` tool and select the `Programmer` tab.
   3. Select the connected device.
   4. Add the hex file and write it to the device.
   5. Your device should now be flashed with the new application and a green LED should blink on the `nrf52840 dongle`.


### Debugging

The `nrf52840 dongle` does not feature a built-in debugger-chip. However, the `nrf52840 dongle` can be debugged using the `J-Link EDU Mini` debugger which targets 3.3V platforms. The debugger can be connected to the `nrf52840 dongle` using the `SWD` interface. The `SWD` interface is located on the backside of the `nrf52840 dongle`. You can use the 10-pin ribbon cable and a USB hub to connect the debugger to the `nrf52840 dongle` [[4](#references)]. Image of the setup are given in the following.

![img](/img/debugging_1.jpg "debugging setup")
![img](/img/debugging_2.jpg "debugging setup")

To debug the `nrf52840 dongle` using the `J-Link EDU Mini` debugger, you will need to follow the these steps:

0. Copy the above hardware setup for the `nrf52840 dongle` and the `J-Link EDU Mini` debugger. Please first power up both devices and afterward connect the 10-pin ribbon cable to the `SWD` interface of the `nrf52840 dongle` and the `J-Link EDU Mini` debugger. The order of connecting the devices may cause problems. 

1. Install another version of the `J-Link` software on your computer. [[5](#references)]. I downloaded `V7.96` and checked the box to additionally install the USB driver. Having multiple versions of the `J-Link` software installed on your computer is not a problem. I was required to follow this step on my Windows laptop due to connection issues with the `J-Link EDU Mini` debugger. For now I am using the `J-Link` version that came with the `nRF Connect for Desktop` tool which is `V7.94e`.
2. Open the `J-Link Commander` tool and type `connect` in order to connect to the `J-Link EDU Mini` debugger. You can find the `J-Link Commander` tool in the `J-Link` software folder. [[6](#references)] You will be prompted for more information afterward. You can find it below.

   - target device: `nRF52840_xxAA`
   - target interface: `SWD`
   - target frequency: `8000` [[2](#references)]
   
    The terminal should look like this if the connection was successful:

    ```bash
    Connecting to target via SWD
    InitTarget() start
    InitTarget() end - Took 5.20ms
    Found SW-DP with ID 0x2BA01477
    DPIDR: 0x2BA01477
    CoreSight SoC-400 or earlier
    Scanning AP map to find all available APs
    AP[2]: Stopped AP scan as end of AP map has been reached
    AP[0]: AHB-AP (IDR: 0x24770011)
    AP[1]: JTAG-AP (IDR: 0x02880000)
    Iterating through AP map to find AHB-AP to use
    AP[0]: Core found
    AP[0]: AHB-AP ROM base: 0xE00FF000
    CPUID register: 0x410FC241. Implementer code: 0x41 (ARM)
    Found Cortex-M4 r0p1, Little endian.
    FPUnit: 6 code (BP) slots and 2 literal slots
    CoreSight components:
    ROMTbl[0] @ E00FF000
    [0][0]: E000E000 CID B105E00D PID 000BB00C SCS-M7
    [0][1]: E0001000 CID B105E00D PID 003BB002 DWT
    [0][2]: E0002000 CID B105E00D PID 002BB003 FPB
    [0][3]: E0000000 CID B105E00D PID 003BB001 ITM
    [0][4]: E0040000 CID B105900D PID 000BB9A1 TPIU
    [0][5]: E0041000 CID B105900D PID 000BB925 ETM
    Memory zones:
    Zone: "Default" Description: Default access mode
    Cortex-M4 identified.
    ```

    You can close the `J-Link Commander` tool after successfully verifying the connection.

3. Open the VS Code and install the extension `Cortex-Debug` [[8](#references)] as we will follow the debugging steps outlined in [[7](#references)].
4. Download and unpack the `ARM GNU Toolchain` [[9](#references)] for your operating system. The `nrf52840` features an `ARM Cortex-M4` 32-bit processor [[2](#references)]. I have downloaded the Windows (mingw-w64-i686) hosted cross toolchain for AArch32 bare-metal target (`arm-none-eabi`).
5. Open VS Code and click on the icon `Run and Debug` to create a `launch.json` file. You can select the `Cortex-Debug` configuration.
6. Add the following configuration to the `launch.json` file:

    ```json
    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Cortex Debug",
                "cwd": "${workspaceFolder}",
                "executable": "C:\\ncs\\v2.6.0\\zephyr\\samples\\basic\\blinky\\build\\zephyr\\zephyr.elf",
                "request": "launch",
                "type": "cortex-debug",
                "runToEntryPoint": "main",
                "servertype": "jlink",
                "serverpath": "C:\\Program Files\\SEGGER\\JLink_V794e\\JLinkGDBServerCL.exe",
                // "serverargs": "",
                "device": "nRF52840_xxAA",
                "interface": "swd",
                // "showDevDebugOutput": "raw",
                // "serialNumber": "", //If you have more than one J-Link probe, add the serial number here.
                // "jlinkscript":"${workspaceFolder}/BSP/SEGGER/K66FN2M0_emPower/Setup/Kinetis_K66_Target.js",
                // "svdFile": "${workspaceFolder}/SVD/MK66F18.svd",
                "armToolchainPath": "C:\\Users\\denni\\Downloads\\arm-gnu-toolchain-13.2.rel1-mingw-w64-i686-arm-none-eabi\\arm-gnu-toolchain-13.2.Rel1-mingw-w64-i686-arm-none-eabi\\bin"
            }
        ]
    }
    ```

    The `armToolchainPath` should point to the location of the `arm-none-eabi` toolchain on your computer. The `executable` should point to the `.elf` file of the project you want to debug. The `serverpath` should point to the `JLinkGDBServerCL.exe` file of the `J-Link` software.
7. If you haven't already, build your application with the optimization level `-Og` to enable debugging symbols in the `build\zephyr\zephyr.elf` file.
8. Add a breakpoint to your code and start the debugging process by clicking on the `Run and Debug` icon in VS Code. The program should stop at the breakpoint and you can now debug your application.

> Note: Please do not accidentally disconnect the `nrf52840 dongle`, `J-Link EDU Mini` debugger or any other part of the hardware setup while debugging. This could brick the `nrf52840 dongle` and you will have to restore its functionality afterward to use it again. Always stop the debugging process before disconnecting the devices.

## Tasks

- [x] Test debugging using `Cortex-Debug`
- [ ] Find an UART example for the `nrf52840 dongle`
- [ ] Find an UART example for the `3pi+ robot`
- [ ] Solder jumper cables to the `nrf52840 dongle` and `3pi+` for UART communication
- [ ] Adjust UART communication programs on both sides
- [ ] Test UART communication between the `nrf52840 dongle` and the `3pi+ robot`
- [ ] Move to radio communication between the `nrf52840 dongle` and the `Crazyradio 2.0`
  - [ ] ...

## References

1. [User guide for the nrf52840 dongle](https://infocenter.nordicsemi.com/topic/ug_nrf52840_dongle/UG/nrf52840_Dongle/intro.html) 
2. [User guide for the nrf52840](https://docs.nordicsemi.com/bundle/ps_nrf52840/page/keyfeatures_html5.html)
3. [User guide for our 3pi+](https://www.pololu.com/docs/0J86/all#5)
4. [J-Link EDU Mini](https://www.segger.com/products/debug-probes/j-link/models/j-link-edu-mini/)
5. [J-Link software](https://www.segger.com/downloads/jlink/)
6. [J-Link user guide](https://wiki.segger.com/UM08001_J-Link_/_J-Trace_User_Guide)
7. [J-Link VS Code guide](https://wiki.segger.com/J-Link_Visual_Studio_Code)
8. [Cortex-Debug](https://github.com/Marus/cortex-debug/)
9.  [ARM GNU Toolchain Download](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads)

## Helpful Links

1. [nrf52840 dongle not showing up in connected devices tree of VS Code](https://devzone.nordicsemi.com/f/nordic-q-a/102916/nrf52840-dongle-isnt-showing-up-in-the-connected-devices-section-on-vs-code)
2. [nrf52840 dongle programming tutorial](https://devzone.nordicsemi.com/guides/short-range-guides/b/getting-started/posts/nrf52840-dongle-programming-tutorial)
3. [J-Link IDE support](https://www.segger.com/products/debug-probes/j-link/technology/ides/overview-of-supported-ides/)
4. [nrf52840 dongle bricked](https://devzone.nordicsemi.com/f/nordic-q-a/64921/nrf52840-dongle-bricked-after-programming)