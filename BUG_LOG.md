# Bug Log

## 2026-03-22 (Update): EZdrummer 3.1.2 Still Crashing — Crash Loop on Set Reload

### Summary
After updating EZdrummer to 3.1.2, Ableton still crashes — this time during startup when reloading the last Live Set. The previous crash corrupted EZdrummer's heap memory, and the corrupted state was saved into the Live Set file.

### Crash Details
- **Exception:** `EXC_CRASH (SIGABRT)` — `abort()` called by malloc
- **Crashed Thread 241:** `free_list_checksum_botch` in `libsystem_malloc.dylib`
  - This means EZdrummer corrupted the malloc heap, and the OS detected it on the next allocation
- **Thread 0 (MainThread):** Also stuck inside EZdrummer 3 (`usleep` → EZdrummer 3 code)
  - EZdrummer is blocking the main thread during set reload
- **EZdrummer version:** 3.1.2 (updated from 3.0.x, but still crashing)

### Crash Report Path
```
~/Library/Logs/DiagnosticReports/Live-2026-03-22-162726.ips
```

### Resolution
- Hold **Option** while launching Ableton to bypass the corrupted set
- Or delete: `~/Library/Preferences/Ableton/Live 12.3.6/CrashRecoveryInfo.cfg`
- The EZdrummer 3.1.2 update fixed the *trigger* (Program Change preset swapping) but the *damage* from the previous crash is embedded in the saved set

### Note on EZdrummer Bug Fix
The 3.1.2 release notes confirm: "Using Program Change to select a user preset could cause graphical glitches or a crash if the preset used an EZX other than the current." This fix prevents *new* crashes but cannot repair already-corrupted set state.

---

## 2026-03-22: Ableton Live Crashes When Rapidly Switching Drum Presets

### Summary
Ableton Live 12.3.6 crashes when rapidly pressing Maschine MK3 pads to switch between EZdrummer 3 drum presets via ClyphX Pro X-Controls.

### Symptoms
- Crash occurs when pressing drum pads back-to-back quickly
- Specifically triggered on Acoustic Drums presets (EZdrummer 3-based)
- After crashing, Ableton sometimes crash-loops when trying to reopen the last set

### Root Cause
**EZdrummer 3 plugin bug** — NOT caused by ClyphX Pro or the X-Controls configuration.

Both crash reports were analyzed and neither involves ClyphX Pro:

**Crash 1** (`Live-2026-03-22-153913.ips`):
- Exception: `EXC_BAD_ACCESS (SIGSEGV)` — pointer authentication failure
- Every frame in the crashed thread stack is inside `EZdrummer 3`
- Cause: Memory corruption in EZdrummer 3 when presets are swapped before the plugin finishes loading

**Crash 2** (`Live-2026-03-22-153824.ips`):
- Exception: `EXC_BREAKPOINT (SIGTRAP)`
- Stack: `_os_unfair_lock_corruption_abort` in `libsystem_platform.dylib` → `IOGPU` → `AGXMetalG16X`
- Cause: macOS GPU/Metal driver lock corruption — an OS-level bug unrelated to ClyphX Pro

### Crash Report Locations
```
~/Library/Logs/DiagnosticReports/Live-2026-03-22-153913.ips   (EZdrummer crash)
~/Library/Logs/DiagnosticReports/Live-2026-03-22-153824.ips   (GPU driver crash)
~/Library/Logs/DiagnosticReports/Live-2026-03-21-203455.ips   (earlier crash)
~/Library/Logs/DiagnosticReports/Live-2026-03-21-201946.ips   (earlier crash)
```

### Ableton Log Location
```
~/Library/Preferences/Ableton/Live 12.3.6/Log.txt
```

### Resolution
- Updated EZdrummer 3 (was 2 versions behind) — likely fixes the memory corruption
- The `WAIT 2` in action lists gives Ableton 2 beats between SWAP and NAME/COLOR, but does NOT prevent rapid pad presses from overlapping SWAP operations
- Recommendation: avoid mashing drum pads back-to-back; give each EZdrummer preset ~2 seconds to load

### Environment
- macOS 15.5 (24F74), Apple Silicon (Mac16,11 / ARM64)
- Ableton Live 12.3.6
- ClyphX Pro v1.3.1
- EZdrummer 3 (version was outdated, updated 2026-03-22)
- Controller: Maschine MK3 via Bome MIDI Translator
