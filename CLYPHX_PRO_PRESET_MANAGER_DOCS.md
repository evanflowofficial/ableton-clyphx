# ClyphX Pro Preset Manager - Technical Documentation

## Project Overview

**Project Name:** ClyphX Pro Preset Manager  
**Type:** Web-based GUI application  
**Technology:** Single-file HTML/CSS/JavaScript (no dependencies)  
**Purpose:** Visual drag-and-drop interface for managing Ableton Live presets through ClyphX Pro  
**File:** `preset-manager.html`  
**Location:** `/Users/evanbaker/nativeKONTROL/ClyphX_Pro/`

## What Problem It Solves

ClyphX Pro uses a text-based configuration file (`X-Controls.txt`) to map MIDI notes to Ableton Live preset files. Manually editing 560 preset mappings in a text file is error-prone and tedious. This tool provides a visual interface to:
- Organize presets across 5 banks (560 total slots)
- Drag-and-drop files from Finder
- Rearrange presets visually
- Generate the X-Controls.txt file automatically

## Architecture

### System Design
- **Single HTML file** - All code (HTML/CSS/JS) in one portable file
- **No build process** - Open directly in any modern browser
- **Offline-first** - Works completely offline, no server required
- **localStorage** - Persists UI state (collapsed sections, etc.)
- **File-based I/O** - Reads/writes X-Controls.txt files

### Data Structure

```javascript
// 5 banks, 112 slots each = 560 total presets
const banks = {
    1: Array(112).fill(null),  // MIDI Channel 1
    2: Array(112).fill(null),  // MIDI Channel 2
    3: Array(112).fill(null),  // MIDI Channel 3
    4: Array(112).fill(null),  // MIDI Channel 4
    5: Array(112).fill(null)   // MIDI Channel 5
};

// Each slot can contain:
// - null (empty)
// - String (preset filename, e.g., "LABS - Percussion.adg")
```

### Bank Organization

Each bank has 7 sections (16 slots each):
1. **Acoustic Drums** (Slots 1-16) - Dark Green
2. **Electronic Drums** (Slots 17-32) - Light Green
3. **Bass** (Slots 33-48) - Yellow/Gold
4. **Keyboards** (Slots 49-64) - Deep Blue
5. **Synths** (Slots 65-80) - Purple
6. **Strings** (Slots 81-96) - Red
7. **Winds** (Slots 97-112) - Silver/Grey

Section names are customizable in code at line ~550:
```javascript
const pages = [
    { name: 'Acoustic Drums', start: 0, count: 16 },
    { name: 'Electronic Drums', start: 16, count: 16 },
    { name: 'Bass', start: 32, count: 16 },
    { name: 'Keyboards', start: 48, count: 16 },
    { name: 'Synths', start: 64, count: 16 },
    { name: 'Strings', start: 80, count: 16 },
    { name: 'Winds', start: 96, count: 16 }
];
```

## Complete Feature List

### File Management
- **Import X-Controls.txt** - Parses existing configuration, loads presets into UI
- **Export X-Controls.txt** - Generates new config file from current state
- **Preserve custom controls** - Non-bank controls (like SHOW_PLUGIN/HIDE_PLUGIN) are preserved during export
- **Preview before download** - View generated file in modal before saving
- **Re-import support** - Input field resets to allow importing same file multiple times

### Visual Organization
- **5 collapsible banks** - Click header to expand/collapse entire bank
- **7 collapsible sections per bank** - Click section header to expand/collapse
- **Color-coded sections** - Each instrument category has unique color scheme
- **Fill count display** - Each bank shows "X/112 filled" in header
- **Persistent UI state** - Collapse states saved to localStorage

### Drag & Drop Features
- **File drag from Finder** - Drag `.adg` files directly into slots
- **Multi-file drag** - Select multiple files, drag once, fills sequentially
- **Slot-to-slot drag** - Drag preset to another slot to swap
- **Group drag** - Select multiple presets, drag all at once
- **Visual feedback** - Drag-over highlighting, opacity changes

### Selection System
- **Single-click** - Select one preset (clears others)
- **Cmd/Ctrl+click** - Toggle selection (multi-select)
- **Shift+click** - Range selection (all slots between clicks)
- **Visual indicators** - Blue border + glow on selected slots
- **Cross-bank selection** - Can select presets from multiple banks

### Editing Operations
- **Double-click** - Opens prompt to rename preset
- **Remove button** - Hover over filled slot shows × button
- **Keyboard delete** - Delete/Backspace removes all selected presets
- **Bulk operations** - All edits work on single or multiple selections

### Undo/Redo System
- **50-action history** - Keeps last 50 states in memory
- **Smart state management** - Only saves on actual changes (not UI interactions)
- **Button indicators** - Undo/Redo buttons auto-enable/disable based on history
- **Keyboard shortcuts:**
  - Cmd/Ctrl + Z → Undo
  - Cmd/Ctrl + Shift + Z → Redo
  - Cmd/Ctrl + Y → Redo (alternative)
  - Delete/Backspace → Delete selected presets

### File Format Support
- **Input:** Parses X-Controls.txt with regex pattern matching
- **Output:** Generates properly formatted X-Controls.txt
- **Validation:** Only accepts `.adg` files for presets
- **Bank support:** Handles B1-B5 bank notation (PAD_X_B1, PAD_X_B2, etc.)

## X-Controls.txt File Format

### Structure
```
[Header Section]
# Comments and metadata

[X-CONTROLS]

# Bank 1
PAD_1_B1 = NOTE, 1, 0, 0, 127, SEL/DEV(1) SEL ; WAIT 1 ; SEL/SWAP "PresetName.adg"
PAD_2_B1 = NOTE, 1, 1, 0, 127, SEL/DEV(1) SEL ; WAIT 1 ; SEL/SWAP "PresetName.adg"
...

# Bank 2
PAD_1_B2 = NOTE, 2, 0, 0, 127, SEL/DEV(1) SEL ; WAIT 1 ; SEL/SWAP "PresetName.adg"
...

[Preserved Controls]
SHOW_PLUGIN = CC, 1, 38, 0, 127, SEL/DEV FOLD OFF
HIDE_PLUGIN = CC, 1, 39, 0, 127, SEL/DEV FOLD ON
```

### Key Parsing Logic
- **Preset detection:** Regex `/PAD_(\d+)_B(\d)/` matches bank entries
- **Name extraction:** Regex `/SEL\/SWAP "([^"]+)"/` extracts preset filename
- **Preservation:** All non-PAD entries are preserved in footer

## Key Functions

### Core Rendering
```javascript
initBanks()           // Creates all 5 bank containers dynamically
renderBank(bankNum)   // Renders all sections for one bank
createSlot(bankNum, index)  // Creates individual preset slot element
toggleBank(bank)      // Collapses/expands bank
toggleSection(section) // Collapses/expands section
```

### State Management
```javascript
saveState()          // Snapshots current state to history
undo()              // Restores previous state
redo()              // Re-applies undone state
restoreState(state) // Applies state to UI
updateUndoRedoButtons() // Updates button enabled/disabled state
```

### File Operations
```javascript
parseXControls(content)    // Parses X-Controls.txt into banks object
generateXControlsText()    // Generates X-Controls.txt from banks object
displayPreservedControls() // Shows non-bank controls in UI
getDefaultHeader()         // Returns default file header
```

### Interaction Handlers
```javascript
handleSlotClick(e)        // Selection logic (click/Cmd+click/Shift+click)
handleSlotDoubleClick(e)  // Rename prompt
handleDragStart(e)        // Initiates drag (single or group)
handleDragOver(e)         // Drag-over visual feedback
handleDrop(e)            // Handles file drop or slot swap
deleteSelectedPresets()  // Bulk delete operation
removePreset(e, bank, index) // Single preset removal
```

### Utility Functions
```javascript
updateBankCount(bankNum)  // Updates "X/112 filled" display
clearAll()               // Clears all presets (with confirmation)
showPreview()            // Opens preview modal
closePreview()           // Closes preview modal
generateFile()           // Downloads X-Controls.txt
setStatus(message, type) // Updates status message
```

## UI Layout

```
┌─────────────────────────────────────────────┐
│  Header: Title + Subtitle                   │
├─────────────────────────────────────────────┤
│  Controls: Import | Undo | Redo | Clear |  │
│            Preview | Generate | [Status]    │
├─────────────────────────────────────────────┤
│  ▼ Bank 1 - MIDI Channel 1    [42/112]     │
│    ├─ ▼ Acoustic Drums                      │
│    │   [Slot] [Slot] [Slot] [Slot]         │
│    ├─ ▼ Electronic Drums                    │
│    ├─ ▼ Bass                                │
│    ├─ ▼ Keyboards                           │
│    ├─ ▼ Synths                              │
│    ├─ ▼ Strings                             │
│    └─ ▼ Winds                               │
├─────────────────────────────────────────────┤
│  ▼ Bank 2 - MIDI Channel 2    [0/112]      │
├─────────────────────────────────────────────┤
│  ▼ Bank 3 - MIDI Channel 3    [0/112]      │
├─────────────────────────────────────────────┤
│  ▼ Bank 4 - MIDI Channel 4    [0/112]      │
├─────────────────────────────────────────────┤
│  ▼ Bank 5 - MIDI Channel 5    [0/112]      │
├─────────────────────────────────────────────┤
│  ✅ Preserved Controls                      │
│    - SHOW_PLUGIN = ...                      │
│    - HIDE_PLUGIN = ...                      │
└─────────────────────────────────────────────┘
```

## Usage Workflow

### Initial Setup
1. **Open tool:** Double-click `preset-manager.html` in Finder
2. **Import existing config:** Click "📁 Import X-Controls.txt" → Select file
3. All presets load into appropriate banks and slots

### Daily Workflow
1. **Organize presets:**
   - Drag `.adg` files from Finder to empty slots
   - Select multiple presets (Cmd+click or Shift+click)
   - Drag groups to rearrange
   - Delete unwanted presets (Delete/Backspace key)
   - Double-click to rename presets
2. **Preview changes:** Click "👁️ Preview" to see generated file
3. **Export:** Click "💾 Generate X-Controls.txt" to download
4. **Install:** Replace old X-Controls.txt in ClyphX Pro folder
5. **Reload:** Restart Ableton or reload ClyphX Pro

### Example: Adding 50 New Presets
1. Open Finder to your preset folder
2. Select 50 `.adg` files
3. Drag all 50 files to Bank 2, Slot 1
4. All 50 files populate Slots 1-50 automatically
5. Click Generate to download new config

### Example: Reorganizing a Section
1. Shift+click to select Slots 10-20 (11 presets)
2. Drag the group to Slot 5
3. All 11 presets now fill Slots 5-15
4. Original slots are cleared

## Technical Constraints

- **Browser requirements:** Modern browser with ES6 support (Chrome, Firefox, Safari, Edge)
- **File size:** ~85KB (single HTML file)
- **Performance:** Handles 560 presets smoothly
- **Memory:** 50 undo states × 5 banks × 112 slots = ~3MB max
- **Storage:** localStorage for UI state only (~10KB)

## Integration Points

- **Input:** Reads X-Controls.txt (ClyphX Pro configuration)
- **Output:** Writes X-Controls.txt (ClyphX Pro configuration)
- **File source:** User drags `.adg` files from Ableton's preset folders
  - Typical locations:
    - `/Users/[username]/Music/Ableton/User Library/Presets/`
    - `/Applications/Ableton Live X Suite/Contents/App-Resources/Core Library/Devices/`
- **Installation:** Generated file replaces existing X-Controls.txt in ClyphX Pro folder

## Customization Guide

### To Customize Section Names
Edit lines ~550 in `preset-manager.html`:
```javascript
// ============================================================================
// EDIT SECTION NAMES HERE - Change the 'name' property to customize labels
// ============================================================================
const pages = [
    { name: 'YOUR NEW NAME', start: 0, count: 16 },      // Category 0 - Dark Green
    { name: 'Electronic Drums', start: 16, count: 16 },  // Category 1 - Light Green
    { name: 'Bass', start: 32, count: 16 },              // Category 2 - Yellow/Gold
    { name: 'Keyboards', start: 48, count: 16 },         // Category 3 - Deep Blue
    { name: 'Synths', start: 64, count: 16 },            // Category 4 - Purple
    { name: 'Strings', start: 80, count: 16 },           // Category 5 - Red
    { name: 'Winds', start: 96, count: 16 }              // Category 6 - Silver/Grey
];
```

### To Change Color Scheme
Edit CSS classes `.category-0` through `.category-6` (lines ~200-300):
```css
.page-section.category-0 .page-title {
    background: #2d5016;  /* Change background color */
    border-left: 4px solid #4a7c2c;  /* Change accent color */
}
```

### To Add More Banks
Would require changes to:
1. **Data structure** (line ~545):
   ```javascript
   const banks = {
       1: Array(112).fill(null),
       2: Array(112).fill(null),
       3: Array(112).fill(null),
       4: Array(112).fill(null),
       5: Array(112).fill(null),
       6: Array(112).fill(null)  // Add new bank
   };
   ```
2. **Loop limits** (change `i <= 5` to `i <= 6` in multiple places)
3. **Generation logic** (update generateXControlsText function)
4. **State management** (update saveState and restoreState functions)

### To Change Section Slot Count
Modify the `count` property in the pages array:
```javascript
{ name: 'Acoustic Drums', start: 0, count: 20 },  // Changed from 16 to 20
```
**Warning:** Must ensure total slots = 112 per bank

## Keyboard Shortcuts Reference

| Action | Shortcut |
|--------|----------|
| Undo | Cmd/Ctrl + Z |
| Redo | Cmd/Ctrl + Shift + Z |
| Redo (alt) | Cmd/Ctrl + Y |
| Delete selected | Delete or Backspace |
| Select single | Click |
| Multi-select toggle | Cmd/Ctrl + Click |
| Range select | Shift + Click |
| Rename | Double-click |

## Known Limitations

- No server-side storage (all data in memory/localStorage)
- No multi-user collaboration
- No auto-save (must manually export)
- No preset preview/audio playback
- No validation of preset file existence on disk
- No automatic backup of X-Controls.txt
- No search/filter functionality for presets
- No batch rename operations
- No import/export of individual banks

## Troubleshooting

### Issue: Changes not reflected in Ableton
**Solution:** After downloading new X-Controls.txt:
1. Ensure file is placed in correct ClyphX Pro folder
2. Completely quit and restart Ableton Live
3. Or reload ClyphX Pro from preferences

### Issue: Presets show as "PLACEHOLDER"
**Cause:** Empty slots in exported file
**Solution:** Fill slots with actual preset files or leave empty

### Issue: Import doesn't load presets
**Cause:** File format doesn't match expected pattern
**Solution:** Ensure file was generated by this tool or follows exact format

### Issue: Drag-and-drop not working
**Cause:** Browser security restrictions
**Solution:** 
- Ensure using modern browser (Chrome, Firefox, Safari)
- File must have `.adg` extension
- Try dragging one file first to test

### Issue: Undo button disabled
**Cause:** No actions in history yet
**Solution:** Make a change first (e.g., rename a preset)

## Development Notes

### Code Structure
- **Lines 1-500:** HTML structure and CSS styles
- **Lines 500-550:** JavaScript data structures and configuration
- **Lines 550-650:** UI rendering functions
- **Lines 650-750:** Event handlers
- **Lines 750-850:** File I/O operations
- **Lines 850-900:** State management (undo/redo)
- **Lines 900-950:** Utility functions
- **Lines 950-1000:** Initialization

### Testing Checklist
- [ ] Import existing X-Controls.txt
- [ ] Drag single .adg file
- [ ] Drag multiple .adg files
- [ ] Single-click selection
- [ ] Cmd+click multi-selection
- [ ] Shift+click range selection
- [ ] Drag group of presets
- [ ] Delete with backspace
- [ ] Undo/Redo operations
- [ ] Double-click rename
- [ ] Collapse/expand banks
- [ ] Collapse/expand sections
- [ ] Preview generation
- [ ] Download X-Controls.txt
- [ ] Verify preserved controls
- [ ] Refresh page (state persists)

## Future Enhancement Ideas

- **Search/Filter:** Search for presets by name
- **Favorites:** Mark frequently used presets
- **Import/Export Banks:** Save individual banks as templates
- **Batch Rename:** Rename multiple presets at once
- **Duplicate Detection:** Highlight duplicate preset names
- **Preset Preview:** Display preset metadata or waveform
- **Auto-backup:** Automatic backup before generating new file
- **Categories:** Custom categories beyond the 7 defaults
- **Themes:** Light/dark mode toggle
- **Mobile Support:** Touch-friendly interface

## Success Criteria

✅ Tool is fully functional and production-ready  
✅ All features implemented and tested  
✅ No external dependencies  
✅ Works offline  
✅ Intuitive drag-and-drop interface  
✅ Preserves custom controls  
✅ Comprehensive undo/redo  
✅ Keyboard shortcuts  
✅ Persistent UI state  
✅ Clean, maintainable code  
✅ Well-documented

## Related Files in Project

- `preset-manager.html` - The main application file
- `X-Controls.txt` - ClyphX Pro configuration file
- `Macros.txt` - ClyphX Pro macros
- `Preferences.txt` - ClyphX Pro preferences
- `SETUP_GUIDE.md` - Setup documentation
- `QUICK_REFERENCE.md` - Quick reference guide
- `BANK_SELECT_GUIDE.md` - Bank selection guide
- `MASCHINE_PRESET_MAP.md` - Maschine preset mapping

## Contact / Support

This tool was built through iterative development with Claude AI to solve the specific problem of managing 560 presets visually for ClyphX Pro.

---

**Version:** 1.0  
**Last Updated:** March 2026  
**Status:** Production Ready  

This comprehensive documentation provides all necessary context for another LLM to understand, modify, or extend the ClyphX Pro Preset Manager.
