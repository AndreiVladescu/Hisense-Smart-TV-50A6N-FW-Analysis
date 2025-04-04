
<link  rel="shortcut icon"  type="image/x-icon"  href="icon.ico">
<figure>  <img
src="./images/banner.jpeg"
alt="Exploiting the router"
>  </figure>

  
# Introduction

The Hisense [50A6N](https://qrcode.hisense.com/appliance/0000000000200138520000000000000000000?lang=en) is a chinese smart TV which offers 4K resolution. While the TV seems solid and is sold at a cheap price, it failed right after it went out the warranty, a defect in the screen's backlight board. This gave me an oportunity to pry it open and take a look at the insides of the boards.
  
<img  src="./images/hisense_tv.png"  width="30%">


# Internal Photos

The TV has 4 boards, not counting the flexible PCBs that the display uses. 

## Main Board
It's main board houses the power section, the on-board computer (since this is a smart TV, I assumed it used Android).

<div style="display: flex; flex-direction: row; align-items: center;">
  <img src="./images/mainboard_frontside.png" width="23%" style="margin-right: 50px;">
  <img src="./images/mainboard_backside.png" width="30%">
</div>


The main board's name is `he50a6109fuwts`. Taking a close look at it, we can segment the more interesting parts from the "dumb" components.

<img src="./images/mainboard_frontside_annotated.png" width="30%">

The CPU sits behind the black heatsink, and if that doesn't give it away, maybe the backside of the board with all the fine traces leading to it may give it away. To see the CPU the heatsink needs to be desoldered.

<img src="./images/closeup_mainboard_computer.jpeg" width="20%">

| Component  | Name                           | Image                                      | Datasheet                                                                                |
| ---------- | ------------------------------ | ------------------------------------------ | ---------------------------------------------------------------------------------------- |
| CPU        | MStar MSD6886NQGAT-8-00GH      | <img src="./images/cpu.png" width="75%">   | not available                                                                            |
| RAM        | Samsung 512MB DDR3L K4B4G1646E | <img src="./images/ram.png" width="75%">   | https://www.alldatasheet.com/datasheet-pdf/download/1131836/SAMSUNG/K4B4G1646E.html      |
| EMMC Flash | Toshiba 4GB THGBMDG5D1LBAIL    | <img src="./images/flash.png" width="75%"> | https://www.alldatasheet.com/datasheet-pdf/download/1179334/TOSHIBA/THGBMDG5D1LBAIT.html |

The other ICs are not interesting, they are audio amplifiers, inverter ICs, etc.

We will take a closer look at the flash storages after inspecting the other boards.

## Display Board

The second board's name is `CV500U2-T01-CB-1` and is the board that connects to the display. This display board is the second largest board, and is connected with a ribbon cable to the main board, and with other 2 big connectors to the display.

<div style="display: flex; flex-direction: row; align-items: center;">
  <img src="./images/displayboard_frontside.png" width="30%" style="margin-right: 50px;">
  <img src="./images/displayboard_backside.png" width="30%">
</div>

| Component | Name                                 | Image                                                   | Datasheet                                                                                     |
| --------- | ------------------------------------ | ------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| CPU       | CHOT CCU1221A11N                     | <img src="./images/displayboard_cpu.png" width="75%">   | not available                                                                                 |
| SPI Flash | Boya Microelectronics 2MB 25D16ASSIG | <img src="./images/displayboard_flash.png" width="75%"> | https://www.lcsc.com/datasheet/lcsc_datasheet_2304140030_BOYAMICRO-BY25D16ASSIG-T_C382740.pdf |

## WiFi Board

The third board is named `WTA1`. This board also has an FCCID on the EMI shield. This board is the WiFi/Bluetooth interface of the TV, and the antennas are on the side.

<img src="./images/wifi_board.png" width="30%">

<img src="./images/wifi_board_backside.png" width="30%">


If searching on the [fccid.io](https://fccid.io/2AC23-WTA1) website, we can find some useful info about it. It has a microcontroller, MediaTek MT7638GUN controlling the WiFi/Bluetooth interfaces. [shoutwiki](https://techinfodepot.shoutwiki.com/wiki/MediaTek) has a database of these microcontrollers, since the official Mediatek no longer has it on their website.

The FCC external photos also say which antennas are used for WiFi and which is the Bluetooth antenna.

The PCB has interesting markings on the backside and on the topside, which are USB, used as the default communication protocol, and UART, for programming/debugging, I assume.

## IR Board

The last board's name is RSAG7.820.8441 (I think). This last board can be found seated in the case of the TV, and it is used for controlling the infrared remote, since it also has IR receiver. Also, it has the physical button to turn on the computer.

<img src="./images/ir_board_topside.png" width="20%">

<img src="./images/ir_board_backside.png" width="20%">

Taking a closer look at the pinout, on the PCB are neatly written the pins' role. The board has an I2C bus, LED signal input and keys/infrared outputs. 