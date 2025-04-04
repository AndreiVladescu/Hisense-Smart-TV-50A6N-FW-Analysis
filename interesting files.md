
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

