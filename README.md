# Console Map Editor

A browser-based tool for designing 2D maps for console games. It lets you paint a grid of colored cells and export the result as a C++ 2D array initializer, where each value corresponds to a `ConsoleColor` enum value used with `Console::ForegroundColor`.

---

## Features

### Grid configuration

- Set the grid width and height independently, from 1 to 500 cells each.
- Default size is 80x80.
- Clicking "Apply" resizes the grid while preserving any existing drawing within the new bounds.

### Drawing

- Click any cell to paint it with the selected color.
- Click and drag across the grid to paint multiple cells in one stroke.
- Touch input is supported for tablet use.

### Brush size

Choose the size of the painting brush from the following options: 1x1, 2x2, 3x3, 4x4, 5x5, 10x10, 20x20, and 30x30. Larger brushes paint a square block of cells centered on the cursor, which speeds up filling large areas.

### Color palette

All 16 values of the `ConsoleColor` enum are available as clickable swatches:

| Value | Name         | Value | Name        |
|-------|--------------|-------|-------------|
| 0     | Black        | 8     | DarkGray    |
| 1     | DarkBlue     | 9     | Blue        |
| 2     | DarkGreen    | 10    | Green       |
| 3     | DarkCyan     | 11    | Cyan        |
| 4     | DarkRed      | 12    | Red         |
| 5     | DarkMagenta  | 13    | Magenta     |
| 6     | DarkYellow   | 14    | Yellow      |
| 7     | Gray         | 15    | White       |

The active color is highlighted with a border. Its name and numeric value are shown above the palette.

### Fill all

A dropdown lets you pick any of the 16 colors and apply it to every cell on the grid at once. A color preview swatch updates as you change the selection. This replaces the entire canvas with the chosen color.

### Console proportions

A checkbox toggles "console proportions" mode, which stretches each cell so that its height is approximately 1.9 times its width. This matches the aspect ratio of a typical character cell in a Windows console, giving you an accurate visual preview of how the map will look at runtime. The drawing is preserved when toggling this mode on or off.

### C++ matrix export

Clicking "Generate C++ matrix" converts the current grid into a nested initializer list in the format:

```cpp
{{0,0,15,15,...}, {0,2,1,15,...}, ...}
```

Each value is the `ConsoleColor` index of the corresponding cell, row by row from top to bottom. The text box shows a preview of the first three rows. The "Copy" button copies the full matrix to the clipboard, ready to paste into a C++ source file.

---

## Usage example

```cpp
using namespace System;

const int MAP_W = 80;
const int MAP_H = 80;

int map[MAP_H][MAP_W] = {
    // paste exported matrix here
};

void drawMap() {
    for (int y = 0; y < MAP_H; y++) {
        for (int x = 0; x < MAP_W; x++) {
            Console::SetCursorPosition(x, y);
            Console::ForegroundColor = (ConsoleColor)map[y][x];
            Console::Write(L"\u2588"); // full block character
        }
    }
    Console::ResetColor();
}
```

Each integer in the matrix is cast to `ConsoleColor`, which maps directly to the same 0-15 values used in the editor. `Console::SetCursorPosition(x, y)` places the cursor at the correct column and row before each character is printed.

---

## Intended audience

Students building 2D games for the Windows console who need a visual way to design levels or maps without writing array values by hand.

---

## Technical notes

- Runs entirely in the browser with no dependencies and no server required. Open the HTML file directly.
- The grid is rendered on an HTML5 Canvas element using pixel-perfect scaling.
- Color data is stored as a flat typed array (`Uint8Array`) for efficiency.
- The exported matrix contains only integer values (0-15), each directly castable to `ConsoleColor` without modification.
