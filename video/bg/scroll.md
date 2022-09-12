# スクロール

## 210Dh/210Eh- BG1HOFS/BG1VOFS - BG1(X/Y)スクロール (W)

> **Note**  
> Mode7ではこのレジスタは違う使われ方をします。

このレジスタは1バイトを2度書き込むことで16bit値を設定するレジスタで、1度目に書き込んだ値はバッファに保存されます。

2度目に書き込んだバイトを`Current`、1度目に書き込んだバイト(バッファ)を`Prev`とすると

**BG1HOFS**

```c
  BG1HOFS = (Current<<8) | (Prev&~7) | ((BG1HOFS>>8)&7);
  Prev = Current;
```

**BG1VOFS**

```c
  BG1VOFS = (Current<<8) | Prev;
  Prev = Current;
```

となります。つまり、

```
  1度目の書き込み: スクロール量の下位 8bit を設定
  2度目の書き込み: スクロール量の上位 2bit を設定
```

ということになります。

## 210Fh/2110h- BG2HOFS/BG2VOFS - BG2(X/Y)スクロール (W)

BG1HOFS/BG1VOFS の BG2 版です。ただし、Mode7では不使用。

## 2111h/2112h- BG3HOFS/BG3VOFS - BG3(X/Y)スクロール (W)

BG1HOFS/BG1VOFS の BG3 版です。ただし、Mode7では不使用。

## 2113h/2114h- BG4HOFS/BG4VOFS - BG4(X/Y)スクロール (W)

BG1HOFS/BG1VOFS の BG4 版です。ただし、Mode7では不使用。

