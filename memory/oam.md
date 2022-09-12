# OAMへのアクセス

## 2102h/2103h - OAMADD - OAMアドレス (W)

```
  2102h: OAMADDL
  2103h: OAMADDH
```

```
  Bit 0-7   OAMアドレス (ワード単位)
              このアドレスを2で割った値、つまりbit1-7がアクセス対象のOBJ
  Bit 8     OAMテーブル (0= 0..511バイト, 1=512..543バイト)
  Bit 9-14  不使用
  Bit 15    OAM優先度回転フラグ (OBJ同士の優先度)
              0: OBJ0が最大、OBJ127が最低
              1: アクセス対象のOBJn(n = bit1-7)が優先度最大、OBJ(n+1)が次に優先され、OBJ(n-1)が優先度最低になる
```

このレジスタでアクセスするOAMのアドレスを設定します。

<sup id="addr">1: このレジスタでは合計9bitでアドレスを指定しますが、内部には(544バイトなので)10bitのアドレスレジスタが存在しており、OAMADDに書き込まれたときに、内部のアドレスレジスタに反映されます。</sup>

## 2104h/2138h - OAMDATA/RDOAM - OAM書き込み/読み込み

OAMDATA(0x2104) は 書き込み専用、 RDOAM(0x2138) は読み取り専用です。

```
  1回目のアクセス: 下位8bit (偶数アドレス)
  2回目のアクセス: 上位8bit (奇数アドレス)
```

Reads and Writes to EVEN and ODD byte-addresses work as follows:

```
  Write to EVEN address      -->  set OAM_Lsb = Data    ;memorize value
  Write to ODD address<200h  -->  set WORD[addr-1] = Data*256 + OAM_Lsb
  Write to ANY address>1FFh  -->  set BYTE[addr] = Data
  Read from ANY address      -->  return BYTE[addr]
```

両方とも、1回目、2回目に関係なく、アクセスのたびにOAMのアドレスがインクリメントされます。

OAM Size is 220h bytes (addresses 220h..3FFh are mirrors of 200h..21Fh).
