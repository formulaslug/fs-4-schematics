**This is the home for all TLB(Telemetry Logging Board) Documentation, and WIP(Work In Progress) Notes**

# Editing this project
## Always export a PDF when committing to this repository
1. Navigate to the main schematic sheet
2. Select the "*File->Plot..*" menu from the top bar
3. Ensure the output directory is set to `./` and output mode is set to "Color"
4. Click "*Plot ALl Pages*" in the bottom right

## Schematic Standards
- Point all power flags up
- Point all gnd flags down
- Wires should not cross blue boxes
- Use dashed blue box for imported circuit blocks, and solid lines for internal containerization
- Use net labels instead of net flags
- Orient net labels so there is a "wire" beneath them, and it's visible (the "connection point" should be on the right side of the flag if the flag is on the right side of a component/net)

# WIP Notes
## TODO
This is a Non-Exhaustive list
### Little tasks
- [x] Chose RPI pins for all peripherals, net label them
- [ ] Check if MCP2517, MCP2518 are pin compatible, and note in sch and here

### Big tasks
- [ ] Implement Audio DAC
- [ ] Create symbol for Teledatics card, refferencing kicad builtin M.2 footprint -> formula\_slug\_symbol\_library
- [ ] Create symbol for Heltec card, refferencing kicad builtin mPCIe footpring -> formula\_slug\_symbol\_library 
- [ ] Implement CAN interface + transiever
    - [ ] create CAN transiever schematic block??
- [ ] Implement FTDI

## Questions To Answer
**Any questions here should be researched and answered in this file, or another .md file in this directory**

- Is writting logs directly to the sdcard good enough considering flash longevity?
- ~~Can 3 MCP2518FD's reach 1mbps on the same SPI bus using the maximum applicable SPI clock speed supported on a Pi?~~
- What other SBC's are pin compatible for our purposes? Which have h265? Useful camera connectors? EMMC? Physical Size?

### How many can transievers can share the SPI bus?
- The maximum speed of the SPI on the pi is the CPU speed, so that's irrelevant.

The MCP2518FD can Reach 20Mhz SPI speed:

At that speed, and assuming one CAN bus is running at 500khz, one at 1mhz, and the last at the maximum 12mhz, 67.5% of the theoretical maximum bandwidth of the SPI bus is consumed.

We need to also consider
- CS hysterisis delay
- "Wasted" bits in SPI communication

If the linux driver is given both CS and INT(input to linux) then it sets the MCP251* to pull down interrupt when it's recieve FIFO has anything in it, and set the interrupt type register. That register is read when the kernel gets around to it, and if it's value is for new message available, the kernel will ask it to send the complete rx FIFO over SPI. There are 32 FIFO's and each is 32 messages deep. I am unsure if they can be chained together. Assuming they cannot, each transiever's FIFO will need to be read out $\frac{f_{bus}}{140 \cdot 32}$ times per second. So from slowest to fastest, our transievers will need to be read at 112hz, 224hz, and 2680hz, for a total of ~3khz. These are absolute worst case maximum values. Assuming each message must be read individually (unlikely), the following timing is required: (required post-timing is included) (all timing given in SPI clock cycles unless otherwise noted)

```
CS -> n     Write 11 Bits to Start Read     <65 Bits outputted over SPI     7 Bits of CRC
25ns        11+50ns                         64                              7
```

So at 20mhz, each read transaction of one message takes $4.1\cdot10^{-6}$ seconds, and we can perform full FIFO reads at 7485khz, which is more than double what we need, and will allow us to run slower SPI speeds. I suspect by the time we consider that bus load should never be >50%, and that FS-3 full DAQ bus data rate (not bus clock speed) is only around 130kbps, we will be able to run 4mhz SPI without ever having a FIFO overflow.
