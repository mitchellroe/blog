# Automatically Hibernate After Extended Sleep

`/etc/systemd/sleep.conf`:
```
[Sleep]
HibernateDelaySec=3600
```

`/etc/systemd/logind.conf`:
```
HandleLidSwitch=suspend-then-hibernate
```

```
# systemctl restart systemd-logind.service
```

```
# systemctl suspend-then-hibernate
```
