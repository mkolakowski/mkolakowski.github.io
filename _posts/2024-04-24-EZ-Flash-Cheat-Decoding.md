
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
