# Toucan2 ZMK config

ZMK firmware config for the beekeeb Toucan2: a wireless split keyboard with 42 keys. The right half has a trackpad and the left half has a nice_view_gem display. The keymap was migrated from a Sofle. The user types on a **Polish** layout.

History of every change (what changed, why, how to test it): see `CHANGELOG.md`.

## Files that matter
- `config/toucan.keymap`: the **active** keymap. It overrides `boards/shields/toucan/toucan.keymap`, so don't edit that one.
- `config/toucan_left.conf` / `config/toucan_right.conf`: idle and sleep settings (light idle 30 s, deep sleep 60 min).
- `boards/shields/toucan/toucan.dtsi`: trackpad scaler (`&zip_xy_scaler 1 1`) and swipe mapper.
- `boards/shields/toucan/toucan_right.overlay`: trackpad (`tps43_trackpad`, `timeout-lp1 = 9`, `report-rate-lp2 = 320`).
- `build.yaml`: GitHub Actions matrix. Builds the left half, the right half and `settings_reset`.
- `docs/reference/`: old Sofle config, kept for reference only.

## Key positions
- Row 0: positions 0–11.
- Row 1: positions 12–23.
- Row 2: positions 24–35.
- Thumbs: positions 36–41.
- Left hand = columns 0–5 of each row (0–5, 12–17, 24–29). Right hand = columns 6–11.

## Layers
- BASE 0, NUM 1 (left middle thumb, tap = DEL), NAV 2 (right middle thumb, tap = BSPC).
- Left outer thumb: plain LCTRL (no tap action).
- Right outer thumb: hold = RALT, tap = DEL (`mt_bal`, balanced flavor so RALT+A = ą never becomes DEL).
- Layer-tap (`&lt`, NUM and NAV): tapping term 170 ms, quick-tap 175 ms.
- ADJ 3 is a tri-layer (NUM + NAV): Bluetooth, output toggle, media.
- `extra_2` / `extra_3`: reserved and empty.
- Mouse layer: G/B are `&trans`. The cursor speed keys were removed because they caused jitter.

## Home-row mods
- `hml` = A S D F, `hmr` = J K L ;.
- Order from the outside in: GUI, ALT, CTRL, SHIFT (mirrored on the right). The NAV layer uses the same order.
- Settings: `tap-preferred`, tapping term 270 ms, quick-tap 175 ms, prior-idle 70 ms.
- Opposite-hand rule (`hold-trigger-key-positions`); thumbs count as opposite for both hands.
- The user is getting used to this setup. **Don't change HRM timing, flavor or the rule without asking.**
- Never put RALT on the home row: it is AltGr on Polish layouts and types diacritics.

## Build and flash
- Push to GitHub, then download the firmware artifacts from Actions.
- If something misbehaves after flashing (pairing, lag, repeated keys), follow the `settings_reset` procedure in `CHANGELOG.md` (2026-11-17).

## Working rules
- Log every change at the **top** of `CHANGELOG.md`: the date, the files changed, what changed and why, and what to test after flashing.
- Commit messages follow `00N short description` (next is 007).
- Commit and push only when the user asks.
- Don't create `.bak` files. Git history holds old versions.
- Explain changes in plain language. The user tests on the real keyboard.
