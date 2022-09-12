# Color-Math

ColorMathはスーファミが、メイン画面、サブ画面、ウィンドウなどから実際の1枚の画面を生成するときに行う一連の処理のことです。

ColorMathを使うと色々な視覚効果を生み出すことができます。例えば、スクリーン加算は光が当たっている演出を、スクリーン平均化では半透明の演出(水中など)が可能です。

## 2130h - CGWSEL - ColorMath制御レジスタA (W)

```
  Bit 0    Direct Color (for 256-color BGs)  (0=Use Palette, 1=Direct Color)
  Bit 1    Sub Screen BG/OBJ Enable    (0=No/Backdrop only, 1=Yes/Backdrop+BG+OBJ)
  Bit 2-3  不使用
  Bit 4-5  ColorMath有効化       (0=Always, 1=MathWindow, 2=NotMathWin, 3=Never)
  Bit 6-7  Force Main Screen Black (3=Always, 2=MathWindow, 1=NotMathWin, 0=Never)
```

## 2131h - CGADSUB - ColorMath制御レジスタB (W)

```
  Bit 7    Color Math Add/Subtract        (0=Add; Main+Sub, 1=Subtract; Main-Sub)
  Bit 6    Color Math "Div2" Half Result  (0=No divide, 1=Divide result by 2)
  Bit 5    Color Math when Main Screen = Backdrop        (0=Off, 1=On) ;\
  Bit 4    Color Math when Main Screen = OBJ/Palette4..7 (0=Off, 1=On) ; OFF: Show
      -    Color Math when Main Screen = OBJ/Palette0..3 (Always=Off)  ; Raw Main,
  Bit 3    Color Math when Main Screen = BG4             (0=Off, 1=On) ;   or
  Bit 2    Color Math when Main Screen = BG3             (0=Off, 1=On) ; ON: Show
  Bit 1    Color Math when Main Screen = BG2             (0=Off, 1=On) ; Main+/-Sub
  Bit 0    Color Math when Main Screen = BG1             (0=Off, 1=On) ;/
```

Half-Color (Bit6): Ignored if "Force Main Screen Black" is used, also ignored on transparent subscreen pixels (those use the fixed color as sub-screen backdrop without division) (whilst 2130.1 uses the fixed color as non-transparent one, which allows division).

Bit0-5: Seem to affect MAIN SCREEN layers:

```
  Disable = Display RAW Main Screen as such (without math)
  Enable  = Apply math on Mainscreen
  (Ie. 212Ch enables the main screen, 2131h selects if math is applied on it)
```

## 2132h - COLDATA - Color Math Sub Screen Backdrop Color (W)

This 8bit port allows to manipulate some (or all) bits of a 15bit RGB value. Examples: Black: write E0h (R,G,B=0), Cyan: write 20h (R=0) and DFh (G,B=1Fh).

```
  Bit 7    Apply Blue  (0=No change, 1=Apply Intensity as Blue)
  Bit 6    Apply Green (0=No change, 1=Apply Intensity as Green)
  Bit 5    Apply Red   (0=No change, 1=Apply Intensity as Red)
  Bit 4-0  Intensity   (0..31)
```

The Sub Screen Backdrop Color is used when all sub screen layers are disabled or transparent, in this case the "Div2" Half Color Math isn't applied (ie. 2131h.Bit6 is ignored); there is one exception: If "Sub Screen BG/OBJ Enable" is off (2130h.Bit1=0), then the "Div2" isn't forcefully ignored.
For a FULLY TRANSPARENT backdrop: Set this register to Black (adding or subtracting black has no effect, and, with "Div2" disabled/ignored, the raw Main screen is displayed as is).

## Color Math

Color Math can be disabled by setting 2130h.Bit4-5, or by clearing 2131h.Bit0-5. When it is disabled, only the Main Screen is displayed, and the Sub Screen has no effect on the display.

Color Math occurs only if the front-most Main Screen pixel has math enabled (via 2131h.Bit0-5.), and only if the front-most Sub Screen pixel has same or higher (XXX or is it same or lower -- or is it ANY priority?) priority than the Main Screen pixel.

Same priority means that, for example, BG1 can be mathed with itself: BG1+BG1 gives double-brightness (or same brightness when Div2 is enabled), and, BG1-BG1 gives Black.

Addition/Subtraction is done per R,G,B color fragment, the results can be (optionally) divided by 2, and are then saturated to Max=31 and Min=0).

## Force Main Screen Black

This feature forces the whole Main Screen (including Backdrop) to become black, so, normally, the whole screen becomes black. However, color addition can be still applied (but, with the "Div2" not being applied). Whereas, although it looks all black, the Main Screen is still divided into black BG1 pixels, black BG2 pixels, and so on. Of which, one can disable Color Math for some of the pixels. Color Subtraction has no effect (since the pixels can't get blacker than black).

## Hires and Pseudo 3-Layer Math

In Hires modes (BG Mode 5,6 and Pseudo Hires via SETINI), the main/sub screen pixels are rendered as half-pixels of the high-resolution image. The TV picture is so blurry, that the result will look quite similar to Color Addition with Div2 - some games (Jurassic Park and Kirby's Dream Land 3) are actually using it for that purpose; the advantage is that one can additionally apply COLDATA addition to (both) main/sub-screen layers, ie. the result looks like "(main+sub)/2+coldata".

## 参考

- [Color Math - Super Nintendo Entertainment System Features Pt. 03b](https://youtu.be/zcoU6-9_fDM)
