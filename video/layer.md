# 画面レイヤ

## 描画先の画面

スーファミにはメイン画面とサブ画面という2つの仮想画面があります。

描画の際には、この2つの画面を重ねることで1つの画面として出力します。

各画面レイヤを メイン画面に映すか、サブ画面に映すか、両方に映すか の制御は[TM/TS](control.md) で行っています。

メイン画面は通常の画面で、サブ画面は[`Color Math`](colormath.md)と`512-pixel Hires Mode`でのみ使用されます。

## レイヤの優先度

```
  記法:
    BD:      Backdrop、背景色 (常に一番後ろ)
    OBJ.N    優先度NのOBJ (OAMで設定 N=0,1,2,3)
    BGn.0    BGレイヤのタイル
    BGn.1    BGレイヤの優先タイル (BGマップのbit13が1)
    BG3.Na   BGMODE.3 が 1 (モード1のみ)
    BG3.Nb   BGMODE.3 が 0 (モード1のみ)
    BG2.1p   Mode7のBG2のうち優先フラグが1のもの
    BG2.0p   Mode7のBG2
```

```
  Mode0    Mode1    Mode2    Mode3    Mode4    Mode5    Mode6    Mode7
  -        BG3.1a   -        -        -        -        -        -
  OBJ.3    OBJ.3    OBJ.3    OBJ.3    OBJ.3    OBJ.3    OBJ.3    OBJ.3
  BG1.1    BG1.1    BG1.1    BG1.1    BG1.1    BG1.1    BG1.1    -
  BG2.1    BG2.1    -        -        -        -        -        -
  OBJ.2    OBJ.2    OBJ.2    OBJ.2    OBJ.2    OBJ.2    OBJ.2    OBJ.2
  BG1.0    BG1.0    BG2.1    BG2.1    BG2.1    BG2.1    -        BG2.1p
  BG2.0    BG2.0    -        -        -        -        -        -
  OBJ.1    OBJ.1    OBJ.1    OBJ.1    OBJ.1    OBJ.1    OBJ.1    OBJ.1
  BG3.1    BG3.1b   BG1.0    BG1.0    BG1.0    BG1.0    BG1.0    BG1
  BG4.1    -        -        -        -        -        -        -
  OBJ.0    OBJ.0    OBJ.0    OBJ.0    OBJ.0    OBJ.0    OBJ.0    OBJ.0
  BG3.0    BG3.0a   BG2.0    BG2.0    BG2.0    BG2.0    -        BG2.0p
  BG4.0    BG3.0b   -        -        -        -        -        -
  BD       BD       BD       BD       BD       BD       BD       BD
```
