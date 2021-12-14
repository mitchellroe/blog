# Battery Charge Threshold

Batteries wear out over time. One of the most wearing things on a battery, over
the long term, is being "fully charged" (e.g. above 85%) for long periods of
time. This will set your system to stop charging once it reaches a certain
percentage.

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
