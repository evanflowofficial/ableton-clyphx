# ClyphX Pro Preset Manager - Changelog

## v1.2 - Insert Drag & Drop + Corrected Colors (2026-03-22)

### Behavior Changes

**Drag & Drop: Insert Instead of Swap**
- **Before:** Dragging preset #13 to slot #5 would swap the two presets
- **After:** Dragging preset #13 to slot #5 inserts it there; slots 5-12 shift down one position. Slot #13 is freed up.
- Cross-bank drags still swap (insert doesn't apply across separate banks)

**Animated Slot Shifting**
- Shifted slots animate with a smooth slide (up or down depending on direction) with a subtle bounce
- The inserted slot gets a pop/glow highlight effect
- Animations are ~350-400ms

### Color Index Corrections

Updated Ableton color indices based on verified 14×5 palette grid:

| Category | Color | Old Index | New Index | Hex |
|----------|-------|-----------|-----------|-----|
| Acoustic Drums | Dark Green | 26 | 62 | #559E38 |
| Electronic Drums | Lime Green | 21 | 19 | #8BFD70 |
| Bass | Yellow | 14 | 18 | #FEEE4A |
| Keyboards | Deep Blue | 37 | 65 | #1B3393 |
| Synths | Purple | 49 | 67 | #624EAB |
| Strings | Red | 4 | 15 | #FC393D |
| Winds | White | 69 | 14 | #FFFFFF |

### Track Name Derivation Update

- Leading numbers now reinsert before parenthetical qualifiers:
  - `"1 - A Drums (Masch).adg"` → `"A Drums 1 (Masch)"` (not just `"A Drums (Masch)"`)
  - `"3 - A Bass.adg"` → `"A Bass 3"`
  - `"Omnisphere 5.adg"` → `"Omnisphere 5"` (no prefix = unchanged)

### Timing Fix

- Added `WAIT 2` between `SEL/SWAP` and `SEL/NAME`/`SEL/COLOR` to give Ableton breathing room after loading heavy presets

---

## v1.1 - Track Name & Color Support (2026-03-21)

### New Features

**Auto Track Naming & Coloring on Preset Load**
When you press a pad on your Maschine MK3, the generated X-Controls.txt now also:
- Renames the selected track to match the preset name (cleaned up)
- Sets the track color based on the instrument category

Each action line now generates:
```
PAD_1_B1 = NOTE, 1, 0, 0, 127, SEL/DEV(1) SEL ; WAIT 1 ; SEL/SWAP "A Drums (Masch).adg" ; SEL/NAME "A Drums (Masch)" ; SEL/COLOR 26
```

**Track Name Derivation Logic:**
- Strips `.adg` extension
- Strips leading number prefixes (e.g., `1 - `, `12 - `)
- Only applies to real presets (PLACEHOLDER slots are unchanged)

**Category → Ableton Color Index Mapping:**
| Category | Color | Ableton Index |
|----------|-------|---------------|
| Acoustic Drums | Dark Green | 26 |
| Electronic Drums | Light Green | 21 |
| Bass | Yellow/Gold | 14 |
| Keyboards | Deep Blue | 37 |
| Synths | Purple | 49 |
| Strings | Red | 4 |
| Winds | White | 69 |

> **Note:** Color indices (1-70) are based on Ableton Live 10/11's color palette grid.
> If a color doesn't look right, you can adjust the `colorIndex` property in the `pages`
> array in `preset-manager.html` (around line 521). Use ClyphX Pro's `SEL/COLOR >` and
> `SEL/COLOR <` actions in Ableton to cycle through colors and find the index you want.

### Code Changes in `preset-manager.html`

1. **`pages` array** (~line 521): Added `colorIndex` property to each category
2. **New function `deriveTrackName()`** (~line 531): Cleans preset filename into a track name
3. **New function `getColorIndexForSlot()`** (~line 541): Returns color index for a given slot
4. **`generateXControlsText()`** (~line 549): Now appends `; SEL/NAME "..." ; SEL/COLOR X` to each real preset line

### File Fixes

- **Renamed** `Dry Org.adg` → `Dry Organ.adg` on disk at:
  `/Users/evanbaker/Music/Ableton/User Library/INSTRUMENTS/Bass/E Bass/Dry/`
  This fixes a mismatch where X-Controls.txt referenced "Dry Organ.adg" but the file was named "Dry Org.adg".

### Known Issues

- **8 missing Synth Bass presets**: `1 - Synth Bass.adg` through `8 - Synth Bass.adg` are referenced in X-Controls.txt but don't exist on disk. You'll need to either create these preset files or remove them from the manager.

---

## v1.0 - Initial Release

- Visual drag-and-drop preset manager
- 5 banks × 112 slots = 560 total presets
- Import/export X-Controls.txt
- Undo/redo, multi-select, keyboard shortcuts
- Collapsible banks and sections with persistent state
