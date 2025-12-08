# Kodi YouTube Add-On Configuration

You need the following components from a project in https://console.cloud.google.com to get this to work:
1. API key
2. OAuth client ID
3. Test user

How do you get them?
1. Go to https://console.cloud.google.com
2. Create a new project and "select" it as the active project
3. Enable the "YouTube Data API v3" in the project
5. Create some credentials
   1. API key: This is simply a string. Nothing fancy about it.
   2. OAuth 2.0 Client ID
      1. This one contains two components: Client ID and Client Secret. You'll need both.
   1. Select an API -> "YouTube Data API v3"
   2. What data will you be accessing? -> "Public data"

