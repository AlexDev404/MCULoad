# MCULoad
**Serial bootloader utility for STM32, LPC17xx, MSP430, CC13xx, Tiva C, Z180, eZ80**

From: http://hsl.wz.cz:8080/MCUload.htm

I often use serial bootloader for debugging applications. I wrote bootloader utility for every MCU earlier, but one time I felt I needed general utility because it was always the same: load Intel-HEX file, open serial port, send bootloader commands; after programming open serial terminal and print debugging messages and send back debugging commands.

It was quite helpful when I started using USB-CDC adapter in my work. It has RxD and TxD signal (of course, it is serial ;-), and also /RST and BOOT signals used to reset MCU and switch between bootloader and application mode. Since this time I have the same connector in all my devices. So this was short history how MCUload has been born. And the last thing is that I use this utility both on Windows and Linux so I always have the same behaviour when I am debugging.

First note about connection: reset signal is connected to DTR, bootloader selection to RTS. When either signal is set (in meaning of system), TTL signal has low level. MCUload can be used with any real or virtual serial port which has these signals (DTR, RTS).



### Command line

Command-line syntax is: `MCUload [options] file`. Here is list of all available command-line options:

- **General options**
- **-?**, **--help**: print command-line help
- **--verbose**: select level of debugging output printed; default is 1
- **File options**
- **file**: binary or Intel-HEX filename
- **--mode**: file parsing mode; default is **auto**, can be manually switched to **bin** or **hex** mode
- **--unsort**: data loaded from Intel-HEX file are sorted and programmed starting with lower addresses; this option disables sorting and keeps data unsorted
- **Basic serial port options**
- **-p**, **--port**: select serial port name; number on windows or /dev/... on Linux
- **--vid**, **--pid**, **--serial**: automatically searches serial port number or name (on Windows or Linux)
- **Programming options**
- **--read**: reads out content of MCU's memory; currently supported for STM32 only
- **-F**, **--frequency**: set connected quartz frekvency in kHz; defaults to 12000 for LPC17xx; also used for eZ80 flash memory timing
- **-E**, **--erase**: erase flash before programming
- **-V**, **--verify**: verify memory after programming
- **-X**, **--execute**: execute code after programming
- **--addr**: set address for programming; usually used with binary file
- **--start**: set address for executing code; usually used with binary file
- **--mcu**: select connected MCU (one of tiva/stm32/lpc17xx/lpc21xx/cc13xx/msp430/z180/ez80)
- **Terminal options**
- **-T**, **--term**: run terminal after successful programming
- **--baud**: select baudrate for programming and terminal; default is usually 115200, Z180 and eZ80 uses 57600; some MCUs override this selection
- **--parity**: select parity for programming and terminal (one of none/odd/even/mark/space); default is none; some MCUs override this selection
- **--boot**: select level of BOOT signal when switching to terminal
- **--rst**: select level of RST signal when switching to terminal
- **--log** *filename*: append log to selected file; all terminal communication will be saved into this log file
- **--command** *command string*: send entered commands to application after terminal is launched; \r and \n can be used for more that one command; %D, %M, %Y or %YY are replaced by current date; %h, %m, %s are replaced by current time; \d disables these commands to be repeated when reload function is activated
- **-R**, **--reload**: enable reload function - when input file is modified (newly generated) or ^L is pressed, MCU is programmed again
- **STM32 specific options**
- **-EO**, **--erase-optimized**: enables optimized erase algorithm; it is the same as -E 2
- **-PRT**: control protection setting (can be one of none/-r/+r/-r+r/-w/-r-w/+r-w/-r+r-w/+w/-r+w/+r+w/-r+r+w/-w+w/-r-w+w/+r-w+w/-r+r-w+w)
- **MSP430 specific options**
- **--bsl** *filename*: bootloader patch for 1.11 bootloader; default is bl_150s_14x.hex
- **--pwd** *filename*: file with previous password (interrupt vectors) to allow bootloader to be accessed again
- **eZ80 specific options**
- **--CS0**, **--CS1**, **--CS2**, **--CS3**: setting for CSx signals: appropriate UBR:LBR:CTL:BMC registers are set
- **--iFLASH**: setting for FLASH access (ADDR_U:CTRL)
- **--iRAM**: setting for internal RAM access (ADDR_U:CTL)

#### Examples

Almost all options can accept values: decimal values begin with 0..9; hexadecimal values begin with x.

Eg. disabling internal Flash for eZ80, `--iFLASH 0` can be used. Or enabling internal RAM: `--iRAM xB780`.

`--command "time %%h:%%m:%%s\rdate %%D.%%M.%%YY\r\d"` sets current time and date when first programmed; If reload ir requested, this command is not used again.

#### Used protocols

Added later...

#### Terminal control

Terminal supports basic ANSI/VT100 keycodes and ESC-control, so F-keys can be used and some basic display control is processed. Terminal is exited with ^X key. RST signal is activated with ^R, BOOT pin can be changed with ^B, file reload can be manually requested by pressing ^L.
