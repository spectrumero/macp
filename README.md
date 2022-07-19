# macp
**ZX Make-a-Chip Pro**

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
