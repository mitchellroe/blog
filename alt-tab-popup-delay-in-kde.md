# Alt-Tab Popup Delay in KDE

Instructions originally from https://zren.github.io/kde/#show-alt-tab-popup-as-fast-as-possible

Normally KWin will wait 90 milliseconds before trying to show the popup. This
makes quick switches faster since it doesn’t need to draw anything.

We need to set `DelayTime=0` under the group [TabBox] in the file
~/.config/kwinrc, then reload kwin.

It’s easier to use these commmands than doing it by hand.

```
kwriteconfig5 --file ~/.config/kwinrc --group TabBox --key DelayTime 0
qdbus org.kde.KWin /KWin reconfigure
```

You can also disable the highlight window effect by going to: System Settings >
Window Management > Task Switcher Tab

Uncheck: Show selected window
