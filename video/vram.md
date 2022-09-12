# VRAM

スーファミには、64KBのVRAMがあります。VRAMはアドレス空間にマッピングされておらず、[I/Oレジスタを通してアクセスする](../memory/vram.md)必要があります。

VRAMは BGマップ と　タイルデータ を保持しています。

## 🗺 BGマップ

[BGマップ](./bg/bgmap.md) を参照してください。

## 🟩 8x8タイルデータ

利用可能な色数に応じて、8x8タイルの使うバイト数は変わってきます。

```
 4色:   16バイト    ; 2bpp
 16色:  32バイト    ; 4bpp
 256色: 64バイト    ; 8bpp
```

```
  2bpp(4色, 16バイト):
    Row0: 00h, 01h
    Row1: 02h, 03h
    ..
    Row6: 0Ch, 0Dh
    Row7: 0Eh, 0Fh

  4bpp(16色):
    Row0: 00h, 01h, 10h, 11h
    Row1: 02h, 03h, 12h, 13h
    ..
    Row6: 0Ch, 0Dh, 1Ch, 1Dh
    Row7: 0Eh, 0Fh, 1Eh, 1Fh

  8bpp(256色):
    Row0: 00h, 01h, 10h, 11h, 20h, 21h, 30h, 31h
    Row1: 02h, 03h, 12h, 13h, 22h, 23h, 32h, 33h
    ..
    Row2: 0Ch, 0Dh, 1Ch, 1Dh, 2Ch, 2Dh, 3Ch, 3Dh
    Row3: 0Eh, 0Fh, 1Eh, 1Fh, 2Eh, 2Fh, 3Eh, 3Fh
```

BGタイルは、BGモードに応じて色数が 4/16/256 色のどれかになりますが、OBJタイルは常に16色です。

<details>
  <summary>Mode7の場合</summary>
  
  ただし、Mode7 のタイルデータだけは例外です。

  Mode7のタイルデータは 1byte = 1pixel に対応しており、256色パレットの色番号となっています。

  VRAMは、オフセット(バイト単位)が偶数のBGマップと奇数のタイルデータに分かれており、8x8タイルデータの内容は以下のようになります。

  ```
    Row0: 01h,03h,05h,07h,09h,0Bh,0Dh,0Fh
    Row1: 11h,13h,15h,17h,19h,1Bh,1Dh,1Fh
    Row2: 21h,23h,25h,27h,29h,2Bh,2Dh,2Fh
    Row3: 31h,33h,35h,37h,39h,3Bh,3Dh,3Fh
    Row4: 41h,43h,45h,47h,49h,4Bh,4Dh,4Fh
    Row5: 51h,53h,55h,57h,59h,5Bh,5Dh,5Fh
    Row6: 61h,63h,65h,67h,69h,6Bh,6Dh,6Fh
    Row7: 71h,73h,75h,77h,79h,7Bh,7Dh,7Fh
  ```
</details>

## 🟧 16x16(以上の)タイル

BGタイルは 16x16、OBJは(最大で) 64x64 と8x8より大きいタイルサイズを取ることが可能です。

8x8より大きいタイルサイズは、8x8のタイルを複数組み合わせて実現されます。

BGの場合、16x16なので、8x8のタイルが2x2集まって構成されています。BGマップのタイル番号Nは左上の8x8タイルを指します。

このとき、16x16タイルは

```
  N+00, N+01
  N+16, N+17
```

のようになります。

32x32のOBJの場合、OAMエントリ指すのタイル番号がNとすると

```
  N+00, N+01, N+02, N+03
  N+16, N+17, N+18, N+19
  N+32, N+33, N+34, N+35
  N+48, N+49, N+50, N+51
```

となります。

The hex-tile numbers could be thus thought of as "Yyxh", with "x" being the 4bit x-index, and "Yy" being the y-index in the array. For OBJ tiles, the are no carry-outs from "x+1" to "y", nor from "y+1" to "Y". Whilst BG tiles are processing carry-outs. For example:

```
  16x16 BG Tile 1FFh     16x16 OBJ Tile 1FFh
  Tile1ffh Tile200h      Tile1ffh Tile1f0h
  Tile20fh Tile210h      Tile10fh Tile100h
```

## 🛎 VRAMへのアクセス

[こちら](../memory/vram.md)を参照してください。