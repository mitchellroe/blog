# Git and GNOME Keyring

Every time I need to authenticate against https in git, I needed to provide my
credentials. You can configure git to use libsecret instead, which will store
them in GNOME Keyring (or whatever credential manager you have configured).

```
git config --global credential.helper /usr/libexec/git-core/git-credential-libsecret 
```

This will store your credentials in GNOME Keyring rather than prompt you for
them every time.
