# Maschine MK3 - ClyphX Pro Quick Reference

## Pad Layout - Page 1 (C-2 to D#-1)

```
┌─────────────┬─────────────┬─────────────┬─────────────┐
│   PAD 13    │   PAD 14    │   PAD 15    │   PAD 16    │
│    C-1      │    C#-1     │    D-1      │    D#-1     │
│             │             │             │             │
│  PRESET 10  │  PRESET 11  │  PRESET 12  │  DUPLICATE  │
│ (PLACEHOLDER)│(PLACEHOLDER)│(PLACEHOLDER)│    TRACK    │
└─────────────┴─────────────┴─────────────┴─────────────┘

┌─────────────┬─────────────┬─────────────┬─────────────┐
│   PAD 9     │   PAD 10    │   PAD 11    │   PAD 12    │
│    G#-2     │    A-2      │    A#-2     │    B-2      │
│             │             │             │             │
│  PRESET 6   │  PRESET 7   │  PRESET 8   │  PRESET 9   │
│(PLACEHOLDER)│(PLACEHOLDER)│(PLACEHOLDER)│(PLACEHOLDER)│
└─────────────┴─────────────┴─────────────┴─────────────┘

┌─────────────┬─────────────┬─────────────┬─────────────┐
│   PAD 5     │   PAD 6     │   PAD 7     │   PAD 8     │
│    E-2      │    F-2      │    F#-2     │    G-2      │
│             │             │             │             │
│ 2 - 808s VST│  PRESET 3   │  PRESET 4   │  PRESET 5   │
│             │(PLACEHOLDER)│(PLACEHOLDER)│(PLACEHOLDER)│
└─────────────┴─────────────┴─────────────┴─────────────┘

┌─────────────┬─────────────┬─────────────┬─────────────┐
│   PAD 1     │   PAD 2     │   PAD 3     │   PAD 4     │
│    C-2      │    C#-2     │    D-2      │    D#-2     │
│             │             │             │             │
│   HOTSWAP   │    NEXT     │  PREVIOUS   │ 1 - 808s VST│
│   BROWSER   │   PRESET    │   PRESET    │             │
└─────────────┴─────────────┴─────────────┴─────────────┘
```

---

## Function Summary

### Core Functions
| Pad | Note | Function | Description |
|-----|------|----------|-------------|
| 1 | C-2 | **HOTSWAP** | Opens preset browser for selected track |
| 2 | C#-2 | **NEXT →** | Navigate to next preset |
| 3 | D-2 | **← PREV** | Navigate to previous preset |
| 16 | D#-1 | **DUPLICATE** | Duplicate selected track |

### Configured Presets
| Pad | Note | Preset Name |
|-----|------|-------------|
| 4 | D#-2 | 1 - 808s VST |
| 5 | E-2 | 2 - 808s VST |

### Placeholder Slots (Replace with your presets)
| Pad | Note | Current Placeholder |
|-----|------|---------------------|
| 6 | F-2 | YOUR_PRESET_NAME_3 |
| 7 | F#-2 | YOUR_PRESET_NAME_4 |
| 8 | G-2 | YOUR_PRESET_NAME_5 |
| 9 | G#-2 | YOUR_PRESET_NAME_6 |
| 10 | A-2 | YOUR_PRESET_NAME_7 |
| 11 | A#-2 | YOUR_PRESET_NAME_8 |
| 12 | B-2 | YOUR_PRESET_NAME_9 |
| 13 | C-1 | YOUR_PRESET_NAME_10 |
| 14 | C#-1 | YOUR_PRESET_NAME_11 |
| 15 | D-1 | YOUR_PRESET_NAME_12 |

---

## Live Performance Workflow

### Basic Loop Building
1. **SELECT** track with Push 2
2. **LOAD PRESET** (Pad 1-3 for browsing, or Pads 4-15 for instant recall)
3. **RECORD** loop with Push 2
4. **DUPLICATE** track (Pad 16)
5. **LOAD NEW PRESET** on duplicated track
6. **RECORD** another loop
7. **REPEAT** to build layers!

### Preset Loading Options

**Option 1: Browse Mode**
- Press **Pad 1** → Opens hotswap browser
- Use Live's browser to find preset
- Press Enter to load

**Option 2: Quick Navigation**
- Select track with plugin loaded
- Press **Pad 2** (next) or **Pad 3** (prev)
- Cycles through plugin's presets

**Option 3: Instant Recall**
- Press **Pads 4-15** directly
- Loads pre-configured preset immediately
- Fastest method for live performance!

---

## ClyphX Pro Actions Reference

### SWAP Action
**Format**: `SEL/SWAP "Preset Name"`
- Loads named preset on selected track
- Works with Live Device Presets (.adv files)
- Case-sensitive naming
- Don't include .adv extension

### DEV PRESET Action
**Format**: `SEL/DEV PRESET >` or `SEL/DEV PRESET <`
- Navigates through presets
- Requires plugin preset browser to be exposed
- Works with most VSTs

### DUPE Action
**Format**: `SEL/DUPE`
- Duplicates selected track
- Includes all clips, devices, and settings
- New track appears to the right

---

## Customization Guide

### To Add New Preset to Empty Slot:

1. **Save the preset** in Live:
   - Load VST and sound
   - Right-click plugin title bar → Save Preset
   - Name it clearly (e.g., "Bass - Deep Sub")

2. **Edit X-Controls.txt**:
   - Find placeholder line (e.g., `PRESET_SLOT_3`)
   - Replace `"YOUR_PRESET_NAME_3"` with `"Bass - Deep Sub"`
   - Save file

3. **Reload Live Set**:
   - Close and reopen Set
   - OR create new Set

4. **Test**:
   - Select track
   - Press the corresponding pad
   - Preset should load instantly!

---

## MIDI Channel Configuration

**Current Setting**: Channel 1

If your Maschine uses a different channel, edit X-Controls.txt:
- Change `NOTE, 1, X` to `NOTE, [your_channel], X`
- Example: For channel 10, use `NOTE, 10, X`

---

## Tips & Tricks

### Performance Tips
✅ Organize presets by type (Bass, Pads, Leads, etc.)  
✅ Put your most-used sounds on Pads 4-8 (easiest to reach)  
✅ Use descriptive names (easier to remember)  
✅ Keep a backup of X-Controls.txt before major changes

### Naming Best Practices
✅ Use consistent naming: "Category - Sound Name"  
✅ Examples: "Bass - Deep Sub", "Pad - Ethereal", "Lead - Bright"  
✅ Avoid generic names like "Preset 1" or "Sound A"

### Workflow Optimization
✅ Use **Pad 1** (hotswap) for exploration/practice  
✅ Use **Pads 4-15** (instant recall) for live performance  
✅ Use **Pad 16** (duplicate) to preserve loops while changing sounds  
✅ Use **Push 2** for track selection and clip recording

---

## Troubleshooting Quick Fixes

| Problem | Quick Fix |
|---------|-----------|
| Pad does nothing | Reload Live Set after editing X-Controls.txt |
| Wrong preset loads | Check exact spelling (case-sensitive!) |
| Can't find preset | Verify preset exists in Live's Browser |
| Navigation doesn't work | Unfold plugin parameters (triangle button) |
| Wrong track affected | Use Push 2 to select correct track first |

---

## Files Location

**Settings Folder:**
- Mac: `~/nativeKONTROL/ClyphX_Pro/`
- Windows: `%USERPROFILE%\nativeKONTROL\ClyphX_Pro\`

**Key Files:**
- `X-Controls.txt` - Pad configuration
- `SETUP_GUIDE.md` - Complete setup instructions
- `QUICK_REFERENCE.md` - This document

---

## Next Steps

1. ✅ X-Controls.txt configured
2. ✅ Live Preferences set up (see SETUP_GUIDE.md)
3. ⬜ Reload Live Set
4. ⬜ Test each pad function
5. ⬜ Save your favorite presets
6. ⬜ Replace placeholders in X-Controls.txt
7. ⬜ Practice workflow
8. ⬜ Perform! 🎹

---

**Need help?** See `SETUP_GUIDE.md` for detailed instructions and troubleshooting.
