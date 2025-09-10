# A2RAM128

This is an expansion card for Apple2 computers, for machines which only have up to 48kB of RAM and are missing the upper 16kB, which are mapped to the 12kB ROM space $D000-$FFFF.
The cards that are available, AKA Language cards, not only occupy a slot, but often must have one of the DRAM chips removed from the motherboard and be linked with a cable to that socket. They usually provide more than an additional 16kB.

Here I created a card for the standard 50-pin slots, which adds 8 banks of 16kB - a total of 128kB, based on modern chips like SRAM, and not needing to have any additional cables to connect to the motherboard.

**IMPORTANT: The card must not be used on computers which already have the 16kB BSR available as there would be clashes between the card and the existing RAM's supporting logic.**

This project is based on a card with one 128kB SRAM chip and one PLD to decode the signals.

The logic of the card completely follows the design recommendations of the Saturn cards:

There are 8 banks of the following organization:
* Two sub-banks A and B of 4kB are mapped to $D000-$DFFF
* One sub-bank of 8kB is mapped to $E000-$FFFF

Bank-switched RAM soft switches:

| Address | Symbolic Name | Bank | Read From | Write to RAM? |
|:--------|:--------------|:-----|:----------|:--------------|
|$C0N0|READBSR2|2|RAM|No|
|$C0N1|WRITEBSR2|2|ROM|Yes*|
|$C0N2|OFFBSR2|2|ROM|No|
|$C0N3|RDWRBSR2|2|RAM|Yes*|
|$C0N8|READBSR1|1|RAM|No|
|$C0N9|WRITEBSR1|1|ROM|Yes*|
|$C0NA|OFFBSR1|1|ROM|No|
|$C0NB|RDWRBSR1|1|RAM|Yes*|

| Address | Function |
|:--------|:--------------|
|$C0N4|Select Bank 1|
|$C0N5|Select Bank 2|
|$C0N6|Select Bank 3|
|$C0N7|Select Bank 4|
|$C0NC|Select Bank 5|
|$C0ND|Select Bank 6|
|$C0NE|Select Bank 7|
|$C0NF|Select Bank 8|

N is the Slot Number + 8.

Operation:

The card houses the 3 soft-switches above which upon reset are configured in the default configuration:

* Sub-bank A is selected
* ROM is readable
* RAM is writeable

A test to be made for quick check of functionality:

]CALL-151           # Enter monitor

*C0N1               # Start activation of RAM for writing (v2 only)

*C0N1               # Activation of RAM for writing

*F800<F800.FFFFM    # Copy monitor to RAM

*C0N3               # Set RAM for reading

This sequence should not lead to the computer freezing with a responce "$C0N3-", because the monitor is copied to the RAM and continues to work from RAM.

The benefit of the card is that programs which require 64kB of RAM or more can be used - primarily ProDOS. It works from any 50-pin slot with no additional cabling - P&P, however some programs expect that the card is in slot #0, so this is the recommended use.

V2.0 was created to fullfill the requrements by the Saturn cards to activate the card for writing by two succecutive calls to C0N1. It is advisable that this version is used.

