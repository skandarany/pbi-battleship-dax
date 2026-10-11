# Build guide

Step-by-step instructions to rebuild the report from the DAX files, in Power BI Desktop.
Estimated time: 20 to 30 minutes.

## 0. Prerequisites

- Power BI Desktop (recent version, PBIP support enabled).
- Optional: in **File > Options and settings > Options > Preview features**, enable **Power BI Project (.pbip) save option** if your version still lists it as a preview.

## 1. Create the tables

Open a blank report. For each table in [`dax/tables.dax`](../dax/tables.dax):

1. **Modeling > New table**
2. Paste the definition (`Grid = ...`, `Shots = ...`, `Ships = ...`) and press Enter.

Then:

- Select `Shots[Cell]` > **Column tools > Sort by column > CellOrder**.
- Do the same for `Grid[Cell]`.
- Hide `CellOrder` columns from report view (optional, keeps the field list clean).
- Check **Model view**: there must be **no relationship** between the tables.

## 2. Create the measures

1. **Home > Enter data**, name the table `_Measures`, click **Load**.
2. For each measure in [`dax/measures.dax`](../dax/measures.dax): select `_Measures`, **Modeling > New measure**, paste.
3. Formatting:
   - `Accuracy %` > Format **Percentage**, 0 decimals.
   - `Board SVG` and `Fleet SVG` > **Measure tools > Data category > Image URL**. This step is mandatory, otherwise Power BI shows the raw text.
4. Delete the default `Column1` of `_Measures`; the table then becomes a measure table.

## 3. Build the page

Suggested canvas: 1280 x 720, dark background (`#0B1120`).

| Visual | Field(s) | Settings |
| --- | --- | --- |
| **Button slicer** (the "fire control") | `Shots[Cell]` | Selection: multi-select ON and "Multi-select with Ctrl" OFF, so each click adds a shot. Layout grid with **10 columns**, small font. This gives a clickable 10 x 10 grid. If your version has no button slicer, use a classic slicer (Tile style) instead. |
| **Slicer** | `Ships[Game]` | Single select ON. Tile style. |
| **Table** | `Board SVG` | One column only. Format > Cell elements / Image size: height and width **444 px**. Turn off totals, headers and grid lines. |
| **Table** | `Fleet SVG` | Same settings, size 240 x 200 px. |
| **Card** (x4) | `Shots Fired`, `Hits`, `Accuracy %`, `Score` | |
| **Card** | `Game Status` | Wide card at the top of the page. |

Alternative for the board: the **Image** visual with a dynamic URL (Format > Image > Image URL > fx > Field value > `Board SVG`).

### How to play

1. Pick a game (Game 1, 2 or 3).
2. Click cells in the fire control slicer: each selected cell is a shot.
3. Sink the 5 ships with as few shots as possible.
4. **New game**: clear the Shots slicer (eraser icon) or switch game.

> Tip: sync the Game slicer with a hidden copy on other pages if you add a "Stats" page later.

## 4. Save as PBIP

**File > Save as > Power BI project files (\*.pbip)**, inside the root of this repository, file name `pbi-battleship-dax`.

You get:

```
pbi-battleship-dax.pbip
pbi-battleship-dax.Report/
pbi-battleship-dax.SemanticModel/
```

The model is stored as TMDL text files, so every DAX change shows up as a readable diff in Git. The `.gitignore` of this repository already excludes the local cache (`.pbi/cache.abf`) and user settings.

## 5. Take the screenshots

Take a screenshot of the full page during a game and save it as `assets/report.png`, then reference it in the README. A short GIF (for example with ScreenToGif) is even better for a portfolio.

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| The board shows a long text starting with `data:image` | Set the measure's Data category to **Image URL**. |
| The image is tiny | Increase image size in the Table visual (Format > Image size). |
| Board is empty | Select exactly one game in the Game slicer. |
| Hits count is wrong after selecting cells | Check that no relationship exists between `Shots`, `Grid` and `Ships`. |
| Cells sorted A1, A10, A2... | Set Sort by column on `Shots[Cell]` to `CellOrder`. |
