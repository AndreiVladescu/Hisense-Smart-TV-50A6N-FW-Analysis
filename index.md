
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

The third board is named `WTA1`. This board also has an FCCID on the EMI shield. This board is the WiFi/Bluetooth interface of the TV, and the patch antennas are on the side.

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

# Flash Analysis

The flash contents of these boards was extracted using a flash reader, an XGecu T48. 

## Display board flash

The first flash chip I analyzed is the flash chip, since it is much smaller, and easier to read, since it's an SOP8, as opposed to the BGA153 package of the main board's EMMC memory. The flash contents for the display board can be found in the GitHub [repo](https://github.com/AndreiVladescu/Hisense-Smart-TV-50A6N-FW-Analysis/blob/main/BY25D16AS%40SOP8-150.BIN.bin).

<img src="./images/dispalyboard_flash_entropy.png" width="40%">

Most of the memory is empty, and the contents that are not empty are semi-readable, and it looks like a logging memory.

<img src="./images/demura_flash_logs.png" width="20%">

Searching for "demura" on the internet yields some info about the [Demura display calibration technology](https://patents.google.com/patent/CN114495815A/en), which improves the quality of the display - in other words, boring for us. It must contain some factory calibration data.

## Menu board flash

The other memory was harded to acquire, since it's in a BGA153 format. This requires chip-off extraction, with a hot air gun. I didn't bother to reflow it to the PCB again.
If you want to analyze it further, you can try at [this](https://mega.nz/file/V9oEkawL#DQxrA4hBd0lGLq-SNMdKyOvNabxtXYXepjSVROQVD8o) Mega upload link, since it's almost 1.5GB in size.

I unpacked the firmware using `binwalkv3`, since the firmware is big, and original `binwalk` takes a long time. I found lots of partitions, so I decided to use `emba` to automatically analyze it in parallel with me, since it will take some time (it took 20 hours, in retrospect) to analyze the firmware.

### Manual findings

I entered manually every partition on the board, and opened every file of this Linux distribution (didn't find any Android reference), and compiled a list with interesting files:

\2A722000\rootfs\Database

- customer.db, factory.db, user_setting.db -sqlite3 databases
- no personal files, only config files for TV settings

---

\2AF22000\rootfs\config

- ----- 1/17/2025 7:17 PM 1510 critical.ini
------ 1/17/2025 7:17 PM 1510 critical.ini_bak
------ 1/17/2025 7:17 PM 913 system.ini

[critical.ini](attachment:57c0409e-6d2f-4eae-b4a6-e61dc73d2ffe:critical.ini)

- has deviceID, country codes, chipVersion, etc

---

\2B722000\rootfs

- has lots of .ini files for TV characteristics
- Has a hotelmode, for hotels
    
    [board.ini](attachment:e1385f57-3354-4d80-b5e0-2cc165d708ec:board.ini)
    

---

\2D522000\rootfs

- SWVER: 2.99.0.0, SVNREF: unknown, DEV; BUILD BY: gfkfcmo@5c2639022883

.\scripts

lots of .sh files:

[Bash file scripts](https://www.notion.so/Bash-file-scripts-1a978968766f80a08fb8dd43879a2c93?pvs=21)

.\misc

- nvram binary blobs

.\lib

- lots of libraries

[Libraries](https://www.notion.so/Libraries-1a978968766f808b8e0dc265623ec70f?pvs=21)

---

\3C523000\rootfs

- misc files, dongle support for integrated USB network chip

.\webengine

- some libraries and elf files

.\sraf

- 3rd party apps json configs, Googlemaps, twitter, yahooweather, facebook, etc
- binaries and libraries for “sraf”
- some images for browser

.\remoteapp

- certificates + private keys
- mosquitto MQTT configuartion file

.\mwhb_engine

- has binaries, libraries, scripts and a sqlite3 database for sraf → SRAF is a browser
- database has some entries for a few website, like Yahoo, UEFA, Wikipedia + their images in b64

.\hisenseUI

- is the GUI for the TV

.\cobalt

- has some interesting key.bin file
- Lots of certificates
- cobalt and cobalt_kids executables

.\avsservice\conf

- alexa services?
- lots of certs
- crypto libs

---

\7A323000\rootfs

- MAC.bin - 6 bytes, just like a mac; bytes are `00 30 1b ba 02 db`
- 2 x HDCP keys
- FVP private keys
- some more binary keys, 1 x YouTube

---

\7A724000\rootfs

- servicelist.db sqlite3 database → has TV Channel names, Services
- device_serial.xml → device serial code `93146cba96e8e3f2c3c8cd726969ec02`

---

\7B724000\rootfs

- has TV configs
- has a reference to UART:

[FactorySrvData]
ComEnable        = 0
UARTOnOff        = 1
FacUpgradeState  = 0
PreChannelIndex  = 0

---

\7BB24000\rootfs

- has lots of files for storing user color preferences, sound, etc

.\common\market\vendor\EU\ir

- ir_config.ini → the config file for interacting with the IR module, has a keymap
- lots of images

---

\22F22000\rootfs

- has set_env file:
    
    [set_env](attachment:ca354a3a-0106-476e-988e-21a1573b03e6:set_env.txt)
    
- dhcp6c and udhcpc scripts
- could be the Bootloader
- lots of .ini files

---

\64F23000\rootfs

- has bluetooth & bluedroid config files
- has the Wifi Password at \wifi\wpa_supplicant
    
    ---
    

\2041000 and \3041000

- has a linux-4.9.44+.bin file → Kernel

---

\4041000\squashfs-root

- seems like a normal linux / root
- has a busybox
- shadow file contents:

root:$6$$snqgU0rzIjnACGt0WLHnFNfyX6UmOUwHLdwCa8KQ/vaS1RbpBd1L.u2fCjJc5IjAdm/hPcGRcLS1YySKUCJxt/::0:99999::::
television:$6$$rWgo9VQeM37sx0mlTh9x1JjvyDtLE1IwyK4wBw/bGUBMJkpnX0xGWBXnNVpVD8.SygWYyQWDuOXOoYeE9krV81::0:99999::::

- has libgcc in /lib

---

\6441000\squashfs-root

- has another linux root fs
- looks similar to last one, but no shadow
- could be a backup partition

---

\24322000\rootfs

- has some cert files
- ECDSA appboot.key

ECDSA:MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEKnN/+KcbYGA+QnpDgPeZfZqIK9vwPDRMqa4PLeg5Z77E1k3NZwrHK3SvPHoCjAk6XAknJZIkYCgRmZhEQcB//w==

- ECDSA appboot.key.sdk

ECDSA:MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAELn5TGw619DfHUxPNPjO5i+X9Tybqm11dpZarekbL4TJI7IHeh+5gPWc7uQ/b76wfcGZZs6IAY8aAriu/gOH5cA==

- has a network saved in .\customer\misc\wifi\p2p_supplicant.conf
- has the IP address of the DHCP server in .\customer\miracast\dhcp\dhcp_server.addr:

192.168.49.1

---

\85124000\rootfs

- X.509 certificates for amazon and youtube servers
- sraf private  RSA key

---

\F861000

- looks like a partition for interrupts of the CPU

---

\F921000\rootfs

- configs and binaries for network/wifi
- internet services config
- some binaries for these services

