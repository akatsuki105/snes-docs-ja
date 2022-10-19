# I/Oレジスタ 一覧

> **Note**  
> 右の列はリセット時の初期値を示します。
> 括弧付きの値はリセット時にはセットされませんが、最初の電源投入時にはセットされます。

>**Note**  
> WO: 書き込み専用, RO: 読み取り専用, RW: 読み書きOK

## PPU

```
  2100h WO - INIDISP - ディスプレイ制御レジスタ1                               8xh
  2101h WO - OBSEL   - Object Size and Object Base                        (?)
  2102h WO - OAMADDL - OAMアドレス (下位8bit)     (?)
  2103h WO - OAMADDH - OAMアドレス (上位1bit)     (?)
  2104h WO - OAMDATA - OAM書き込み                      (?)
  2105h WO - BGMODE  - BG制御レジスタ                                       (xFh)
  2106h WO - MOSAIC  - モザイク                                            (?)
  2107h WO - BG1SC   - BG1画面設定                                         (?)
  2108h WO - BG2SC   - BG2画面設定                                         (?)
  2109h WO - BG3SC   - BG3画面設定                                         (?)
  210Ah WO - BG4SC   - BG4画面設定                                         (?)
  210Bh WO - BG12NBA - BG1,2タイルデータアドレス                              (?)
  210Ch WO - BG34NBA - BG3,4タイルデータアドレス                              (?)
  210Dh WO - BG1HOFS - BG1Xスクロール / M7HOFS                             (?,?)
  210Eh WO - BG1VOFS - BG1Yスクロール / M7VOFS                             (?,?)
  210Fh WO - BG2HOFS - BG2Xスクロール                                      (?,?)
  2110h WO - BG2VOFS - BG2Yスクロール                                      (?,?)
  2111h WO - BG3HOFS - BG3Xスクロール                                      (?,?)
  2112h WO - BG3VOFS - BG3Yスクロール                                      (?,?)
  2113h WO - BG4HOFS - BG4Xスクロール                                      (?,?)
  2114h WO - BG4VOFS - BG4Yスクロール                                      (?,?)
  2115h WO - VMAIN   - VRAMアドレス増加レジスタ                               (?Fh)
  2116h WO - VMADDL  - VRAMアドレス (下位8bit)                     (?)
  2117h WO - VMADDH  - VRAMアドレス (上位8bit)                     (?)
  2118h WO - VMDATAL - VRAMデータ書き込み (下位8bit)                (?)
  2119h WO - VMDATAH - VRAMデータ書き込み (上位8bit)                (?)
  211Ah WO - M7SEL   - Rotation/Scaling Mode Settings                    (?)
  211Bh WO - M7A     - 伸縮回転パラメータA & PPU被乗数レジスタ(FFh)(w2)
  211Ch WO - M7B     - 伸縮回転パラメータB & PPU乗数レジスタ (FFh)(w2)
  211Dh WO - M7C     - 伸縮回転パラメータC (?)
  211Eh WO - M7D     - 伸縮回転パラメータD (?)
  211Fh WO - M7X     - 伸縮回転中心X座標 (?)
  2120h WO - M7Y     - 伸縮回転中心Y座標 (?)
  2121h WO - CGADD   - パレットアドレス                                        (?)
  2122h WO - CGDATA  - パレット書き込み                                 (?)
  2123h WO - W12SEL  - Window BG1/BG2 Mask Settings                       (?)
  2124h WO - W34SEL  - Window BG3/BG4 Mask Settings                       (?)
  2125h WO - WOBJSEL - Window OBJ/MATH Mask Settings                      (?)
  2126h WO - WH0     - Window座標1L                        (?)
  2127h WO - WH1     - Window座標1R                       (?)
  2128h WO - WH2     - Window座標2L                        (?)
  2129h WO - WH3     - Window座標2R                       (?)
  212Ah WO - WBGLOG  - Window 1/2 Mask Logic (BG1-BG4)                    (?)
  212Bh WO - WOBJLOG - Window 1/2 Mask Logic (OBJ/MATH)                   (?)
  212Ch WO - TM      - メイン画面レイヤ制御                                    (?)
  212Dh WO - TS      - サブ画面レイヤ制御                                     (?)
  212Eh WO - TMW     - Window Area Main Screen Disable                    (?)
  212Fh WO - TSW     - Window Area Sub Screen Disable                     (?)
  2130h WO - CGWSEL  - ColorMath制御レジスタA                                (?)
  2131h WO - CGADSUB - ColorMath制御レジスタB                                (?)
  2132h WO - COLDATA - Color Math Sub Screen Backdrop Color               (?)
  2133h WO - SETINI  - ディスプレイ制御レジスタ2                                00h?
  2134h RO - MPYL    - PPU積レジスタ (下位8bit)                           (01h)
  2135h RO - MPYM    - PPU積レジスタ (中位8bit)                          (00h)
  2136h RO - MPYH    - PPU積レジスタ (上位8bit)                           (00h)
  2137h RO - SLHV    - H/Vカウンタラッチ
  2138h RO - RDOAM   - OAM読み込み
  2139h RO - RDVRAML - VRAMデータ読み込み (下位8bit)
  213Ah RO - RDVRAMH - VRAMデータ読み込み (上位8bit)
  213Bh RO - RDCGRAM - パレット読み込み
  213Ch RO - OPHCT   - Hカウンタ                       (01FFh)
  213Dh RO - OPVCT   - Vカウンタ                       (01FFh)
  213Eh RO - STAT77  - PPU1ステータス
  213Fh RO - STAT78  - PPU2ステータス               Bit7=0
```

## APU

```
  2140h RW - APUI00  - Main CPU to Sound CPU Communication Port 0        (00h/00h)
  2141h RW - APUI01  - Main CPU to Sound CPU Communication Port 1        (00h/00h)
  2142h RW - APUI02  - Main CPU to Sound CPU Communication Port 2        (00h/00h)
  2143h RW - APUI03  - Main CPU to Sound CPU Communication Port 3        (00h/00h)
  2144h..217Fh    - APU Ports 2140-2143h mirrored to 2144h..217Fh
```

## WRAM

```
  2180h RW - WMDATA  - WRAMデータレジスタ
  2181h WO - WMADDL  - WRAMアドレスレジスタ (下位8bit)  (W)                   00h
  2182h WO - WMADDM  - WRAMアドレスレジスタ (中位8bit) (W)                   00h
  2183h WO - WMADDH  - WRAMアドレスレジスタ (上位1bit)  (W)                   00h
  2184h..21FFh    - 不使用(Open Bus) / Expansion (B-Bus)                 -
  2200h..3FFFh    - 不使用(Open Bus) / Expansion (A-Bus)                 -
```

## CPU

**低速(1.78MHz)**

```
  4000h..4015h        - 不使用(Open Bus)   -
  4016h WO  - JOYWR - Joypad出力       00h
  4016h RO  - JOYA  - Joypad入力A    -
  4017h RO  - JOYB  - Joypad入力B   -
  4018h..41FFh        - 不使用(Open Bus) -
```

**高速(3.58MHz)**

```
  4200h WO - NMITIMEN- 割り込み有効化レジスタ                   00h
  4201h WO - WRIO    - Joypad Programmable I/O Port (Open-Collector Output)  FFh
  4202h WO - WRMPYA  - 被乗数レジスタ                        (FFh)
  4203h WO - WRMPYB  - 乗数レジスタ (FFh)
  4204h WO - WRDIVL  - 被除数レジスタ (下位8bit)              (FFh)
  4205h WO - WRDIVH  - 被除数レジスタ (上位8bit)              (FFh)
  4206h WO - WRDIVB  - 除数レジスタ          (FFh)
  4207h WO - HTIMEL  - HIRQ座標 (下位8bits)                          (FFh)
  4208h WO - HTIMEH  - HIRQ座標 (上位1bit)                           (01h)
  4209h WO - VTIMEL  - VIRQ座標 (下位8bits)                          (FFh)
  420Ah WO - VTIMEH  - VIRQ座標 (上位1bit)                           (01h)
  420Bh WO - MDMAEN  - GDMAチャネルレジスタ 0
  420Ch WO - HDMAEN  - HDMAチャネルレジスタ                    0
  420Dh WO - MEMSEL  - WS2制御レジスタ                              0
  420Eh..420Fh    - 不使用(Open Bus)                                 -
  4210h RO - RDNMI   - NMIフラグ (Read/Ack)                                      0xh
  4211h RO - TIMEUP  - H/VタイマーIRQフラグ                                        00h
  4212h RO - HVBJOY  - H/VBlankフラグ & 自動Joypadビジーフラグ (R)                 (?)
  4213h RO - RDIO    - Joypad Programmable I/O Port (Input)                   -
  4214h RO - RDDIVL  - 商レジスタ (下位8bit)                                   (0)
  4215h RO - RDDIVH  - 商レジスタ (上位8bit)                                   (0)
  4216h RO - RDMPYL  - 積/余りレジスタ (下位8bit)                                
  4217h RO - RDMPYH  - 積/余りレジスタ (上位8bit)                                
  4218h RO - JOY1L   - Joypad1レジスタ (下位8bit)                              00h
  4219h RO - JOY1H   - Joypad1レジスタ (上位8bit)                              00h
  421Ah RO - JOY2L   - Joypad2レジスタ (下位8bit)                              00h
  421Bh RO - JOY2H   - Joypad2レジスタ (上位8bit)                              00h
  421Ch RO - JOY3L   - Joypad3レジスタ (下位8bit)                              00h
  421Dh RO - JOY3H   - Joypad3レジスタ (上位8bit)                              00h
  421Eh RO - JOY4L   - Joypad4レジスタ (下位8bit)                              00h
  421Fh RO - JOY4H   - Joypad4レジスタ (上位8bit)                              00h
  4220h..42FFh    - 不使用(Open Bus)                                         -
```

## DMA

x = DMAチャンネル番号(0..7)

```
  (additional DMA control registers are 420Bh and 420Ch, see above)
  43x0h RW - DMAPx   - DMA設定レジスタ                                   (FFh)
  43x1h RW - BBADx   - DBバスアドレス          (FFh)
  43x2h RW - A1TxL   - Aバスアドレス (low) (FFh)
  43x3h RW - A1TxH   - Aバスアドレス (high)(FFh)
  43x4h RW - A1Bx    - Aバスアドレス (bank)(xxh)
  43x5h RW - DASxL   - Indirect HDMA Address (low)  / DMA Byte-Counter (low) (FFh)
  43x6h RW - DASxH   - Indirect HDMA Address (high) / DMA Byte-Counter (high)(FFh)
  43x7h RW - DASBx   - Indirect HDMA Address (bank)                          (FFh)
  43x8h RW - A2AxL   - HDMA Table Current Address (low)                      (FFh)
  43x9h RW - A2AxH   - HDMA Table Current Address (high)                     (FFh)
  43xAh RW - NTRLx   - HDMA Line-Counter (from current Table entry)          (FFh)
  43xBh RW - UNUSEDx - 不使用                       (FFh)
  43xCh+  -         不使用(Open Bus)                                       -
  43xFh RW - MIRRx   - 43xBhのミラー (R/W)                                     (FFh)
  4380h..5FFFh    - 不使用(Open Bus)                                       -
```