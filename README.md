# Akai APCmini Launchpad

The launchpad sends a 40 byte payload over USB on each event.

The first two bytes determine the event type:

- `09 90` button press
- `08 80` button release
- `0b b0` slider move

The third byte identifies the button or the slider causing the event.

The fourth byte represents the value of the button or slider:

- Button press or release: always `7f`
- Slider move: `00` through `7f` with an increment of 1

The remaining 36 bytes is always all zeroes.

## Grid Buttons

| Row / Column | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
|--------------|------|------|------|------|------|------|------|------|
| 1            | `38` | `39` | `3a` | `3b` | `3c` | `3d` | `3e` | `3f` |
| 2            | `30` | `31` | `32` | `33` | `34` | `35` | `36` | `37` |
| 3            | `28` | `29` | `2a` | `2b` | `2c` | `2d` | `2e` | `2f` |
| 4            | `20` | `21` | `22` | `23` | `24` | `25` | `26` | `27` |
| 5            | `18` | `19` | `1a` | `1b` | `1c` | `1d` | `1e` | `1f` |
| 6            | `10` | `11` | `12` | `13` | `14` | `15` | `16` | `17` |
| 7            | `08` | `09` | `0a` | `0b` | `0c` | `0d` | `0e` | `0f` |
| 8            | `00` | `01` | `02` | `03` | `04` | `05` | `06` | `07` |

## Vertical Buttons

| Byte | Button         |
|------|----------------|
| `52` | Clip Stop      |
| `53` | Solo           |
| `54` | Rec arm        |
| `55` | Mute           |
| `56` | Select         |
| `57` |                |
| `58` |                |
| `59` | Stop All Clips |

## Horizontal Buttons & Sliders

| Function | ⯅ | ⯆ | ⯇ | ⯈ | Volume | Pan | Send | Device | Shift |
|----------|------|------|------|------|------|------|------|------|------|
| Button   | `40` | `41` | `42` | `43` | `44` | `45` | `46` | `47` | `62` |
| Slider   | `30` | `31` | `32` | `33` | `34` | `35` | `36` | `37` | `38` |
