# 描画サイクル

> **Note**
> サイクルはマスターサイクルのことを指します。

```
   0                                    256           341
    ┌────────────────────────────────────┬─────────────┐
    │                                    │             │
    │                                    │             │
    │                                    │             │
    │                                    │             │
    │                                    │             │
    │               Drawing              │   HBlank    │
    │                                    │             │
    │                                    │             │
    │                                    │             │
    │                                    │             │
224 ├────────────────────────────────────┴─────────────┤
    │                                                  │
    │                    VBlank                        │
    │                                                  │
262 └──────────────────────────────────────────────────┘
```

スーファミの1スキャンラインは1364サイクルで、1フレームは262スキャンラインです。(`21.442080 MHz/(262*1364) = 60 Hz`)

また1スキャンラインごとに、40サイクルをDRAMのリフレッシュに費やすため、CPUは1スキャンラインあたり1324サイクルだけ実行可能です。

HBlank<sup>[1](#hblank)</sup>は `H=256..341` までの間で、VBlank<sup>[2](#vblank)</sup>は `V=225..262` の間です。

FBlank中 の場合を除いて HBlank または VBlank の間しかVRAMにアクセスすることはできません。

スーファミのゲームでは、フレームの描画中(`V=0..224`)にキャラクターの移動や衝突判定、敵の行動などの入力を受けて処理し、VBlank中に新しいデータをVRAMにコピーするというのが一般的でした。

```
  要調査:
    スーファミは縦解像度を 224 lines or 239 lines のどちらかで選択可能だった？(それによってVBlankの期間が 38 lines or 23 lines と変わってきた。ただし、VBlank期間は貴重なのでほとんどのゲームは224だったっぽい)
```

<sup id="hblank">1: アナログTVの電子ビームが画面の左側に戻る時間を稼ぐために描画を無効化すること</sup>

<sup id="vblank">2: アナログTVが描画を無効にして、TVの電子ビームが一旦画面の下に到達してから左上に戻る時間を確保すること</sup>

## 2137h - SLHV - H/Vカウンタラッチ (R)

```
  Bit 0-7  不使用 (CPU Open Bus; usually last opcode, 21h for "MOV A,[2137h]")
```

このレジスタからリードしたときに、現在のH/Vカウンタが OPHCT/OPVCT にラッチ(セット)されます。

Hカウンタは 0..339 の範囲の値を取り、22..277 が画面に表示されます。Vカウンタは 0..261 の範囲の値を取り、1..224 の範囲が画面に表示されます。

Reading here works "as if" dragging IO7 low (but it does NOT actually output a LOW level to IO7 on joypad 2).

## 213Ch/213Dh - OPHCT/OPVCT - H/Vカウンタ (R)

`WRIO.7`がセットされているときに、以下のどれかが起こったときにH/Vカウンタがラッチ(セット)されます。

```
  * ソフトウェアによる SLHV(2137h) からの(ダミー)リード
  * ソフトウェアによって WRIO.7 が 1 から 0 になったとき
  * Lightgun が High から Low になったとき
```

データがラッチされると、STAT78.6 のラッチフラグがセットされます。

```
  1回目のRead: 下位8bit
  2回目のRead: 上位1bit (other 7bit PPU2 open bus; last value read from PPU2)
```

There are two separate 1st/2nd-read flipflops (one for OPHCT, one for OPVCT), both flipflops can be reset by reading from Port 213Fh (STAT78), the flipflops aren't automatically reset when latching occurs.
