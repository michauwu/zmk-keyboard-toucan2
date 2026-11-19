# Toucan2 keymap changelog

## 2026-11-19: NUM / NAV hold time 200 → 170 ms

File changed: `config/toucan.keymap` (`&lt` tapping term).

- Hold the left middle thumb (NUM) or right middle thumb (NAV) for 170 ms instead of 200 ms to turn the layer on.
- Applies to both middle thumbs, because they use the same layer-tap behavior.
- Downside: a slow tap (170–200 ms) now gives the layer instead of Delete / Backspace.

### To test after flashing
- NUM + number and NAV + arrows feel quicker.
- Normal taps still give Delete (left) / Backspace (right). If taps sometimes do nothing, the time is too short.

## 2026-11-19: Left thumbs: plain Ctrl, NUM taps Delete

File changed: `config/toucan.keymap`. Right thumbs and home-row mods are unchanged.

Thumb row, before: `CTRL/DEL | NUM/BSPC | RET || SPACE | NAV/BSPC | RALT/DEL`
Thumb row, after: `CTRL | NUM/DEL | RET || SPACE | NAV/BSPC | RALT/DEL`

- **Left outer thumb:** now plain Ctrl. Tapping it alone does nothing (no more accidental Delete).
- **Left middle thumb:** hold = NUM layer (as before), tap = Delete instead of Backspace. Tap then hold = repeating Delete.
- Backspace is still on the right middle thumb (NAV/BSPC) and the P + - combo.

### To test after flashing
- Tap left outer thumb → nothing. Hold + C / V → copy / paste. Ctrl + click works.
- Tap left middle thumb → Delete. Tap, then hold → Delete repeats. Hold → NUM. Hold both middle thumbs → ADJ.

## 2026-11-18: Backspace / Delete on thumbs

File changed: `config/toucan.keymap`. Home-row mods are unchanged.

Thumb row, before: `CTRL | NUM | RET || SPACE | NAV/BSPC | RALT`
Thumb row, after: `CTRL/DEL | NUM/BSPC | RET || SPACE | NAV/BSPC | RALT/DEL`

- **Left middle thumb:** hold = NUM layer (as before), tap = Backspace. Same as the right NAV/BSPC key. Tap then hold = repeating Backspace.
- **Outer thumbs:** hold = Ctrl / RAlt (as before), tap = Delete.
  - New behavior `mt_bal` (balanced flavor, tapping term 200 ms, quick-tap 175 ms).
  - Press and release another key while holding the thumb → modifier straight away. Fast `ą` (RAlt+A) and Ctrl+C never turn into Delete.
  - Tapping Ctrl or RAlt alone now deletes one character.

### To test after flashing
- Tap left middle thumb → Backspace. Hold it → NUM. Hold both middle thumbs → ADJ.
- Tap either outer thumb → Delete. Tap, then hold → Delete repeats.
- Fast RAlt + A → `ą`, no Delete. Fast Ctrl+C / Ctrl+V work.
- Ctrl + click works.

## 2026-11-17: Trackpad base scaler 1/1 + reset/re-pair

File changed: `zmk-keyboard-toucan2/boards/shields/toucan/toucan.dtsi`.

- `&zip_xy_scaler 100 100` changed to `1 1`. Normal speed is identical.
  - The x100 step overflowed ZMK's 16-bit math on very fast flicks, so the cursor jumped backwards.
- Sluggish trackpad + the same character typed ~10 times = key-release messages arriving late over Bluetooth, not the scaler. Fix after flashing:
  1. Flash `settings_reset` to the left half, then the right half.
  2. Flash the new left/right firmware.
  3. Reset both halves together so they pair with each other.
  4. Remove "Toucan" from Bluetooth on Linux/Windows and pair it again.
- If it still lags: check both batteries, distance to the computer, and USB 3 devices near the Bluetooth radio.

## 2026-11-17: Trackpad speed keys removed

The G/B fast/slow cursor feature (2026-11-14) and the follow-up fix attempt were both removed. After G or B had been used, the cursor jittered and froze until a reset.

- `toucan.dtsi`, `config/toucan.keymap`, `toucan_left.conf` and `toucan_right.conf` are back to commit `bd06b0b` (002), exactly as before.
- Layers 5 and 6 are the reserved `extra_2` / `extra_3` again. On the mouse layer, G and B are `&trans` (they type normally).

## 2026-11-13: Bug fixes + home-row-mod opposite-hand rule

File changed: `zmk-keyboard-toucan2/config/toucan.keymap`.
Previous version: commit `bd06b0b` (002).

### Bug fixes
- **ESC combo removed** (positions 0+1). It was carried over from the Sofle, but position 0 is already ESC on the Toucan. It did nothing except add up to 50 ms of delay to ESC and Q.
- **NAV layer mods now match the base home-row mods.**
  - Before: A=ALT, S=CTRL, D=SHIFT, F=CAPS.
  - After: A=GUI, S=ALT, D=CTRL, F=SHIFT (same as the base layer).
- **Duplicate keys removed** (each now `&trans`):
  - NAV: BSPC (right home outer). PGUP/PGDN are kept on both halves.
  - NUM: second `\` (bottom-right).
- **Right home-row mod on L: RALT → LALT.** RALT acts as AltGr / special-character Option on Polish layouts, so holding L could type a diacritic instead of an Alt shortcut. The RALT thumb key is unchanged.
- **P + − backspace combo** now fires only after a 150 ms pause, and only on the BASE layer. Typing something like `top-level` can no longer produce a backspace.

### ADJ layer (hold NUM + NAV)
- Added on the bottom-left row, under BT0–BT2:
  - **BT previous** (`&bt BT_PRV`) under X.
  - **BT next** (`&bt BT_NXT`) under C.
  - **USB/BT output toggle** (`&out OUT_TOG`) under V.

### Home-row mods
- `hrm` is split into `hml` (left: A S D F) and `hmr` (right: J K L ;).
- **Timing and flavor are unchanged:** `tap-preferred`, tapping term 270 ms, quick-tap 175 ms, prior-idle 70 ms.
- **New: `hold-trigger-key-positions` (opposite-hand rule).**
  - Pressing a same-hand key *before* 270 ms → the home-row key is a letter. A slow roll like "as" can no longer become Cmd+S.
  - Holding *past* 270 ms → a mod with any key, exactly as before. For example, hold D then V gives Ctrl+V.
  - Thumbs count as "opposite hand" for both sides, so mod + Space / Enter / Backspace / clicks works.

### Shift
- **Double-tap left Shift is now Caps Word instead of Caps Lock.**
  - Letters and numbers are capitalised; `_` and Backspace keep it going.
  - Space, Enter, punctuation or most other keys turn it off automatically.
  - Holding Shift is unchanged.
- **Caps Lock is no longer on the keyboard.**

### Not changed
- Mouse layer.
- Sleep / power settings (both `.conf` files and the trackpad overlay).
  - Light idle: 30 s.
  - Deep sleep: 60 min.
  - Trackpad `timeout-lp1 = 9` (3 min) and `report-rate-lp2 = 320`. These are the earlier tweaks that stop the pad feeling slow to wake.
- Flavor and timing of the home-row mods.

### To test after flashing
- ESC and Q respond instantly.
- `top-level` types without a backspace.
- Slow "as" / "df" rolls give letters.
- Hold D ~0.3 s + V → Ctrl+V. Hold `;` + C → Cmd+C.
- Hold L + key → Alt+key, with no special character.
- On NAV, A/S/D/F = GUI/ALT/CTRL/SHIFT.
- Double-tap Shift, type `max_size`, press Space → `MAX_SIZE `, then lowercase again.
