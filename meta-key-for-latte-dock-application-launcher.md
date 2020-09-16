# Use the Meta Key To Open the Latte Dock Application Launcher

## References

<https://github.com/psifidotos/Latte-Dock/wiki/F.A.Q.#q-can-i-use-my-super-key-to-open-the-app-launcher>

Edit your `~/.config/kwinrc` file and add the following:

```
[ModifierOnlyShortcuts]
Meta=org.kde.lattedock,/Latte,org.kde.LatteDock,activateLauncherMenu
```

Alternatively, you can run the following command:

```
kwriteconfig5 --file ~/.config/kwinrc --group ModifierOnlyShortcuts --key Meta "org.kde.lattedock,/Latte,org.kde.LatteDock,activateLauncherMenu"
```

Then, when the configuration file has been written, restart KWin:

```
qdbus org.kde.KWin /KWin reconfigure
```
