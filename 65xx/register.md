# [レジスタ](https://problemkaputt.de/fullsnes.htm#cpuregistersandflags)

スーファミのCPUは汎用レジスタとして使えるレジスタはA,X,Yの最大3個だけです。

しかし、レジスタの数が限られているというデメリットも、快適なでカバーできる部分もあります。

特にメモリのページ0（アドレス`0000h..00FFh`）は比較的高速で複雑な操作が可能であるため、実質的に、CPUはメモリ内に約256個の8bitレジスタ（または128個の16bitレジスタ）を持っていると言えるでしょう。

詳細を知りたい場合は、[アドレッシング](addressing.md)を参照してください。

## 👾 レジスタ

 bit幅 | 名前 | 内容
---- | ---- | ---- 
8/16  | A   | アキュムレータ
8/16  | X   | インデックスレジスタX
8/16  | Y   | インデックスレジスタY
16    | PC  | プログラムカウンタ
8/16  | S   | スタックポインタ
8     | P   | ステータスレジスタ
16    | D   | ゼロページオフセット(8bit`[nn]`を16bit`[00:nn+D]`に拡張する)
8     | DB  | データバンク(16bit`[nnnn]`を24bit`[DB:nnnn]`に拡張する)
8     | PB  | プログラムカウンタバンク(16bit`PC`を24bit`PB:PC`に拡張する)

## 📚 スタックポインタ

スタックポインタは、メモリのページ1の256バイトを指定できます。つまり、`S=0xNN`のとき、スタックのトップは`0x01NN`となります。

スタックは他の多くのCPUと同様、上に伸びていきます。つまりPUSHでデクリメントされ、POPでインクリメントされます。

また、スタックポインタは最初のFREEバイトを指しているので、スタックをトップに初期化する際には、`S=(2)00h`ではなく、`S=(1)FFh`を設定します。

## 🚩 ステータスレジスタ(PSR)

PSR = Processor Status Register

Eがセットされているときにフラグの意味が変わるレジスタにはbitの後ろにEがついています。(4E, 5E)

 bit  | 名前 | 内容
----  | ---- | ---- 
  0   | C   | キャリー       (0=No Carry, 1=Carry)
  1   | Z   | ゼロ          (0=(前の計算結果が)非ゼロ, 1=ゼロ)
  2   | I   | 割り込み無効   (0=有効, 1=無効)
  3   | D   | デシマルモード  (0=通常, 1=ADC/SBC時にBCDモードで動作)
  4   | X   | インデックスレジスタフラグ
  4E  | B   | ブレークフラグ (0=IRQ, 1=BRK)
  5   | M   | アキュムレータフラグ
  5E  | U   | 不使用 (常に1)
  6   | V   | オーバーフロー      (0=No Overflow, 1=Overflow)
  7   | N   | ネガティブ (0=(前の計算結果が)正, 1=負)
  --  | E   | 後述 (0=16bit, 1=8bit)

### エミュレーションフラグ (E)

ファミコンのCPUである6502をエミュレーションするモード(6502互換モード)を起動します。このフラグはXCE命令でのみアクセスできます。

このモードでは、A/X/Yは（X/Mを設定した場合と同じで）8bitとなります。Sも同様に8bit(`0100h..01FFh`)になります。

互換モード中は、XフラグはBフラグ(Break, 後述)に、MフラグはUフラグ(不使用、常に1)に変更されます。さらに、例外ベクタは6502アドレスに変更され、例外発生時はリターンアドレスとして（24bitではなく）16bitをプッシュします。条件付き分岐は、ページをまたぐときに1サイクル余分にかかります。

### アキュムレータフラグ (M)

メモリフラグとも呼ばれているようです。

Aを8bitモードに切り替えます。Aの上位8bitにはXBAでアクセス可能です。

さらに、A,X,Yのオペランドを使用しないすべてのオペコード（STZや一部のINC/DECなど）を8bitモードに切り替えます。

### インデックスレジスタフラグ (X)

インデックスレジスタX,Yを8bitモードに切り替えます。X,Yの上位8bitは強制的に`00h`になります

### キャリー (C)

減算（SBC、CMP）に使用される場合、キャリーフラグは0以上の場合にセットされます。これは通常の80x86やZ80のCPUのキャリーフラグとは逆の意味を持っていることに注意してください。

他のすべての命令（ADC、ASL、LSR、ROL、ROR）では通常通り動作しますが、ROL/RORはキャリーを介して回転します。これは80x86ではROL/RORよりも、RCL/RCRに近い挙動です。

### ゼロフラグ (Z), ネガティブフラグ (N), オーバーフローフラグ (V)

Zは命令の結果がゼロのときにセットされ、Nは符号付き(bit7が1)のときにセットされます。

Vは加算減算が符号付き数値の最大範囲(`-128..+127`)を超えた場合に設定されます。

### 割り込み無効フラグ (I)

Iフラグをセットすると、IRQが無効化されます。NMIとBRK命令を無効にすることはできません。

### デシマルモードフラグ (D)

Dフラグがセットされている場合、ACD、SBC命令がBCDモードで動作します。

### ブレークフラグ (B)

6502互換モード(E=1)のとき、BRKとIRQは同じ割り込みベクタのアドレス(`FFFEh`)にジャンプします。このBフラグはBRKとIRQを区別できるようにするためのもので、BRKならB=1、IRQならB=0となります。

Bフラグに直接アクセスすることはできませんが、Pレジスタをスタックに書き込み、その値からBフラグを調べることができます。

BRKとPHPは常にBフラグをセットし、IRQとNMIは常にBフラグをクリアします。

## 👽 レジスタ名のエイリアス一覧

```
  DPR   D  (used in homebrew specs)
  K     PB (used by PHK)
  PBR   PB (used in specs)
  DBR   DB (used in specs)
  B     DB (used by PLB,PHB)
  B     Upper 8bit of A (used by XBA)
  C     Full 16bit of A (used by TDC,TCD,TSC,TCS)
```

