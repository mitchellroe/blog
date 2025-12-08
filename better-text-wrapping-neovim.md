# Better Text Wrapping in Neovim

In Markdown files or plain-text files, with wrap enabled, neovim will wrap even inside of words, so you might end up with something like this:
```
This is a long line with lots and lo\
ts of text.
```

```lua
-- after/ftplugin/markdown.lua

local wrapenabled = true
function ToggleWrap()
  if wrapenabled then
    vim.opt.linebreak = false
    vim.api.nvim_del_keymap("n", "j")
    vim.api.nvim_del_keymap("n", "k")
    vim.api.nvim_del_keymap("n", "0")
    vim.api.nvim_del_keymap("n", "^")
    vim.api.nvim_del_keymap("n", "$")
    vim.api.nvim_del_keymap("v", "j")
    vim.api.nvim_del_keymap("v", "k")
    vim.api.nvim_del_keymap("v", "0")
    vim.api.nvim_del_keymap("v", "^")
    vim.api.nvim_del_keymap("v", "$")
    wrapenabled = false
  else
    vim.opt.linebreak = true
    vim.opt.wrap = true
    vim.keymap.set("n", "j", "gj", { noremap = true })
    vim.keymap.set("n", "k", "gk", { noremap = true })
    vim.keymap.set("n", "0", "g0", { noremap = true })
    vim.keymap.set("n", "^", "g^", { noremap = true })
    vim.keymap.set("n", "$", "g$", { noremap = true })
    vim.keymap.set("v", "j", "gj", { noremap = true })
    vim.keymap.set("v", "k", "gk", { noremap = true })
    vim.keymap.set("v", "0", "g0", { noremap = true })
    vim.keymap.set("v", "^", "g^", { noremap = true })
    vim.keymap.set("v", "$", "g$", { noremap = true })
    wrapenabled = true
  end
end
vim.keymap.set("n", "<leader>w", ToggleWrap, { desc = "Toggle soft wrap mode" })
```
