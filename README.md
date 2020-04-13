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

## With Ableton

Ableton seems to be able to instrument the launchpad to give the button a wider range of values.

When it starts, it sends the following to the launchpad:

- `04 F0 7E 7F 07 06 01 F7`
- `0b b0 xx 6c` for some of the sliders
  - Is this perhaps a new range/increment for the slider?
- `09 90 xx 00` to reset all of the launchpad buttons
- I open a set in Ableton Live
- `04 F0 7E 7F 07 06 01 F7` again
- The launchpad responds with:

```
04 F0 7E 7F 04 06 02 47 04 28 00 19 04 01 00 00
04 00 7F 00 04 00 00 00 04 00 00 00 04 00 00 00
04 00 00 00 04 00 00 00 04 00 00 00 06 00 F7 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
```

- `0b b0` of random values to some/all? of the sliders
- Resets slider buttons again
- Sends `09 90 xx 05` to light buttons yellow or `00` for black
- `04 F0 7E 7F 07 06 01 F7` again
- The response above again
- Some more lighting buttons

```
0B B0 35 59 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
```

- I press a yellow lit launchpad button to play the set
- `02` might be blinky green and `01` might be still green
- For horizontal buttons `01` would be still red

So for each unlit grid button press:

- Launchpad sends `09 90 xx 7f` to indicate button press
- Ableton sends `09 90 xx 02` to blink until playing
- Launchpad sends `08 80 xx 7f` to indicate button release
- Ableton sends `09 90 yy 01` to light the corresponding column button
- Ableton sends `09 90 xx 01` to light the button when it is playing

When the button in the grid column is already on when pressed:

- Launchpad sends "pressed"
- Ableton sends "be blinky"
- Launchpad sends "released"
- Ableton sends "be lit"

When a button is already on in the grid column and another is pressed:

- Launchpad: X is pressed
- Ableton: Blink X
- Launchpad: X is released
- Ableton: Light X
- Ableton: Dim Y (to `05` - yellow not black because if black would not be pressable)

When a button is pressed which is not part of the set:

- Launchpad: pressed
- Launchpad: released
- Ableton doesn't care or respond

When column button pressed with lit button:

- Akai: column button pressed
- Ableton: blink it
- Akai: it was released
- Ableton: dim it
- Ableton: dim (to yellow) the button that was on

When column button has no lit buttons and gets pressed:

- Akai: column button pressed
- Ableton: keep it off
- Akai: it was released

When a slider is moved:

- Akai: spams move messages
- Ableton: "confirms" last value

When a grid row button is pressed:

- Keeps existing row buttons lit green where they were
- Blinks new row buttons green
- Dims existing row buttons to yellow when switched
- Handles changes to the column buttons depending on if they have buttons

Row buttons can able be lit but they are only ever blunk, never lit continuously.

The shift button is never blunk by Ableton but might be blinkable/litable using `libusb`.

## To-Do

### Build a driver to make the launchpad show graphics on the buttons

The marketing photos show even grid buttons glowing red:

https://www.akaipro.com/apc-mini

Perhaps all buttons have multiple and all the same color states.

Play around with this to see what all colors are possible and animate some graphics.

https://stackoverflow.com/q/17239565

https://github.com/dcuddeback/libusb-rs/issues/20

`libusb-rs` seems abandoned, but has a maintained fork: `https://github.com/a1ien/rusb`

Maybe also use a C# wrapper.

---

It seems as though there are two options: first make the device recognizable to the OS
so that it can select a generic driver for it. This is done using "descriptors" and there
is a general HID framework which is cross-OS and then there is WinUSB which is Windows
specific, but more capable. Perhaps the Akai launchpad's string descriptions already make
it qualify for one such generic driver. Ableton doesn't make me install a driver for it and
can use it from the get-go and I have not had to install a driver for it manually either,
so this might be the case. The other option is to develop a kernel USB driver, which is
much more involved but might not be needed since Ableton can use the launchpad without it,
so it must be possible. Or, and this is also something to check, perhaps the launchpad,
despite using USB, is actually a serial over USB kind of a device like many other are. I
don't know how to validate if this is the case or not, but if it is serial over USB, I might
want to look in ways of determining the serial port name and other serial parameters
(mainly the baud rate) and then use C# APIs for serial communication which can work straight
from user space.

---

When connected to a Windows machine, the Akai launchpad has its driver installed automatically
by the OS. This tells me that the device is HID/WinUSB compatible and thus does not need its
own driver.

When inspected in Device Manager, the device appears in a group called *Software Devices*.
It exposes two entries there: `APC MINI [0]` and `APC MINI [1]`.

It also appears in a group called *Sound, video and game controllers*. There, it has one
entry named `APC MINI`.

I have scraped the USB device properties data in [`akai.csv`](akai.csv).

The next steps are:

- [ ] Trace OP-1 USB calls to see if they rememble these ones, if so, it is generic MIDI comms
- [ ] Compae a mobile USB properties to see if they resemble these ones, if so, RS-232 maybe
