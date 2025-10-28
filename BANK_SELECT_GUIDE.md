# Bank Select System Guide

## Overview

Your Maschine MK3 now has **4 banks of 112 presets** each = **448 total presets**! 🎉

## How It Works

- **CC 49** → Load Bank 1 (your current 112 presets - default)
- **CC 50** → Load Bank 2 (112 more presets)
- **CC 51** → Load Bank 3 (112 more presets)
- **CC 52** → Load Bank 4 (112 more presets)

## Usage

1. **On startup**, all pads are loaded with Bank 1 (your existing presets)
2. **Press CC 49-52** on your Maschine to switch banks
3. Live's status bar will show "Bank X Loaded" to confirm
4. **All 112 pads** now trigger the presets from that bank

## Current Status

- ✅ **Bank 1**: Your existing 112 presets (Acoustic Drums, Electronic Drums, Bass, Keyboards, Synths, Strings, Winds)
- 📋 **Bank 2**: Currently duplicates Bank 1 (customize in Macros.txt)
- 📋 **Bank 3**: Currently duplicates Bank 1 (customize in Macros.txt)
- 📋 **Bank 4**: Currently duplicates Bank 1 (customize in Macros.txt)

## How to Customize Banks 2-4

### Option 1: Edit Macros.txt Directly

1. Open `~/nativeKONTROL/ClyphX_Pro/Macros.txt`
2. Find the `$BANK_2_LOAD$` macro (it's one very long line)
3. Replace preset names with your new .adg files
4. Example: Change `"1 - Acoustic Drums.adg"` to `"My Custom Drum Kit.adg"`
5. Save the file
6. Reload your Live Set or trigger a `[] REINIT` X-Clip

### Option 2: Use Find & Replace

Since the bank loader macros are very long, use find & replace:

**Example: Customize Bank 2 to have different Omnisphere presets**

Find: `$BANK_2_LOAD$ = ... "Omnisphere 1.adg" ...`
Replace with: `... "Omnisphere Pad 1.adg" ...`

Do this for all presets you want to change.

## Tips

- **Start with one bank**: Fully customize Bank 2 before moving to Banks 3 & 4
- **Keep preset names exact**: They must match your .adg file names exactly (including spaces and capitalization)
- **Test incrementally**: Change a few presets, test, then continue
- **Back up Macros.txt**: Before making major changes, save a backup copy

## Example Workflow

1. Press **CC 49** → Bank 1 loaded (current presets)
2. Press Pad 1 → "1 - Acoustic Drums.adg" loads
3. Press **CC 50** → Bank 2 loaded
4. Press Pad 1 → Different preset loads (once you've customized Bank 2)
5. Press **CC 49** → Back to Bank 1
6. Press Pad 1 → "1 - Acoustic Drums.adg" loads again

## Technical Details

### Files Modified

- **Macros.txt**: Contains all bank loaders and default pad macros
- **X-Controls.txt**: Updated to use macros instead of direct actions

### How It Works Internally

1. All pads trigger macros (`$PAD_1$` through `$PAD_112$`)
2. Bank select buttons reassign these macros to different presets
3. Macros are stored in memory and persist until changed
4. Default macros (Bank 1) are loaded on startup

### Expanding Beyond 4 Banks

Want even more banks? You can add them!

1. Create new macros: `$BANK_5_LOAD$`, `$BANK_6_LOAD$`, etc.
2. Add new bank select controls in X-Controls.txt using CC 53, 54, etc.
3. Follow the same pattern as Banks 1-4

## Troubleshooting

**Pads not switching sounds after pressing bank button:**
- Make sure you pressed CC 49-52 (not regular pads)
- Check Live's status bar for "Bank X Loaded" message
- Verify Macros.txt was saved and Live Set was reloaded

**Preset name not found error:**
- Check that the .adg file name in Macros.txt matches exactly
- Include file extension: ".adg"
- Match capitalization and spaces exactly

**Want to reset everything:**
- Reload your Live Set - Bank 1 will be active again

## Questions?

- Bank 1 = Your existing workflow (unchanged)
- Banks 2-4 = Exact duplicates until you customize them
- No functionality is broken - everything still works!

**Total capacity: 448 presets across 4 banks!** 🎹🎸🎺🥁
