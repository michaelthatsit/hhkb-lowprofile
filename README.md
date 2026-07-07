# HHKB Low-Profile – ZMK Firmware

Custom ZMK firmware configuration for a 60-key HHKB-layout wireless keyboard,
hand-wired to a SuperMini nRF52840 (nice!nano V2 compatible).

## Pin Mapping

### Rows (active-high, output)

| Row | GPIO     | Board Label |
|-----|----------|-------------|
| R0  | &gpio0 6 | 006         |
| R1  | &gpio0 8 | 008         |
| R2  | &gpio0 17| 017         |
| R3  | &gpio0 20| 020         |
| R4  | &gpio0 22| 022         |

### Columns (active-high, pull-down)

| Col | GPIO      | Board Label | Note    |
|-----|-----------|-------------|---------|
| C00 | &gpio0 24 | 024         |         |
| C01 | &gpio1 0  | 100         |         |
| C02 | &gpio0 11 | 011         |         |
| C03 | &gpio1 4  | 104         |         |
| C04 | &gpio1 6  | 106         |         |
| C05 | &gpio0 31 | 031         |         |
| C06 | &gpio0 29 | 029         |         |
| C07 | &gpio0 2  | 002         |         |
| C08 | &gpio1 15 | 115         |         |
| C09 | &gpio1 13 | 113         |         |
| C10 | &gpio1 11 | 111         |         |
| C11 | &gpio0 10 | 010         | NFC pin |
| C12 | &gpio0 9  | 009         | NFC pin |
| C13 | &gpio1 7  | 107         |         |
| C14 | &gpio1 1  | 101         |         |

C11 and C12 require `CONFIG_NFCT_PINS_AS_GPIOS=y` (set in `hhkb.conf`).

Diode direction: **row2col**

## Physical Layout

```
Row 0: Esc  1  2  3  4  5  6  7  8  9  0  -  =  \  `
Row 1: Tab   Q  W  E  R  T  Y  U  I  O  P  [  ]  Bksp
Row 2: Ctrl   A  S  D  F  G  H  J  K  L  ;  '  Enter
Row 3:  LShift  Z  X  C  V  B  N  M  ,  .  /  RShift Fn
Row 4:        Alt  Gui       Space       Gui  Alt
```

60 keys total (15 + 14 + 13 + 13 + 5).

## Fn Layer Highlights

| Key       | Fn + Key            |
|-----------|---------------------|
| 1–0, -, = | F1–F12             |
| \         | Insert              |
| `         | Delete              |
| H J K L   | Left Down Up Right  |
| ; '       | Home End            |
| A S D     | Vol- Vol+ Mute      |
| M , .     | Play/Pause Prev Next|
| I         | Page Up             |
| P         | Page Down           |
| Q–Y       | BT Prof 0–4, BT CLR|
| Bksp      | Bootloader          |

## Building

### GitHub Actions (recommended)

Push to the `main` branch (or open a PR). The workflow at
`.github/workflows/build.yml` runs automatically and produces a firmware
artifact named `nice_nano_v2-hhkb-zmk`.

You can also trigger a build manually from the **Actions** tab →
**Build ZMK firmware** → **Run workflow**.

### Local build

```sh
west init -l config/
west update
west build -s zmk/app -b nice_nano_v2 -- -DSHIELD=hhkb
```

The output UF2 is at `build/zephyr/zmk.uf2`.

## Flashing

1. Connect the SuperMini nRF52840 via USB.
2. Double-tap the **reset** button — the board enters UF2 bootloader mode
   and a USB drive named **NICENANO** appears.
3. Drag `zmk.uf2` onto the **NICENANO** drive.
4. The board reboots automatically with the new firmware.

## Repository Structure

```
├── .github/workflows/build.yml   # GitHub Actions workflow
├── boards/shields/hhkb/
│   ├── hhkb.overlay               # Matrix pins + transform
│   ├── hhkb.keymap                # Keymap (Base + Fn layers)
│   ├── hhkb.conf                  # Shield config (BLE, USB, sleep)
│   ├── Kconfig.shield             # Shield detection
│   ├── Kconfig.defconfig          # Default config
│   └── hhkb.zmk.yml               # ZMK metadata
├── build.yaml                     # Build matrix (nice_nano_v2 + hhkb)
├── config/west.yml                # West manifest (ZMK v0.3)
└── zephyr/module.yml              # Zephyr module registration
```
