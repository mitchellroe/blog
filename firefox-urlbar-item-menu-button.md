# Firefox urlbar item menu button

Firefox added a hamburger menu to the urlbar suggestion items.
When tabbing through the list, the hamburger menu button would be highlighted as well, meaning that two tab presses are required to move past the suggestion.
This can be disabled.

- Go to `about:config`.
- To make the Tab key skip the hamburger menu, change `browser.urlbar.resultMenu.keyboardAccessible` to `false`.
- To disable the hamburger menu altogether, set `browser.urlbar.resultMenu` to `false`.
