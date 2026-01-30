# Compose Key

## GNOME

To set the compose key under GNOME, set the
`/org/gnome/desktop/input-sources/xkb-options` dconf parameter. This parameter
is a list of xkb-options, which can be found in the `xkeyboard-config(7)` man
page. Here are the list of options specifically for the "Position of Compose
key":

| Option                | Description                |
|-----------------------|----------------------------|
| `compose:ralt`        | Right Alt                  |
| `compose:lwin`        | Left Win                   |
| `compose:lwin-altgr`  | 3rd level of Left Win      |
| `compose:rwin`        | Right Win                  |
| `compose:rwin-altgr`  | 3rd level of Right Win     |
| `compose:menu`        | Menu                       |
| `compose:menu-altgr`  | 3rd level of Menu          |
| `compose:lctrl`       | Left Ctrl                  |
| `compose:lctrl-altgr` | 3rd level of Left Ctrl     |
| `compose:rctrl`       | Right Ctrl                 |
| `compose:rctrl-altgr` | 3rd level of Right Ctrl    |
| `compose:caps`        | Caps Lock                  |
| `compose:caps-altgr`  | 3rd level of Caps Lock     |
| `compose:102`         | The "< >" key              |
| `compose:102-altgr`   | 3rd level of the "< >" key |
| `compose:paus`        | Pause                      |
| `compose:ins`         | Insert                     |
| `compose:prsc`        | PrtSc                      |
| `compose:sclk`        | Scroll Lock                |

For example,

```sh
gsettings set org.gnome.desktop.input-sources xkb-options "['compose:lctrl-altgr']"
```
