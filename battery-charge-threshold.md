# Battery Charge Threshold

Batteries wear out over time. One of the most wearing things on a battery, over
the long term, is being "fully charged" (e.g. above 85%) for long periods of
time.

This will set your system to stop charging once it reaches a certain
percentage.

## Option 1: `tlp`

From the [TLP website](https://linrunner.de/tlp/):

> TLP is a feature-rich command line utility for Linux, saving laptop battery
> power without the need to delve deeper into technical details.

To configure battery charge thresholds, take a look at `/etc/tlp.conf`,
specifically the `START_CHARGE_THRESH_BAT0` and the `STOP_CHARGE_THRESH_BAT0`
variables.

From `/etc/tlp.conf`:
```
# Battery Care -- Charge thresholds
# Charging starts when the charge level is below the START_CHARGE_THRESH value
# when the charger is connected. It stops when the STOP_CHARGE_THRESH value is
# reached.
# Required hardware: Lenovo ThinkPads and select other laptop brands are driven
# via specific plugins, the actual support status is shown by tlp-stat -b.
# For more explanations and vendor specific details refer to
#   https://linrunner.de/tlp/settings/battery.html
# Notes:
# - ThinkPads may require external kernel module(s), refer to the output of
#   tlp-stat -b
# - Vendor specific parameter value ranges are shown by tlp-stat -b
# - If your hardware supports a start *and* a stop threshold, you must
#   specify both, otherwise TLP will refuse to apply the single threshold
# - If your hardware supports only a stop threshold, set the start value to 0

# BAT0: Primary / Main / Internal battery (values in %)
# Note: also use for batteries BATC, BATT and CMB0
# Default: <none>

START_CHARGE_THRESH_BAT0=80
STOP_CHARGE_THRESH_BAT0=85
```

Once you've configured these variables, restart the `tlp` service to pick up
the changes:
```
systemctl restart tlp.service
```

And, of course, make sure the `tlp` service is enabled so it runs at startup:
```
systemctl enable tlp.service
```

## Option 2: `systemd` service

`/etc/systemd/system/battery-charge-threshold.service`:
```systemd
[Unit]
Description=Set the battery charge threshold

[Service]
Type=oneshot
ExecStart=/bin/bash -c 'echo 85 > /sys/class/power_supply/BAT0/charge_control_end_threshold'

[Install]
WantedBy=multi-user.target
```

`/etc/systemd/system/battery-charge-threshold.path`:
```systemd
[Path]
PathExists=/sys/class/power_supply/BAT0/charge_control_end_threshold
Unit=battery-charge-threshold.service

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable --now battery-charge-threshold.service
```
