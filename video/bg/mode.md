# BGモード

背景(BG)に関して、スーファミは7つのモードが存在しています。

各モードは Mode0, Mode1, ... Mode7 と呼ばれ、モードごとに

- 利用できる背景レイヤの枚数と色数
- レンダリングの特性

が異なります。

モードの切り替えは[BGMODE](bg_control.md)で設定します。

**各モードが使える背景レイヤと色数**

モード | BG1 | BG2 | BG3 | BG4 
-- | -- | -- | -- | -- 
Mode0 | 4 | 4 | 4 | 4 
Mode1 | 16 | 16 | 4 | - 
Mode2 | 16 | 16 | - | - 
Mode3 | 256 | 16 | - | - 
Mode4 | 256 | 4 | - | - 
Mode5 | 16 | 4 | - | - 
Mode6 | 16 | - | - | - 
Mode7 | 256 | EXTBG | - | - 

## Mode0

<img src="/images/modeprops/mode0.webp" width="248" alt="mode0 props" />

<img src="/images/mode0_1.webp" width="256" alt="ヨッシーアイランド" />

基本となるBGモードです。

BG1,BG2,BG3,BG4 の全て利用可能で、タイルサイズも `8x8` か `16x16` から選択可能です。

特殊効果も、モザイク、ウィンドウ、ColorMath、インターレース(制限あり)、擬似512モードが利用可能です。

基本となるBGモードですが、どのBGも利用可能なのは4色だけなので、実際にゲームで使われることはあまりありませんでした。

## Mode1

<img src="/images/modeprops/mode1.webp" width="224" alt="mode1 props" />

<img src="/images/mode1_1.webp" width="256" alt="ロックマンX2" />&nbsp;&nbsp;&nbsp;&nbsp;<img src="/images/mode1_2.webp" width="256" alt="クロノトリガー" />

最も使われているBGモードです。

Mode0 の特殊効果はすべてサポートしています。タイルサイズも `8x8` か `16x16` から選択可能です。

Mode0 との違いは、利用可能なBG数と、BGが使える色数です。

## Mode2

<img src="/images/modeprops/mode2.webp" width="224" alt="mode2 props" />

<img src="/images/mode2_1.webp" width="256" alt="カービィSDX" />

Mode2 はMode1とほとんど一緒ですが、`Offset-per-Tile`が使える点が異なっています。

`Offset-per-Tile`はBG3を使う機能なので、背景として使えるBGはMode1と違ってBG1とBG2のみです。

## Mode3

<img src="/images/modeprops/mode3.webp" width="224" alt="mode3 props" />

Mode1 からBG1の表現力を高めたBGモードが、Mode3です。

BG1は、256色利用可能になり、[ダイレクトカラーモード](palette.md)に対応しました。しかし、Mode1 では使えていたBG3は使用不可能になりました。

## Mode4

<img src="/images/modeprops/mode4.webp" width="248" alt="mode4 props" />

Mode4 は Mode2 と Mode3 を組み合わせたようなBGモードです。

Mode2の`Offset-per-Tile`とMode3のダイレクトカラーモードの両方を使えますが、BG2が使えるのが4色に減っています。

`Offset-per-Tile`もMode2と違って、X方向かY方向のどちらかにしかスクロールできなくなりました。

## Mode5

<img src="/images/modeprops/mode5.webp" width="160" alt="mode5 props" />

<img src="/images/mode5_1.webp" width="256" alt="ルドラの秘宝" />&nbsp;&nbsp;&nbsp;&nbsp;<img src="/images/mode5_2.webp" width="256" alt="ロマサガ3" />

Mode5の特徴は、擬似ではない512モード(真512モード)を利用可能なところです。

## Mode6

<img src="/images/modeprops/mode6.webp" width="160" alt="mode6 props" />

Mode6 は Mode2 と Mode5 を組み合わせたようなBGモードです。

Mode2 の`Offset-per-Tile`と Mode5 の真512モードの両方を使えます。

ただし、BG1しか使えず、色数も16色までです。そのせいか、Mode6が使われているゲームは(要調査)ほとんど存在しないようです。

## Mode7

<img src="/images/modeprops/mode7.webp" width="248" alt="mode7 props" />

<img src="/images/mode7_1.webp" width="256" height="224" alt="F-ZERO" />&nbsp;&nbsp;&nbsp;&nbsp;<img src="/images/mode7_2.webp" width="256" alt="ヨッシーアイランド" />

背景レイヤを行列変換可能な特殊モードで、スーパマリオカートやF-ZEROのように擬似3D表現をするときに利用されます。

Mode7では正確にはBG1の1つしか使えませんが、SETINI.6 のMode7拡張フラグをセットすると、BG2も使えるようになります。(このBG2のことをEXTBGと呼ぶこともある)

Mode7の詳細については [個別ページ](./mode7.md) を参照してください。

## 参考

- [SNES Background Modes 0-6](https://youtu.be/5SBEAZIfDAg)
