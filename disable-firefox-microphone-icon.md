# Disable Firefox Microphone/Webcam Widget

Whenever you're using the micrphone or the webcam in Mozilla Firefox, it
displays a little widget at the top of your display. This little widget can be
annoying. Here's how to get rid of it:

1. Enable "legacy" user styles in [about:config](about:config):
   ```
   toolkit.legacyUserProfileCustomizations.stylesheets
   ```
2. Add the following to
   `~/.mozilla/firefox/your-profile-folder/chrome/userChrome.css` (create it if
   it doesn't exist):
   ```css
   /* Add the following line at the top of the file if it doesn't already exist. */
   @namespace url("http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul");
   
   #webrtcIndicator {
       display: none;
   }
   ```
3. Restart Firefox
