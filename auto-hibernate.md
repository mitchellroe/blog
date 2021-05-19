# Automatically Hibernate After Extended Sleep

## Semantics

From `man 5 systemd-sleep.conf`:

<dl>

<dt>suspend</dt>
<dd>
a low-power state where execution of the OS is paused, and complete power loss
might result in lost data, and which is fast to enter and exit. This
corresponds to suspend, standby, or freeze states as understood by the kernel.
</dd>

<dt>hibernate</dt>
<dd>
a low-power state where execution of the OS is paused, and complete power loss
does not result in lost data, and which might be slow to enter and exit. This
corresponds to the hibernation as understood by the kernel.
</dd>

<dt>hybrid-sleep</dt>
<dd>
a low-power state where execution of the OS is paused, which might be slow to
enter, and on complete power loss does not result in lost data but might be
slower to exit in that case. This mode is called suspend-to-both by the kernel.
</dd>

<dt>suspend-then-hibernate</dt>
<dd>
A low power state where the system is initially suspended (the state is stored
in RAM).  If not interrupted within the delay specified by
<strong>HibernateDelaySec=</strong>, the system will be woken using an RTC
alarm and hibernated (the state is then stored on disk).
</dd>

</dl>

## To modify the amount of time in suspend before the system hibernates

First, create the `/etc/systemd/sleep.conf.d` directory:

```
# mkdir -p /etc/systemd/sleep.conf.d
```

Then, in `/etc/systemd/sleep.conf.d/hibernate-timeout.conf`, place the
following, substituting `3600` for the amount of time (in seconds) before a
hibernation should occur after sleep:

```
[Sleep]
HibernateDelaySec=3600
```

## To automatically hibernate after suspending for a while

Normally, when the lid of your laptop is closed, the "suspend" action is run.
This is equivalent to running `systemctl suspend`. In this mode, the system
will remain suspended, and will not hibernate after a certain amount of time.

We'll tell the system to use the equivalent of `systemctl suspend-then-hibernate`
during certain situations (such as closing the lid).

First, create the `/etc/systemd/logind.conf.d` directory:

```
# mkdir -p /etc/systemd/logind.conf.d
```

Then, in `/etc/systemd/logind.conf.d/auto-hibernate.conf`, place the following:

```
[Login]
HandleSuspendKey=suspend-then-hibernate
HandleLidSwitch=suspend-then-hibernate
HandleLidSwitchExternalPower=suspend-then-hibernate
```

Finally, for the changes to take effect, either restart your computer or
restart the logind service.

**NOTE**: Restarting the logind service will log you out of any graphical
sessions you may be logged in to.

```
# systemctl restart systemd-logind.service
```

## Additional Notes

If you're running Ubuntu or Fedora, you may also need to enable hibernate for
this to work properly.
- [Enable Hibernate in Ubuntu](enable-hibernate-in-ubuntu.md)
- [Enable Hibernate in Fedora](enable-hibernate-in-fedora.md)
