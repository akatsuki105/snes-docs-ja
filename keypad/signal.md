# [キー入力](https://problemkaputt.de/fullsnes.htm#snescontrollersioportsautomaticreading)

コントローラーデバイスからの信号単位の入出力です。

## 4016h - JOYWR/JOYA - Joypad出力/入力A (R/W)

Read と Write で用途が変わります。

```
Read:  JOYA(Joypad入力A)
  Bit 0    CPU の Pin 32 からの入力, connected to gameport 1, pin 4 (JOY1) (1=Low)
  Bit 1    CPU の Pin 33 からの入力, connected to gameport 1, pin 5 (JOY3) (1=Low)
  Bit 2-7  不使用

  このレジスタ(JOYA)を読み出すと自動的にCPUの35番ピンがクロックパルスを発生し、ゲームポート1の2番ピンと接続されます。

Write: JOYWR(Joypad出力)
  Bit 0    OUT0, CPU の Pin 37 へ出力される (Joypad Strobe) (both gameports, pin 3)
  Bit 1    OUT1, CPU の Pin 38 へ出力される (実際はされない) (1=High)
  Bit 2    OUT2, CPU の Pin 39 へ出力される (実際はされない) (1=High)
  Bit 3-7  不使用

  Out0-2 は 仕様上は CPU の Pins 37-39 に接続されているはずですが、実際には Out0 しか繋がってないようです
```

## 4017h - JOYB - Joypad入力B (R)

```
  Bit 0    Input on CPU Pin 27, connected to gameport 2, pin 4 (JOY2) (1=Low)
  Bit 1    Input on CPU Pin 28, connected to gameport 2, pin 5 (JOY4) (1=Low)
  Bit 2    Input on CPU Pin 29, connected to GND (always 1=LOW)       (1=Low)
  Bit 3    Input on CPU Pin 30, connected to GND (always 1=LOW)       (1=Low)
  Bit 4    Input on CPU Pin 31, connected to GND (always 1=LOW)       (1=Low)
  Bit 5-7  不使用
```

Reading from this register automatically generates a clock pulse on CPU Pin 36, which is connected to gameport 2, pin 2.

## 4201h - WRIO - Joypad Programmable I/O Port (Open-Collector Output) (W)

```
  Bit 0-7   I/O PORT  (0=Output Low, 1=HighZ/Input)
  Bit 0-5   Not connected (except, used by SFC-Box; see Hotel Boxes)
  Bit 6     Joypad 1 Pin 6
  Bit 7     Joypad 2 Pin 6 / PPU Lightgun input (should be usually always 1=Input)
```

Note: Due to the weak high-level, the raising "edge" is raising rather slowly, for sharper transitions one may need external pull-up resistors.

## 4213h - RDIO - Joypad Programmable I/O Port (Input) (R)

```
  Bit 0-7   I/O PORT  (0=Low, 1=High)
```

When used as Input via 4213h, set the corresponding bits in 4201h to HighZ.

I/O Signals 0..7 are found on CPU Pins 19..26 (in that order). IO-6 connects to Pin 6 of Controller 1. IO-7 connects to Pin 6 of Controller 2, this pin is also shared for the light pen strobe.

Wires are connected to IO-0..5, but the wires disappear somewhere in the multi-layer board (and might be dead-ends), none of them is output to any connectors (not to the Controller ports, not to the cartridge slot, and not to the EXT port).
