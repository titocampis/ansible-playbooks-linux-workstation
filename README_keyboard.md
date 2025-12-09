## New keys on keyboard layout

> :warning: **WARNING:** This procedure can break your keyboard layout, be really careful

As my laptop does not have the keys '< >', I want to type them when:
- Alt Gr + G: >
- Alt Gr + L: <

:one: Check which `keycode` `G` and `L`have (any user):
```bash
xev
```

Type the key and see logs.

:two: Grep the keycode to see the key number (sudo):
```bash
sudo grep <keycode> /usr/share/X11/xkb/keycodes/evdev
```

:three: Copy the `es` layout into `es-custom`
```bash
sudo cp /usr/share/X11/xkb/symbols/es /usr/share/X11/xkb/symbols/es-custom
```

:four: Edit the `es-custom` layout and replace de `3rd and 4th columns` with the desired symbol:
```bash
sudo vim /usr/share/X11/xkb/symbols/es-custom
```
```bash
    // -------------------  Greater and less --------------------------------------------
    key <AC09>  { [ l, L, less, Less ] };
    key <AC05>  { [ g, G, greater, Greater ] };
    // -------------------  Greater and less --------------------------------------------
```

:five: Apply the new layout config and pray :pray:
```bash
sudo setxkbmap -layout es-custom
```

> :collision: **Note:** If something went wrong, roll-back
> ```
> sudo setxkbmap -layout es
> ```


