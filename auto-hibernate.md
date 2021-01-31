# Automatically Hibernate After Extended Sleep

## To modify the amount of time in suspend before the system hibernates

First, create the `/etc/systemd/sleep.conf.d` directory:

```
# mkdir -p /etc/systemd/sleep.conf.d
```

Then, in `/etc/systemd/sleep.conf.d/hibernate-timeout.conf`, place the following:

```
[Sleep]
HibernateDelaySec=3600
```

## To automatically hibernate after suspending for a while

Normally, the "suspend" action is equivalent to running `systemctl suspend`.
We'll tell the system to use the equivalent of `systemctl suspend-then-hibernate`.

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

Finally, for the changes to take effect, either restart your computer or restart the logind service.

**NOTE**: Restarting the logind service will log you out of any graphical sessions you may be logged in to.

```
# systemctl restart systemd-logind.service
```
