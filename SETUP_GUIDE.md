# ClyphX Pro + Maschine MK3 Setup Guide

## Overview
This guide will help you configure ClyphX Pro to work with your Maschine MK3 for instant preset switching in Ableton Live.

---

## Step 1: Configure Ableton Live Preferences

1. Open **Ableton Live Preferences** (Cmd+, on Mac / Ctrl+, on Windows)
2. Navigate to the **Link/MIDI** tab
3. Locate **ClyphX Pro** in the Control Surface list (it should already be there)
4. Set the following:
   - **Input**: `Maschine MK3`
   - **Output**: `Maschine MK3` (for LED feedback)
5. **Keep your existing Maschine settings** for Track and Remote switches as they are

---

## Step 2: Reload Live Set

After saving X-Controls.txt, you **must** reload your Live Set for changes to take effect:

- **Option A**: Close and reopen your Live Set
- **Option B**: Create a new Live Set
- **Option C**: Use File > Open Recent to reload current Set

---

## Step 3: Verify Installation

1. Check Live's **Status Bar** (bottom of screen) for any error messages
2. If you see a **colored ring around the selected Clip Slot**, ClyphX Pro is working
3. If you see errors, refer to the Troubleshooting section below

---

## Step 4: Prepare Your Presets

For the SWAP action to find your presets, they must be saved as **Live Device Presets**:

### How to Save Presets:

1. Load your VST (Omnisphere, Keyscape, etc.) on a track
2. Load the sound/preset you want inside the VST
3. **Right-click the plugin's title bar** in Live
4. Select **"Save Preset"**
5. **Name it clearly** (e.g., "Omni - Epic Pad", "Keys - Bright Piano")
6. This creates a `.adv` file in your User Library

### Preset Naming Requirements:

- ✅ Use exact capitalization (case-sensitive)
- ✅ Include numbers, spaces, dashes as they appear (e.g., "1 - 808s VST")
- ❌ Do NOT include the .adv file extension in X-Controls.txt
- ✅ Presets can be in subfolders (e.g., "Omnisphere/Epic Pad")

### Where Presets Are Stored:

**Mac**: `~/Music/Ableton/User Library/Presets/Instruments/`
**Windows**: `%USERPROFILE%\Documents\Ableton\User Library\Presets\Instruments\`

---

## Step 5: Customize Your Preset Slots

Open `X-Controls.txt` and replace the placeholders with your actual preset names:

### Current Configuration:

```
PRESET_SLOT_3 = NOTE, 1, 5, 0, 127, SEL/SWAP "YOUR_PRESET_NAME_3"
```

### Replace with your preset name:

```
PRESET_SLOT_3 = NOTE, 1, 5, 0, 127, SEL/SWAP "Omni - Epic Pad"
```

**Important**: After editing X-Controls.txt, you must reload your Live Set!

---

## Step 6: Test Your Configuration

### Test 1 - Hotswap Mode (Pad 1)
1. Select any track with a device
2. Press **Pad 1** (C-2) on Maschine
3. **Expected**: Browser opens in hotswap mode
4. ✅ Working / ❌ See Troubleshooting

### Test 2 - Preset Navigation (Pads 2-3)
1. With a device selected, press **Pad 2** or **Pad 3**
2. **Expected**: Cycles forward/backward through presets
3. ✅ Working / ❌ See Troubleshooting

### Test 3 - Load Specific Presets (Pads 4-5)
1. Select an empty track (or existing track with device)
2. Press **Pad 4** (D#-2)
3. **Expected**: Loads "1 - 808s VST"
4. Press **Pad 5** (E-2)
5. **Expected**: Loads "2 - 808s VST"
6. ✅ Working / ❌ See Troubleshooting

### Test 4 - Track Duplication (Pad 16)
1. Select a track with clips/devices
2. Press **Pad 16** (D#-1)
3. **Expected**: Track is duplicated
4. ✅ Working / ❌ See Troubleshooting

---

## Your Live Performance Workflow

Once everything is configured and tested, here's your performance workflow:

1. **Use Push 2** to select which track you want to work with
2. **Press Maschine Pad 1** to open hotswap browser (for exploring)
   - OR press **Pads 2-3** to navigate presets
   - OR press **Pads 4-15** for instant preset recall
3. **Record loops** using Push 2
4. **Press Pad 16** when you want to duplicate the track
5. **Switch to the new track** (with Push 2 or Pads 2-3)
6. **Load a different preset** on the duplicated track
7. **Repeat** to build layers!

---

## Troubleshooting

### Issue: Pads don't trigger any actions

**Solutions**:
- Verify Maschine is sending on **MIDI Channel 1**
  - Check in Live by creating a MIDI track and watching the status bar
- Confirm **ClyphX Pro Input** is set to "Maschine MK3"
- Ensure you **reloaded the Live Set** after saving X-Controls.txt

### Issue: SWAP can't find presets

**Solutions**:
- Verify **exact preset name** (case-sensitive)
- Check that preset appears in **Live's Browser** under User Library
- Try using **just the preset name** first: `SEL/SWAP "Epic Pad"`
- If there are naming conflicts, use the **folder path**: `SEL/SWAP "Omnisphere/Epic Pad"`
- Make sure you're **not including the .adv extension**

### Issue: DEV PRESET > doesn't work

**Solutions**:
- The plugin's **preset browser must be exposed** (visible) in Live
- Not all plugins support this navigation
- Try clicking the **Unfold Device Parameters** button (triangle) on the plugin

### Issue: Nothing happens at all

**Solutions**:
1. Create an **X-Clip** named `[] DEBUG` in Session View
2. Launch the X-Clip to enable debug mode
3. Trigger your Maschine pads
4. Close Live (or load new Set)
5. Check **Debug Log.txt** in the ClyphX_Pro settings folder
6. Look for error messages indicating what's wrong

### Issue: Wrong MIDI channel

**Solutions**:
- If Maschine is sending on a different channel, edit X-Controls.txt
- Change the CHANNEL parameter from `1` to the correct channel
- Example: If channel 10, change `NOTE, 1, 0` to `NOTE, 10, 0`

---

## Adding More Presets

To add more presets to your empty slots:

1. **Save presets** from your VSTs as Live Device Presets
2. Open **X-Controls.txt**
3. Find the placeholder line (e.g., `PRESET_SLOT_3`)
4. Replace `"YOUR_PRESET_NAME_3"` with your actual preset name
5. **Save** X-Controls.txt
6. **Reload** your Live Set

---

## Advanced: Using Multiple Pad Pages

Your Maschine MK3 has 8 pages of 16 pads. Currently, only Page 1 is configured.

To configure additional pages:

1. Note the **MIDI note numbers** for each pad page in your Maschine Controller Editor
2. Add new X-Control entries in X-Controls.txt with those note numbers
3. Reload Live Set

**Example for Page 2** (if starting at note 16):
```
# PAGE 2 - UTILITY FUNCTIONS
MUTE_TRACK = NOTE, 1, 16, 0, 127, SEL/MUTE
ARM_TRACK = NOTE, 1, 17, 0, 127, SEL/ARM
[etc...]
```

---

## Quick Tips

### Best Practices:
- ✅ Use **clear, descriptive preset names** (e.g., "Bass - Deep Sub" not "Preset 1")
- ✅ Organize presets in **subfolders** by instrument type
- ✅ Test presets in the Browser first before adding to X-Controls
- ✅ Keep a **backup** of X-Controls.txt before making major changes

### Performance Tips:
- Use **Pad 1** (hotswap) when you want to explore sounds
- Use **Pads 2-3** (navigation) when browsing through a plugin's presets
- Use **Pads 4-15** (specific presets) for your most-used sounds
- Use **Pad 16** (duplicate) to layer sounds while preserving your original loop

---

## Need Help?

**ClyphX Pro Support:**
- Submit ticket: https://isotonikstudios.com/my-account/
- Forum: Beatwise Network

**Debug Mode:**
- Launch X-Clip named `[] DEBUG`
- Check `Debug Log.txt` in ClyphX_Pro settings folder
- Include log when asking for help

---

## Files Modified in This Setup

- `X-Controls.txt` - Your Maschine pad configuration
- `SETUP_GUIDE.md` - This document
- `QUICK_REFERENCE.md` - Pad layout reference (see next)

**Settings Folder Location:**
`~/nativeKONTROL/ClyphX_Pro/` (Mac)
`%USERPROFILE%\nativeKONTROL\ClyphX_Pro\` (Windows)
