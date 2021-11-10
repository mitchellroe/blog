# Fingerprint Reader

To allow both password and fingerprint authentication, allow an empty password
attempt first, then prompt for the fingerprint.

Add the following to the top of `/etc/pam.d/swaylock` (or `/etc/pam.d/sudo`, or
any other file in `/etc/pam.d/`):

```
auth sufficient pam_unix.so try_first_pass likeauth nullok
auth sufficient pam_fprintd.so
```

Originally from https://wiki.archlinux.org/title/Fprint
