# [VRAMへのアクセス](https://problemkaputt.de/fullsnes.htm#snesmemoryvramaccesstileandbgmap)

このページでは、VRAMにアクセスするためのレジスタについて解説します。

VRAM自体の内容については[こちら](../video/vram.md)を参照。

## 🔰 概要

```
            High  : Low
  Write:    2119h : 2118h
  Read:     213Ah : 2139h
  Address:  2117h : 2116h
```

## 🗓 2115h - VMAIN - VRAMアドレス増加レジスタ (W)

```
  Bit 0-1 VMADDのインクリメント量(word = 2byte単位)
            0b00: 1 word
            0b01: 32 word
            0b10: 64 word
            0b11: 128 word
  Bit 2-3 アドレス変換 (後述)
            0b00: 0bit回転(アドレス変換なし)
            0b01: 8bit回転
            0b10: 9bit回転
            0b11: 10bit回転
  Bit 4-6 不使用
  Bit 7   上位/下位バイトのどちらにアクセスした後、VRAMアドレスをインクリメントするか
            0: 下位バイト(0x2118 or 0x2139)
            1: 上位バイト(0x2119 or 0x213A)
```

<details>
  <summary>アドレス変換機能について</summary>
  
アドレス変換機能は、VRAMにアクセスする際に`VMADD`で指定したVRAMアドレスに一時的に適応される変換処理です。

アドレス変換はビットマップ式を対象としており、技術的にはワードアドレスの下位8,9,10bitを3bitだけ左回転させます。

変換方法 | Bitmap Type | `Port [2116h/17h] --> VRAM Word-Address`
-- | -- | -- 
8bit rotate  | 4-color; 1 word/plane    | `aaaaaaaaYYYxxxxx --> aaaaaaaaxxxxxYYY`
9bit rotate  | 16-color; 2 words/plane  | `aaaaaaaYYYxxxxxP --> aaaaaaaxxxxxPYYY`
10bit rotate | 256-color; 4 words/plane | `aaaaaaYYYxxxxxPP --> aaaaaaxxxxxPPYYY`

`aaaaa`は通常のアドレスのMSB、`YYY`は8x8タイル内のYインデックス、`xxxxx`は1ラインあたり32タイルのうちの1つを選択、`PP`はビットプレーンのインデックス（1プレーンに複数のWordがあるBGの場合）です。

256ピクセルの行を書きたいなら、アドレス変換する際には`Increment Step=1`と組み合わせる必要があります。

Mode7のビットマップの場合、最終的には32/128ステップ(bit1-0)と8bit/10bit回転(bit3-2)の組み合わせになります。

```
  8bit-rotate/step32   aaaaaaaaXXXxxYYY --> aaaaaaaaxxYYYXXX
  10bit-rotate/step128 aaaaaaXXXxxxxYYY --> aaaaaaxxxxYYYXXX
```

しかし、SNESはMode7ビットマップで画面全体を描画するのに十分大きなVRAMを持っていません。

アドレス変換なしの32ステップはBGマップの列を更新するのに便利です。(例: X方向のスクロール後)
</details>

## 📮 2116h/2117h - VMADD - VRAMアドレス(W)

```
  2116h: VMADDL
  2117h: VMADDH
```

VRAMの読み出し/書き込み用アドレスを指定するレジスタです。これはワード単位（2バイト単位）で指定します。つまり、実際のアドレスは`2 * VMADD`です。

理論上、最大128KB(64Kワード)までアドレス指定できますが、スーファミ本体にVRAMは64KB(32Kワード)しか搭載されていません。 そのため、VMADD.15は不使用で0扱いなので、`8000h..FFFFh`は`0..7FFFh`のミラーリングとなります。

VRAMデータの読み出し/書き込み後、`VMAIN(2115h)`のインクリメントモードにより、ワードアドレス(`VMADD`)が 1,32,128ずつ自動的にインクリメントされることがあります。

注: アドレス変換機能は、メモリアクセス時に「一時的に」適用されるだけで、ポート`2116h..2117h`の値には影響を与えません。

`2116h/2117h`への書き込みは、後で読み出すために新しいアドレスから16bitデータをプリフェッチします。

## 🖌 2118h/2119h - VMDATA - VRAM書き込み (W)

```
  2118h: VMDATAL
  2119h: VMDATAH
```

`2118h`または`2119h`への書き込みは、現在(アドレス変換を適用した)アドレスで指定されているVRAMのLSBまたはMSBを変更するだけです。VMAINのインクリメントモードにより、書き込み後、アドレスは自動的にインクリメントされます。

## 🔍 2139h/213Ah - RDVRAM - VRAM読み込み (R)

```
  2139h: RDVRAML
  213Ah: RDVRAMH
```

これらのレジスタから読み取りを行うと、内部の16bitプリフェッチレジスタのLSBまたはMSBが返されます。VMAINのインクリメントモードによって、読み出し後にアドレスが自動的にインクリメントされます。

プリフェッチレジスタは、次の2つの場合に、VMADDL/VMADDHでアドレス指定されているVRAMワード（オプションのアドレス変換が適用されている）からデータで満たされます。

```
プリフェッチが行われる2つのパターン:
  2116h/17hへの書き込みによってVRAMアドレスが変化したとき
  2139h/3Ahからの読み取りでVRAMアドレスがインクリメントされたとき
```

`Prefetch BEFORE Increment`は一種のハードウェアグリッチで、基本的に`Prefetch AFTER Increment`のほうが有用です。

Increment/Prefetch の処理は

```
  1. 古いプリフェッチからの1バイトをCPUに送る                     ;-this always
  2. 古いVRAMアドレスから新しい値をプリフェッチレジスタにロード      ;\these only if
  3. VRAMアドレスをインクリメントして新しいアドレスにする           ;/increment occurs    
```

`2118h/19h`への書き込みでインクリメントが起きた場合、プリフェッチは行われません。(当然、内部のプリフェッチレジスタも変わりません)

>**Note**  
> （`2116h/17h`経由で）VRAMアドレス変更後、最初のアクセス時はVRAMアドレスはインクリメントされません。そのため、1度ダミーの読み出しをする必要があります。
