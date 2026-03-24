# ClyphX Pro Preset Manager — Technical Reference

> **Purpose:** This file is a comprehensive code map for AI agents working on this repo.
> Read this before modifying any code. Last updated: 2026-03-23.

## Repository Structure

```
ClyphX_Pro/
├── preset-manager.html          # THE MAIN APP — single-file HTML/CSS/JS application
├── preset-manager-v2.html       # Redesigned UI (Tailwind/DaisyUI/glassmorphism). Missing: swapWait feature from v1
├── X-Controls.txt               # LIVE CONFIG — the file Ableton/ClyphX Pro actually reads
├── X-Controls-LAST-WORKING.txt  # Backup of last known-good config
├── X-Controls-BROKEN-BACKUP.txt # Backup of a config that caused crashes
├── Macros.txt                   # ClyphX Pro macros (currently clean/unused)
├── Preferences.txt              # ClyphX Pro preferences (WAIT thresholds, OSC, etc.)
├── Variables.txt                # ClyphX Pro variables
├── X-OSC.txt                    # ClyphX Pro OSC config
├── Button Bindings.txt          # ClyphX Pro button bindings
├── Encoder Bindings.txt         # ClyphX Pro encoder bindings
├── Script Linking.txt           # ClyphX Pro script linking
├── MIDI Rack-SysEx.txt          # ClyphX Pro MIDI/SysEx config
├── ClyphX-Pro-User-Manual-1.pdf # Official manual (readable with poppler installed)
│
├── CHANGELOG.md                 # Version history of preset manager changes
├── BUG_LOG.md                   # Crash analysis and debugging notes
├── CLYPHX_PRO_PRESET_MANAGER_DOCS.md  # User-facing docs, feature list, future ideas
├── TECHNICAL_REFERENCE.md       # THIS FILE — code map for AI agents
│
├── BANK_SELECT_GUIDE.md         # Guide for bank selection setup
├── MASCHINE_PRESET_MAP.md       # Maschine MK3 pad mapping reference
├── QUICK_REFERENCE.md           # Quick reference card
├── SETUP_GUIDE.md               # Initial setup instructions
└── README.md                    # Stub
```

## preset-manager.html — Architecture

Single-file app: all HTML, CSS, and JavaScript in one file. No dependencies, no build process. Opens directly in a browser.

### File Layout (line ranges approximate, may shift with edits)

| Lines | Section | Description |
|-------|---------|-------------|
| 1-7 | HTML head | Doctype, meta, title |
| 7-490 | `<style>` | All CSS including category colors, animations, modal styles |
| 490-528 | HTML body | DOM structure: header, controls bar, banks container, preserved controls, preview modal |
| 530-565 | JS: Global State | `banks` object, selection state, undo history, `pages` config array |
| 565-700 | JS: Rendering | `initBanks()`, `renderBank()`, `createSlot()`, toggle functions |
| 700-780 | JS: Selection | Click handlers (single, cmd+click, shift+click range) |
| 780-950 | JS: Drag & Drop | `handleDragStart`, `handleDragOver`, `handleDrop` (file drops, insert within bank, swap cross-bank, group drag) |
| 950-1000 | JS: Import/Parse | File input handler, `parseXControls()` |
| 1000-1060 | JS: Display helpers | `displayPreservedControls()`, `clearAll()` |
| 1060-1170 | JS: Generation | `deriveTrackName()`, `getColorIndexForSlot()`, `generateXControlsText()`, `getDefaultHeader()` |
| 1170-1210 | JS: UI helpers | `showPreview()`, `closePreview()`, `generateFile()`, `setStatus()` |
| 1210-1270 | JS: Undo/Redo | `saveState()`, `undo()`, `redo()`, `restoreState()`, `updateUndoRedoButtons()` |
| 1270-1320 | JS: Keyboard & Init | Keyboard shortcut listener, `deleteSelectedPresets()`, initialization calls |

---

## Global State Variables

```javascript
const banks = { 1: Array(112).fill(null), 2: ..., 3: ..., 4: ..., 5: ... }
// Main data. Each bank is 112 slots. Each slot is either null (empty) or a string (preset filename like "1 - A Drums (Masch).adg")

let preservedHeader = ''     // Header text from imported X-Controls.txt (comments, settings notes)
let preservedFooter = ''     // Non-PAD control lines (SHOW_PLUGIN, HIDE_PLUGIN, etc.) joined as string
let preservedControls = []   // Array of individual preserved control lines

let selectedSlots = new Set() // Set of "bank-index" strings, e.g. {"1-5", "1-6", "2-10"}
let lastSelectedSlot = null   // Last clicked slot key, used for shift+click range selection

let history = []              // Array of state snapshots for undo/redo
let historyIndex = -1         // Current position in history
const MAX_HISTORY = 50        // Max undo steps

let draggedSlot = null        // DOM element being dragged (single slot)
let draggedGroup = null       // Array of {bank, index, preset} for group drags
```

## Configuration: `pages` Array

```javascript
const pages = [
    { name: 'Acoustic Drums',   start: 0,   count: 16, colorIndex: 62, swapWait: 0 },
    { name: 'Electronic Drums', start: 16,  count: 16, colorIndex: 19, swapWait: 0 },
    { name: 'Bass',             start: 32,  count: 16, colorIndex: 18, swapWait: 0 },
    { name: 'Keyboards',        start: 48,  count: 16, colorIndex: 24, swapWait: 0 },
    { name: 'Synths',           start: 64,  count: 16, colorIndex: 67, swapWait: 0 },
    { name: 'Strings',          start: 80,  count: 16, colorIndex: 15, swapWait: 0 },
    { name: 'Winds',            start: 96,  count: 16, colorIndex: 56, swapWait: 8 },
]
```

**Properties:**
- `name` — Display label and section header
- `start` — First slot index (0-based) in the 112-slot bank
- `count` — Number of slots in this section (all must sum to 112)
- `colorIndex` — Ableton Live color palette index (1-70) used in `SEL/COLOR` action
- `swapWait` — Extra WAIT beats after SEL/SWAP before NAME/COLOR (heavy plugins like SWAM need more time)

**Ableton Color Palette:** 14 columns × 5 rows = 70 colors. Index = (row-1)*14 + column. See CHANGELOG.md for verified hex values.

---

## Function Reference

### Rendering

#### `initBanks()`
Creates all 5 bank containers in the DOM. For each bank:
- Creates bank wrapper div with collapse state from localStorage
- Creates header with bank number, MIDI channel, fill count
- Attaches click handler for `toggleBank()`
- Calls `renderBank(i)` for each bank

#### `renderBank(bankNum)`
Renders all 7 sections for one bank. For each page in `pages`:
- Creates section div with category class and collapse state from localStorage
- Creates slot grid (4 columns) with `createSlot()` for each slot
- Calls `updateBankCount(bankNum)`

#### `createSlot(bankNum, index)` → HTMLElement
Creates a single slot DOM element. Sets:
- Class: `slot filled` or `slot empty`, plus `selected` if in `selectedSlots`
- `data-bank` and `data-index` attributes
- `draggable` attribute
- Inner HTML: slot number/note label, preset name, remove button (filled only)
- Event listeners: dragstart, dragover, drop, dragleave, click, dblclick

#### `toggleBank(bank)` / `toggleSection(section)`
Toggle `.collapsed` class and persist to localStorage.

#### `toggleAllSections(bankNum)` *(v2 only)*
Collapses all sections in a bank if any are expanded, or expands all if all are collapsed. Persists each section's state to localStorage. Triggered by the rows icon button in the bank header.

#### `updateBankCount(bankNum)`
Updates the "X/112 filled" text in the bank header.

---

### Selection Handlers

#### `handleSlotClick(e)`
Three modes based on modifier keys:
- **Shift+click:** Range select from `lastSelectedSlot` to clicked slot (same bank only, filled slots only)
- **Cmd/Ctrl+click:** Toggle individual slot in/out of selection
- **Plain click:** Clear all selections, select only this slot

#### `handleSlotDoubleClick(e)`
Opens a `prompt()` dialog to rename the preset. Empty string removes the preset.

---

### Drag & Drop

#### `handleDragStart(e)`
If the dragged slot is part of a multi-selection (`selectedSlots.size > 1`), populates `draggedGroup` with all selected slot data. Otherwise sets `draggedSlot` only. Applies opacity styling.

#### `handleDragOver(e)` / `handleDragLeave(e)`
Adds/removes `.drag-over` class for visual feedback.

#### `handleDrop(e)` — **Complex, three branches:**

**Branch 1: File drop from Finder**
- Filters for `.adg` files only
- Fills slots sequentially starting from target slot
- Stops at bank boundary (slot 112)

**Branch 2: Group drag (multi-selection)**
- Clears original positions
- Places all selected presets sequentially at target
- Clears selection, re-renders affected banks

**Branch 3: Single slot drag**
- **Same bank → INSERT:** Uses `Array.splice()` to remove from source and insert at target. All slots between shift. Triggers shift/insert animations.
- **Cross bank → SWAP:** Traditional swap of source and target values.

#### Animation classes applied after insert:
- `.shift-down` / `.shift-up` — 350ms slide with bounce (applied to shifted slots)
- `.insert-highlight` — 400ms pop/glow (applied to inserted slot)
- Classes removed after 400ms timeout

#### `removePreset(e, bank, index)`
Sets `banks[bank][index] = null`, re-renders, saves state.

#### `deleteSelectedPresets()`
Iterates `selectedSlots`, nulls each, clears selection, re-renders affected banks.

---

### File I/O

#### `parseXControls(content)`
Parses an X-Controls.txt string into the `banks` object:
- Splits by newline, iterates each line
- Detects PAD lines with regex `/PAD_(\d+)_B(\d)/`
- Extracts preset name with `/SEL\/SWAP "([^"]+)"/`
- Populates `banks[bankNum][padNum]`
- Preserves non-PAD control lines (SHOW_PLUGIN, etc.) in `preservedControls`
- Preserves header lines in `preservedHeader`

#### `generateXControlsText()` → string
Generates the full X-Controls.txt content from current state:
- Starts with preserved header or default header
- For each bank (1-5), for each page section:
  - Writes section comment
  - For each slot: generates PAD line with `SEL/DEV(1) SEL ; WAIT 1 ; SEL/SWAP "preset"`
  - For real presets (not PLACEHOLDER): appends `; SEL/NAME "cleanName" ; SEL/COLOR {colorIndex}`
  - If the category has `swapWait > 0`: inserts `; WAIT {swapWait}` between the SWAP and NAME/COLOR (heavy plugins like SWAM need breathing room)
  - Categories with `swapWait: 0` get NAME/COLOR immediately after SWAP (no extra WAIT)
- Appends preserved footer controls

#### `deriveTrackName(presetFilename)` → string|null
Cleans a preset filename into a track display name:
- Returns null for null/PLACEHOLDER
- Strips `.adg` extension
- If leading number prefix (e.g., `"1 - "`): removes it and reinserts the number
  - Before parenthetical: `"1 - A Drums (Masch).adg"` → `"A Drums 1 (Masch)"`
  - At end if no parens: `"3 - A Bass.adg"` → `"A Bass 3"`
- If no prefix: returns as-is (e.g., `"808 Pure.adg"` → `"808 Pure"`)

#### `getColorIndexForSlot(slotIndex)` → number
Looks up which page section a slot belongs to and returns its `colorIndex`.

#### `getDefaultHeader()` → string
Returns the default X-Controls.txt header with settings notes and section marker.

#### `displayPreservedControls()`
Renders preserved (non-PAD) controls in the UI footer section.

---

### UI Helpers

#### `showPreview()` / `closePreview()`
Opens/closes the preview modal with generated X-Controls.txt text.

#### `generateFile()`
Creates a Blob from `generateXControlsText()`, triggers a download as `X-Controls.txt`.

#### `setStatus(message, type)`
Shows a status message (with optional 'success' styling). Auto-clears after 5 seconds.

#### `clearAll()`
Confirms, then resets all 5 banks to empty arrays.

---

### State Management (Undo/Redo)

#### `saveState()`
Deep-copies all 5 bank arrays into `history`. Truncates future history if not at end. Caps at `MAX_HISTORY` (50).

#### `undo()` / `redo()`
Moves `historyIndex` and calls `restoreState()`.

#### `restoreState(state)`
Copies bank arrays from snapshot back into `banks`, re-renders all banks.

#### `updateUndoRedoButtons()`
Enables/disables undo/redo buttons based on `historyIndex` position.

---

### Keyboard Shortcuts

Registered via `document.addEventListener('keydown', ...)`:

| Shortcut | Action |
|----------|--------|
| Cmd/Ctrl + Z | `undo()` |
| Cmd/Ctrl + Shift + Z | `redo()` |
| Cmd/Ctrl + Y | `redo()` (alternative) |
| Delete / Backspace | `deleteSelectedPresets()` (when selection exists) |

---

## CSS Architecture

### Key Animation Classes
- `.shift-down` — `@keyframes shiftDown`: translateY(-100%) → bounce → 0
- `.shift-up` — `@keyframes shiftUp`: translateY(100%) → bounce → 0
- `.insert-highlight` — `@keyframes insertPop`: scale(0.8) → scale(1.05) with blue glow → scale(1)

### Category Color Classes
`.category-0` through `.category-6` — each sets `.page-title` background and left border color:
- 0: Dark green (#2d5016)
- 1: Light green (#3d6026)
- 2: Gold (#7a6020)
- 3: Deep blue (#1a4d7a)
- 4: Purple (#4a2a5a)
- 5: Red (#6a2a2a)
- 6: Silver/grey (#4a4a4a)

### Collapse Behavior
Banks and sections use `.collapsed` class:
- `.bank.collapsed .bank-content` → `max-height: 0; padding: 0`
- `.page-section.collapsed .page-content` → `max-height: 0; padding: 0`
- Arrows rotate -90deg when collapsed
- Transitions: `max-height 0.3s ease-out`

---

## X-Controls.txt Format

Each line maps a MIDI note to a ClyphX Pro action list:

```
PAD_{slotNum}_B{bankNum} = NOTE, {midiChannel}, {noteNum}, 0, 127, {actionList}
```

- `slotNum`: 1-112 (1-indexed)
- `bankNum`: 1-5
- `midiChannel`: matches bankNum (Bank 1 = Channel 1)
- `noteNum`: 0-111 (0-indexed, = slotNum - 1)

### Action List for Real Presets
```
# Most categories (swapWait: 0) — no extra wait:
SEL/DEV(1) SEL ; WAIT 1 ; SEL/SWAP "filename.adg" ; SEL/NAME "Track Name" ; SEL/COLOR {colorIndex}

# Heavy plugin categories (e.g. Winds, swapWait: 8) — extra wait before NAME/COLOR:
SEL/DEV(1) SEL ; WAIT 1 ; SEL/SWAP "filename.adg" ; WAIT 8 ; SEL/NAME "Track Name" ; SEL/COLOR {colorIndex}
```

### Action List for PLACEHOLDER Slots
```
SEL/DEV(1) SEL ; WAIT 1 ; SEL/SWAP "PLACEHOLDER"
```
(No NAME or COLOR — these are unfilled slots)

---

## Integration with Ableton/ClyphX Pro

### Signal Flow
```
Maschine MK3 pad press
  → MIDI Note on Channel N
  → Bome MIDI Translator
  → ClyphX Pro (reads X-Controls.txt)
  → Executes action list on selected track
  → Device swaps, track renamed, track colored
```

### File Locations
- **X-Controls.txt lives in:** `/Users/evanbaker/nativeKONTROL/ClyphX_Pro/`
- **ClyphX Pro reads from:** Same path (symlinked or set in ClyphX Pro preferences)
- **Preset .adg files:** `/Users/evanbaker/Music/Ableton/User Library/INSTRUMENTS/` (organized by category subfolders)
- **Ableton crash logs:** `~/Library/Logs/DiagnosticReports/Live-*.ips`
- **Ableton Live log:** `~/Library/Preferences/Ableton/Live 12.3.6/Log.txt`

### Reload Procedure
After generating a new X-Controls.txt:
1. Replace the file in the ClyphX Pro folder
2. Restart Ableton Live completely (or load a new Live Set)
3. ClyphX Pro re-reads config on set load

---

## Known Bugs & Caveats

- **EZdrummer 3 crash:** Rapidly swapping EZdrummer presets corrupts its heap memory. This is an EZdrummer bug (fixed in 3.1.2 for new triggers, but corrupted Live Sets require fresh sets). See BUG_LOG.md.
- **SWAM instruments need long WAIT:** The Winds category uses `swapWait: 8` because SWAM plugins take longer to load. Adjust per category if other heavy plugins are added.
- **8 missing Synth Bass presets:** `1-8 - Synth Bass.adg` are referenced but don't exist on disk.
- **Placeholder slots:** Empty slots generate `SEL/SWAP "PLACEHOLDER"` which will fail gracefully in Ableton (no .adg file found).
- **Cross-bank drag = swap, not insert:** Insert only works within the same bank. Cross-bank drags swap the two slots.
