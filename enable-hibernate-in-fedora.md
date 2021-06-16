# Enable Hibernate in Fedora

## For Fedora ≤ 32

Resuming from hibernate is not working properly.  There is [a
bug](https://bugzilla.redhat.com/show_bug.cgi?id=1795422) open for this issue,
which contains a workaround.

```
echo 'add_dracutmodules+=" resume "' | sudo tee /etc/dracut.conf.d/99-enable-resume.conf
sudo dracut -fv
```

## For Fedora ≥ 33

Fedora 33 introduced some changes to the default partition scheme, specifically
they started using ZRAM instead of a physical swap partition.  Because of this,
if you want to use hibernation in Fedora 33 and above, you will need to
repartition your disk to include a swap partition (if you don't already have
one).
