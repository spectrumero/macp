# ZX Make-a-Chip Pro

Back in the day, there was some software for the Sinclair Spectrum (often
advertised today as "VERY R@RE!!!!11" on eBay) called Make-a-Chip, by
Icognito Software Ltd. It waas a circuit simulator that allowed you to
build simple logic circuits with digital gates (AND, OR, NAND, NOR).

Today, of course, we can go many steps better; we have the FPGA (field
programmable gate array) to play with, and we can realise our own designs,
right up to entire systems on a chip.

2022 is the 40th anniversary of the launch of the Sinclair Spectrum, and
this project is something to celebrate that.

The ZX Make-a-Chip Pro is a board with a Lattice iCE40-HX4K FPGA with
all the level shifting required to connect to a Sinclair Spectrum.
It's designed to work with the open source IceStorm toolchain for
the FPGA used (but will no doubt work just fine with the Lattice
proprietary toolchain, too). Aside from the FPGA, it also has some
static ram (128k x 8, 256k x 8 or 512k x 8), the serial flash to 
hold the FPGA's "gateware" (with room left over for other uses),
a crystal oscillator, interface electronics for the 3.3v to 5v level
translation, and 25 GPIOs for connecting to other things.

## The PCB

A 4-layer PCB. This has been successfully made using PCBWay.

## Major parts

The FPGA is an iCE40-HX4K-TQ144. Unfortunately the HX1K has a sufficiently
different pinout that it won't work. When using arachne-pnr/nextpnr during
place and route, select "8k" as the device and "tq144:4k" as the package.
(In reality, the die in this chip is an HX8K)

Any JEDEC compliant SPI flash chip will work but it **must** support
the page program command. Check the datasheet before you commit to a chip.
While flash that doesn't support paged write will work, you won't be able
to program it with the `iceprog` tool - at least without modifying it to
work with a chip that doesn't support the page program command (and it's
doubtful you will be able to make it work at all with the Lattice
proprietary IDE).

Look for support for 'PP' (command 0x02) in the chip's datasheet.
The chip must also support 64kb block erase.

The gateware requires three 64kb blocks. This gets written to the first
three 64kb blocks. Remaining space in the flash may be used for projects 
(e.g. code), so as long as the flash is bigger than 192kbytes, it's good 
enough.

The RAM is on the 3.3v side of the board, so ensure that the static RAM
works with 3.3v (there are still quite a few 5v static RAM chips that won't
go down to 3.3v). The faster the better. If you only use the static RAM
for the Spectrum side then you can pretty much use any chip you please,
but if you're going to implement a second processor on the FPGA, you
probably want something fairly fast (10ns is available).

The crystal oscillator I use is 25MHz. If you choose a different value,
it's worth checking that it fits in the input limits of the FPGA's
PLL.

The remainder of the devices on the board should be pretty widely availble.

## Special symbols

There are two symbols unique to the project: the ZX edge connector
(designed for through hole mounting) and a schematic symbol for the
74ALVC164245PVG bus translator. The edge connector also has an associated
PCB footprint. See the local-lib directory.

## Programming other parts of the flash with iceprog

The icestorm toolchain's `iceprog` program can do more than just write
the gateware to the flash chip. You can supply it an offset, for example
if you want to store ROM code that will be used later by the FPGA
or copied to RAM, or a FatFS filesystem:

`iceprog -0 0x30000 file.bin`

to write the contents of a file from 0x30000 (the 64k block immediately
following the gateware).

## Recommended programming hardware

The Adafruit FT232H board works well (any similar board with this FTDI
chip should work with `iceprog`). Connect it as follows:

D0 -> SCK
D1 -> SDO
D2 -> SDI
D3 (not connected)
D4 -> SS
D5 (not connected)
D6 -> CDONE
D7 -> CRESET

You may need to invoke `iceprog` as follows:

`iceprog -d i:0x0403:0x6014 <filename>`

...if it fails to find your FT232H breakout board. (You may need `sudo`
as well).

