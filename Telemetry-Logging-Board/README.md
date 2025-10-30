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
- [ ] Chose RPI pins for all peripherals, net label them
- [ ] Check if MCP2517, MCP2518 are pin compatible, and note in sch

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
- Can 3 MCP2518FD's reach 1mbps on the same SPI bus using the maximum applicable SPI clock speed supported on a Pi?
- What other SBC's are pin compatible for our purposes? Which have h265? Useful camera connectors? EMMC? Physical Size?

