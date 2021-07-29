# Higher quality Bluetooth audio input with Pipewire and mSBC

## Installing Pipewire on Fedora 33

Follow the instructions here: https://fedoraproject.org/wiki/Changes/DefaultPipeWire#How_To_Test

Run the following command to install pipewire (will remove pulseaudio):
```
sudo dnf install --allowerasing pipewire-pulseaudio
```

Reboot your machine.

`pactl info` should now list `Server Name: PulseAudio (on PipeWire 0.3.x)`.

## Enable mSBC codec support for HFP

Instructions from here:
<https://www.redpill-linpro.com/techblog/2021/05/31/better-bluetooth-headset-audio-with-msbc.html>

The default audio codec in Fedora 33 for HSP/HFP is CVSD, which is an 8 kHz
codec. mSBC is a 16 kHz codec. mSBC is disabled by default because it's not
supported on all Bluetooth input devices. To enable it, edit either
`~/.config/pipewire/media-session.d/bluez-monitor.conf` or
`/etc/pipewire/media-session.d/bluez-monitor.conf` with the following contents:

```
properties = {
  bluez5.msbc-support = true
}
```

Then restart the pipewire service with

```
systemctl --user restart pipewire.service
```

In your system settings for audio, you should now have an mSBC codec available
for the HSP/HFP protocol.
