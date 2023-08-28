---
layout: post
title:  "Extracting Game Boy ROMs with a Saleae Logic Analyzer"
categories:
---

# Overview
This article goes in-depth about how to extract ROMs from a game boy cartridge using a logic analyzer, in this case the [saleae logic](https://www.saleae.com/) (a great tool for hardware hackers and hobbyists)! <br />

For more in-detail information about the Game Boy, the best references I have found are the [1998 devrs document by k00pa](https://www.devrs.com/gb/files/gbspec.txt) and Gekkio's [Game Boy: Complete Technical Reference](https://gekkio.fi/files/gb-docs/gbctr.pdf). 

Note that ROM extraction this way is mostly for-fun exercise, there are great tools like [insidegadget's GBxCart](https://www.tindie.com/products/insidegadgets/gbxcart-rw-gameboygbcgba-cart-readerwriter/) that handle this for you. <br />

# Identifying the ROM chip 
The 32-pins at the bottom of the game boy cartridge are the same across every cartridge, since they must connect to the console motherboard. However, the cartridge layouts vary immensely. <br />

The ROM chip is typically the largest on the cartridge. The address pins (pins 5-19 starting at 0) typically connect to a *memory bank controller*,  a piece of hardware used for bank switching. The ROM chip will connect to the data lines, D0-D7, or pins 21-28 on the cartridge. The exact pinout can be found [here](https://allpinouts.org/pinouts/connectors/cartridges_expansions/gameboy-cartridge/). <br /> 

It can be seen as the bottom right chip on this cartridge. 

![Game Boy ROM Cart](/images/gbrom-extract/gb_cart.jpg) <br />

# Reading the ROM contents

Using the saleae logic clips, attach one pin to the clock signal and 7 data lines if using an 8-pin logic analyzer and or all 8-pins if using a 16-pin logic analyzer. Note that it might take a few tries to find the right pins! <br /> 

![Game Boy Cart Saleae](/images/gbrom-extract/saleae.jpg) <br /> 

From here the next step is to force the ROM to be read. The easiest means of doing this is with dedicated hardware, like the aforementioned GBxCart. Alternatively, an easy setup to toggle power to the cartridge is with a Game Boy Color. Remove the back from the Game Boy Color and insert the cartridge. The handheld will read the ROM's contents on boot. <br /> 

# Analyzing the Capture
Once a good capture is obtained, look for the Nintendo logo in the ROM's header. This is constant 48-byte value across all cartridges. It is defined [here](https://www.devrs.com/gb/files/gbspec.txt). 

![Game Boy Header](/images/gbrom-extract/header_table.png) <br /> 

Click on *Analyzers* on the right toolbar, then click the "+" icon. Select "Simple Parallel" as the analyzer type. Input the clock signal and the rest of the data lines. 

![Logic Capture](/images/gbrom-extract/logic_cap.png) <br />

This capture shows a 7-bit capture with the MSB (D7) missing. Note that all ASCII data is still ordinarily visible with this bit missing. Here you can see the Game Boy reading the title of the game, "TETRIS2", as well as the tail-end of the 48-byte Nintendo graphic. <br />

# Troubleshooting and Post-Processing on the Capture
To transfer the ROM into something that be emulated or reflashed, go to "File>Export Data" then export as a CSV. The CSV can then be processed in the programming language of your choice; I used python. <br />

Getting a good capture is the hardest part, if you find that your captures are "noisy", try turning down the sampling rate. If you find no data at all, then check to make sure that ground is properly connected. If you find that the checksums in the ROM header are off, I suggest taking multiple captures and stitching them together to account for bit errors. 








