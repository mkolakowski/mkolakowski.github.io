---
title: 'EZ Flash Cheat Decoding'
collection: posts
date: 2024-04-24
permalink: /posts/2024-04-24-EZ-Flash-Cheat-Decoding
tags:
  - Nintendo
  - GBA
  - EZ-Flash
---


https://www.reddit.com/r/Gameboy/comments/hcq2g6/ezflash_omega_cheat_system_tutorial/k55cvss/
From Reddit


For anyone in 2023 who wants to understand how the conversion works...

Here is the example code given by OP:
```
CA1D8DB3 689FDAFA
9CC263F2 68DE0537
1DCAC2C5 4FCA9184
```

This can be converted by following these steps:

1. Go to https://gamehacking.org/system/gba
2. Set "Devices" to "Action Replay MAX"
3. Check "Encrypted"
4. Paste the code into the large right section labeled "Code"
5. Click "Process Code"

This should produce an explanation like OP presented, with lines such as

```
Writes 0x037D to 0x200B578.
```

To convert this to the EZFlash Omega format, do the following:

1. Convert the address:

    ~0x200~ B578

2. Convert the value:

    ~0x~ 03 7D -> 7D, 03

3. Add them together:

    B578, 7D, 03

Doing the same with the 3rd line and adding them together in the .cht file yields the following, as shown by OP:

```
[Debug]
ON=B578,7D,03;B57C,F8,00,A8,00

[GameInfo]
Name=2377 - Mother 3 (J)
System=GBA
Text=vicente
Hope this helps someone
```


---

How to read Cheat codes:

Memory Writes:
```
0XXXXXXX YYYYYYYY – 32bit write to [XXXXXXX + offset]
1XXXXXXX 0000YYYY – 16bit write to [XXXXXXX + offset]
2XXXXXXX 000000YY – 8bit write to [XXXXXXX + offset]
```

Conditional 32bit codes:
```
3XXXXXXX YYYYYYYY – Greater Than (YYYYYYYY > [XXXXXXX + offset])
4XXXXXXX YYYYYYYY – Less Than (YYYYYYYY < [XXXXXXX + offset])
5XXXXXXX YYYYYYYY – Equal To (YYYYYYYY == [XXXXXXX + offset])
6XXXXXXX YYYYYYYY – Not Equal To (YYYYYYYY != [XXXXXXX + offset])
```

Conditional 16bit deref + write codes:
```
7XXXXXXX ZZZZYYYY – Greater Than
8XXXXXXX ZZZZYYYY – Less Than
9XXXXXXX ZZZZYYYY – Equal To
AXXXXXXX ZZZZYYYY – Not Equal To
```

Offset Codes:
```
BXXXXXXX 00000000 – offset = *(xxx)
D3000000 XXXXXXXX – set offset to immediate value
DC000000 XXXXXXXX – Adds an value to the current offset
```

Loop Code:
```
C0000000 YYYYYYYY – Sets the repeat value to ‘YYYYYYYY’
D1000000 00000000 – Loop execute
D0000000 00000000 – Terminator code
```

Data Register Codes:
```
D4000000 XXXXXXXX – Adds XXXXXXXX to the data register
D5000000 XXXXXXXX – Sets the data register to XXXXXXXX
D6000000 XXXXXXXX – (32bit) [XXXXXXXX+offset] = data ; offset += 4
D7000000 XXXXXXXX – (16bit) [XXXXXXXX+offset] = data & 0xffff ; offset += 2
D8000000 XXXXXXXX – (8bit) [XXXXXXXX+offset] = data & 0xff ; offset++
D9000000 XXXXXXXX – (32bit) sets data to [XXXXXXXX+offset]
DA000000 XXXXXXXX – (16bit) sets data to [XXXXXXXX+offset] & 0xffff
DB000000 XXXXXXXX – (8bit) sets data to [XXXXXXXX+offset] & 0xff
```

Special Codes:
```
DD000000 XXXXXXXX – if KEYPAD has value XXXXXXXX execute next block
```

Special Keypad Code
As for the Special KEYPAD cheat code, the keypad value can be any combination of the following:

```
0x1     A
0x2     B
0x4     Select
0x8     Start
0x10    Right
0x20    Left
0x40    Up
0x80    Down
0x100   R
0x200   L
0x400   X
0x800   Y
```
