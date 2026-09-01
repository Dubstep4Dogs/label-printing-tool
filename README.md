# label-printing-tool
A browser-based tool for laying out 2" round sticker designs on a 12-up sheet and printing directly — no template upload, no Word, no Canva print step.

Open `sticker-printing-tool.html` in any browser to use it. It runs entirely locally; no upload or server is involved.

## Basic use
1. **Add your design(s).** Drop an image (PNG or JPG, square, full-bleed — no white border needed) into the upload area, or click it to browse. You can add more than one design (see below).
2. **Set quantity per design.** Each uploaded design gets its own stepper. Slots fill in the order designs were added — e.g. 8 of Design A then 4 of Design B fills one 12-up sheet as A×8 + B×4.
3. **Reuse a partial sheet (optional).** If you've already peeled some labels off a sheet, expand "Reuse a partial sheet" and set how many positions to skip. The rest print starting on the next open slot.
4. **Check margins & spacing.** These are pre-calibrated for this specific sticker stock (see *Calibration* below) — you generally shouldn't need to touch them unless printing on a different sheet.
5. **Fine-tune alignment (optional).** `Horizontal`/`Vertical` nudge the whole sheet by a small amount without touching the calibrated margins — use this if a specific printer shifts prints slightly.
6. **Turn on the cut-guide** ("Show cut-line guide on screen" and/or "Include guide when printing") to see a dashed circle marking exactly where the die-cut will fall. Use it for test prints, then turn "Include guide when printing" off for the real batch.
7. **Print.** Click "Print sheet(s)" — this calls your browser's normal print dialog.

## Source design specs
The design currently in use: a square PNG, 1181×1181px at 300 DPI (≈3.94" per side before the tool scales/crops it to fit the 2" circle). This comfortably exceeds the ~600px-at-300-DPI minimum needed to fill a 2" label without pixelation, so it's safe to reuse at this resolution for future designs, or size new ones similarly (a 1200×1200px square at 300 DPI is a convenient round-number target).

## Your setup
- **Printer:** Brother HL-L2395DW
- **Feed:** manual feed tray, sheet right-side up, top edge away from you (the tray's default/natural orientation)
- **Labels:** Avery 2" Round Labels, Matte (Template 22612)

## Print settings that matter
- **Scale: 100% / Actual size** — not "Fit to page." The page is already built to Letter size with zero margins; any auto-scaling will throw off alignment.
- **Margins: None** (the page handles its own margins internally).
- **Paper source / tray:** use whichever tray reliably feeds your sticker stock without jamming. Note that face-up vs. face-down orientation, and even margin/scale defaults, can differ between trays on the same printer — if you switch trays, re-run a test print with the guide on.
  - **HL-L2395DW specific:** Brother's laser printers route paper through an S-shaped/curved path by default, which is a common cause of skewed feeds on stiff or glossy stock like labels. Manual feed already bypasses one of those curves. For labels specifically, Brother also recommends opening the machine's **back cover** (the face-up output tray) when using Thick Paper or Labels in manual feed — this creates a straight-through path instead of curving the sheet back out the top, which should meaningfully reduce the skew/pivoting issue seen in testing. Worth trying on the next test print if you haven't already.
- **Media/paper type:** if your printer driver has a "Labels" media profile, prefer it over a generic "thick paper"/cardstock setting — label profiles are typically tuned for adhesive-backed sheets (roller pressure, feed speed, ink saturation) rather than uniformly rigid stock.

## Calibration
Real sticker sheets vary in exact spacing — this tool's defaults were measured directly off a physical sheet (not assumed from a generic spec) using a repeatable method that's worth knowing if you ever calibrate for a different sheet or printer:

1. Print a test sheet with the cut-guide on, at 100% scale.
2. Measure from the **paper's outer edge** (not the sticker layer, which may be inset from the true paper edge) to the guide circles — margin measurements — and across multiple positions at once (e.g. left column to right column, top row to bottom row) rather than adjacent cells, since small measurement error gets multiplied over a shorter span and is easy to misread.
3. Compare those measurements to the same measurements taken on a real, unprinted die-cut sheet. Differences reveal whether the mismatch is a margin issue, a spacing/pitch issue, or a printer-specific shift — each has a different fix in the tool (`marginLeft`/`marginTop` for margins, `gapX`/`gapY` for pitch, `offX`/`offY` for a pure printer shift).

**Current calibrated defaults for this stock/printer:**
| Setting | Value |
|---|---|
| Left margin | 0.5625" |
| Top margin | 0.5625" |
| Column gap | 0.625" |
| Row gap | 0.583" |

These bake in a measured ~2.625" column pitch and ~2.583" row pitch (the real sheet's spacing, not the generic 0.5"-gap assumption), plus a small correction for this printer's own slight rightward print shift and a 1/16" upward nudge dialed in by eye.

## Troubleshooting
- **A single sticker or row is uniformly off in one direction:** use `offX`/`offY` (Fine-tune alignment) — it shifts the whole sheet without touching pitch.
- **The error grows the further a sticker is from the center of the sheet:** that's a pitch/scale issue, not an offset issue — adjust `gapX`/`gapY` (see *Calibration*), not `offX`/`offY`.
- **The whole sheet looks rotated/pivoted around one corner:** this is paper skew from the printer feed, not something this tool (or any print-layout tool) can correct — it needs to be fixed at the printer/feed level. On the HL-L2395DW specifically, try opening the back cover (face-up output tray) for a straight-through path when manual-feeding labels (see *Print settings that matter* above); also make sure the sheet is seated flush and square against the manual feed guides before it grabs.
- **A toggle doesn't seem to take effect on print:** make sure you're testing with a fresh print (some browsers cache the previous print preview) and that "Include guide when printing" and "Show cut-line guide on screen" are two independent switches — the printed guide follows only the "Include guide when printing" toggle.

## Notes on saved data
Your designs and settings are saved in the browser's local storage so you don't have to re-upload each time you reopen the file. This storage is tied to your browser, not to the specific copy of the HTML file — reopening an updated version of this tool (e.g. after a fix or new feature) will still show your previously saved designs and settings, since it's the same local storage bucket. There's currently no in-app "clear all" button; ask if you'd like one added.
