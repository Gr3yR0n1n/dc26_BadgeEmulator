# DC26 Badge Emulator
---
The badges use a stripped down version of wireengineer's 
[Badge Bus protocol](https://github.com/Wireb/badge_bus/wiki).

### Header structure

Section  | Size in bytes
:---: | :---:
Preamble | 2
Packet Start | 2
Status | 1
Command | 1
Payload Len | 1
Payload | Varies
CRC16 | 2

The communication begins with two `0x55 (0b1010101)` bytes -- a preamble
which is used for baud rate detection (if supported). The following two
bytes `0x4242` indicate the start of the packet.

The DC26 badge protocol only uses two statuses:

   * `0x80 (0b10000000)` -- Checksum is valid; Request. This packet will carry
     no payload.

   * `0xC0 (0b11000000)` -- Checksum is valid; Response. This packet carries a
     payload identifying the originator.

The command field is always `0xAF` and has no apparent purpose. This field is 
left pretty flexible by the specification, see the wiki page for more info.

The header ends with a byte long length of the payload. As noted above only one
of the two packet types (statuses) carries a payload. The payload is always five bytes so 
this field will be `0x00` for the requests and `0x05` for the responses.

### Payload Structure

The first three bytes of the payload are `0x000100`. It is unclear to me at the 
moment what these bytes really do, we weren't able to capture any traffic where 
these bytes differed.

The next two bytes represent the badge type and the badge state.

Badge Type | Value
:---: | :---:
Human | 1
Contest | 2
Goon | 3
Artist | 4
Press | 5
Vendor | 6
Speaker | 7
CFP | 8

Only two badge states were captured over the wire:
  * `0x00` -- neutral/red.
  * `0x01` -- happy/green.

The packet ends with two bytes -- CRC16 checksum of the packet (starting from
the `0x42` byte).

### Using Arduino

We use the `D3` pin for the end of cycle LED, but this is just preference. The only 
thing you need to connect is the `Tx` pin of the Arduino to the `Rx` pin of your badge. 
Your defcon badge runs on 3.3V. If you're using Arduino Nano or Uno (or anytother 5V
board) for this project, you will have to build a voltage divider for the output pin (`Tx`).

Further instructions (with the voltage divider) coming soon!
