# Pulse Audio Not Using the Default Sink for New Streams

## The problem

Sometimes, pulseaudio would assign a stream to a particular sink that a) was not the default sink and b) was not related to prior settings.
I wanted pulseaudio to assign all streams, by default, to the default sink.

## The solution

Apparently pulseaudio remembers where each stream goes, so the next time it sees a stream from that application, it will try to assign it to the sink it was previously assigned to.
I don't want this behavior.

In `/etc/pulse/default.pa`, replace
```
load-module module-stream-restore
```
with
```
load-module module-stream-restore restore_device=false
```
and reboot.

## Resources

Originally found on [ArchWiki](https://wiki.archlinux.org/index.php/PulseAudio/Troubleshooting#Fallback_device_is_not_respected) (like so many other great troubleshooting guides).
