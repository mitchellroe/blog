# Stop Google Chrome Asking to be the Default Browser

Google Chrome really wants to be the default browser. So much so that, if it is
not, it will ask you to set it as the default every time it runs. This can get
rather irritating.

To turn off this behavior, open `~/.config/google-chrome/Default/Preferences`
(or `~/Library/Application Support/Google/Chrome/Default/Preferences` on
macOS), and add the following code within the `browser` section:

```
"check_default_browser":false
```

So, for example, replace
```
"browser":{
```
with
```
"browser":{"check_default_browser":false,
```

## References

https://unix.stackexchange.com/questions/110613/how-can-i-make-chrome-stop-asking-to-be-the-default-browser
