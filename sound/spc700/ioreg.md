# IOレジスタ

## 00F0h - TEST - Testing functions (W)

```
  Bit 0    Timer-Enable     (0=Normal, 1=Timers don't work)
  Bit 1    RAM Write Enable (0=Disable/Read-only, 1=Enable SPC700 & S-DSP writes)
  Bit 2    Crash SPC700     (0=Normal, 1=Crashes the CPU)
  Bit 3    Timer-Disable    (0=Timers don't work, 1=Normal)
  Bit 4-5  Waitstates on RAM Access         (0..3 = 0/1/4/9 cycles) (0=Normal)
  Bit 6-7  Waitstates on I/O and ROM Access (0..3 = 0/1/4/9 cycles) (0=Normal)
```

Default setting is 0Ah, software should never change this register. Normal memory access time is 1 cycle (adding 0/1/4/9 waits gives access times of 1/2/5/10 cycles). Using 4 or 9 waits doesn't work with some opcodes (0 or 1 waits seem to work stable).

Internal cycles (those that do not access RAM, ROM, nor I/O) are either using the RAM or I/O access time (see notes at bottom of this chapter).

## 00F1h - CONTROL - Timer, I/O and ROM Control (W)

リセット時には `0xB0` になります。

```
  Bit 0-2  Timer 0-2 Enable (0=Disable, set TnOUT=0 & reload divider, 1=Enable)
  Bit 3    不使用
  Bit 4    Reset Port 00F4h/00F5h Input-Latches (0=No change, 1=Reset to 00h)
  Bit 5    Reset Port 00F6h/00F7h Input-Latches (0=No change, 1=Reset to 00h)
           Note: The CPUIO inputs are latched inside of the SPC700 (at time when the Main CPU writes them), above two bits allow to reset these latches.
  Bit 6    不使用
  Bit 7    ROM at FFC0h-FFFFh (0=RAM, 1=ROM) (writes do always go to RAM)
```

## 00F2h - DSPADDR - DSPレジスタ番号 (R/W)

```
  Bit 0-7  DSPレジスタ番号 (usually 00h..7Fh) (80h..FFh are read-only mirrors)
```

## 00F3h - DSPDATA - DSPレジスタデータ (R/W)

```
  Bit 0-7  DSPレジスタデータ (read/write the register selected via Port 00F2h)
```

## 00Fxh - CPUIOn - CPU Input and Output Register n (R/W)

```
  00F4h - CPUIO0
  00F5h - CPUIO1
  00F6h - CPUIO2
  00F7h - CPUIO3
```

これらのレジスタはスーファミ本体のCPU(S-CPU)との通信に使用されます。

S-CPUへの出力ポート(書き込み専用)が4つ、S-CPUからの入力ポート(読み出し専用)が4つ、合計8つのレジスタがこの4つのアドレスでアクセスされます。

```
  Bit 0-7   データ
    APU(SPC700)目線で見て
      Write: APU   -> S-CPU
      Read:  S-CPU -> APU
```

If the SPC700 writes to an output port while the S-CPU is reading it, the S-CPU will read the logical OR of the old and new values. Possibly the same thing happens the other way around, but the details are unknown?

## 00Fxh - AUXIOn - External I/O Port Pn (R/W)

使用されてはいませんでした。

```
  00F8h - AUXIO4 (S-SMP Pins 34-27)
  00F9h - AUXIO5 (S-SMP Pins 25-18)
```

Writing changes the output levels. Reading normally returns the same value as the written value; unless external hardware has pulled the pins LOW or HIGH. Reading from AUXIO5 additionally produces a short /P5RD read signal (Pin17).

```
  Bit 0-7  Input/Output levels (0=Low, 1=High)
```

In the SNES, these pins are unused (not connected), so the registers do effectively work as if they'd be "RAM-like" general purpose storage registers.

## 00Fxh - TnDIV - Timer n Divider (W)

```
  00FAh - T0DIV (for  8000Hz clock source)
  00FBh - T1DIV (for  8000Hz clock source)
  00FCh - T2DIV (for 64000Hz clock source)
```

```
  Bit 0-7  Divider (01h..FFh=Divide by 1..255, or 00h=Divide by 256)
```

If timers are enabled (via Port 00F1h), then the TnOUT registers are incremented at the selected rate (ie. the 8kHz or 64kHz clock source, divided by the selected value).

## 00Fxh - TnOUT - Timer n Output (R)

```
  00FDh/00FEh/00FFh - T0OUT/T1OUT/T2OUT
```

```
  Bit 0-3  Incremented at the rate selected via TnDIV (reset to 0 after reading)
  Bit 4-7  不使用 (常に0)
```


