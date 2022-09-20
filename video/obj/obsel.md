# OBSEL

## 2101h - OBSEL - オブジェクトサイズ/ベースアドレス (W)

```
  Bit 0-2   タイルデータ(前半)のベースアドレス (16KB単位)
              BASE = OBSEL.bit(0,2) * 16KB
  Bit 3-4   タイルデータ前後半のベースアドレスがどれだけ離れているか (8KB単位)
              タイルデータ前半: タイル番号 000h..0FFh
              タイルデータ後半: タイル番号 100h..1FFh
                BASE1 を タイルデータ前半 のベースアドレス、 BASE2 を後半のそれとすると、
                  BASE1 = OBSEL.bit(0,2) * 16KB
                  BASE2 = BASE1 + 0x2000 + (OBSEL.bit(3,4) * 8KB)
  Bit 5-7   OBJサイズ
              Val  Small   Large
               0 = 8x8     16x16    ;Caution:
               1 = 8x8     32x32    ;In 224-lines mode, OBJs with 64-pixel height
               2 = 8x8     64x64    ;may wrap from lower to upper screen border.
               3 = 16x16   32x32    ;In 239-lines mode, the same problem applies
               4 = 16x16   64x64    ;also for OBJs with 32-pixel height.
               5 = 32x32   64x64
               6 = 16x32   32x64    (undocumented)
               7 = 16x32   32x32    (undocumented)
            (Ie. a setting of 0 means Small OBJs=8x8, Large OBJs=16x16 pixels)
            (Whether an OBJ is "small" or "large" is selected by a bit in OAM)
```