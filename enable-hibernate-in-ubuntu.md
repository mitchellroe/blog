# Enable hibernate in Ubuntu

First and foremost, you must have sufficient swap space for hibernation to
work. The exact amount varies depending upon your system, but a general rule is
for your swap to be 1.5x to 2x the capacity of your RAM.

Assuming you have sufficient swap space for hibernation, edit
`/etc/polkit-1/localauthority/50-local.d/com.ubuntu.enable-hibernate.pkla` and
add the following:

```
[Re-enable hibernate by default in upower]
Identity=unix-user:*
Action=org.freedesktop.upower.hibernate
ResultActive=yes

[Re-enable hibernate by default in logind]
Identity=unix-user:*
Action=org.freedesktop.login1.hibernate;org.freedesktop.login1.handle-hibernate-key;org.freedesktop.login1;org.freedesktop.login1.hibernate-multiple-sessions;org.freedesktop.login1.hibernate-ignore-inhibit
ResultActive=yes
```

Reboot and you should now have hibernation as an option.
