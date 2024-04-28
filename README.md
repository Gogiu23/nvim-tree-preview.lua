# nvim-tree-preview.lua

Preview [NvimTree](https://github.com/nvim-tree/nvim-tree.lua/) files in a floating window.

https://gist.github.com/assets/21299126/2b58931d-67ca-468d-8807-51a629a81def

Depends on [plenary.nvim](https://github.com/nvim-lua/plenary.nvim).

## Installation

Lazy.nvim:

```lua
{
  'kyazdani42/nvim-tree.lua',
  dependencies = {
    'b0o/nvim-tree-preview.lua',
  },
},
```

Configuration:

```lua
local preview = require'nvim-tree-preview'

-- Default config:
preview.setup {
  -- Keymaps for the preview window (does not apply to the tree window)
  -- Keymaps can be a string (vimscript command), a function, or a table.
  -- If a table, it must contain either an 'action' or 'open' key.
  -- Valid actions are 'close' and 'toggle_focus'.
  -- Valid open modes are 'edit', 'tab', 'vertical', and 'horizontal'.
  -- To disable a default keymap, set it to false.
  -- All keymaps are set in normal mode.
  keymaps = {
    ['<Esc>'] = { action = 'close' },
    ['<Tab>'] = { action = 'toggle_focus' },
    ['<CR>'] = { open = 'edit' },
    ['<C-t>'] = { open = 'tab' },
    ['<C-v>'] = { open = 'vertical' },
    ['<C-x>'] = { open = 'horizontal' },
  },
  min_width = 10,
  min_height = 5,
  max_width = 85,
  max_height = 25,
  wrap = false, -- Whether to wrap lines in the preview window
  border = 'rounded', -- Border style for the preview window
}
```

In your nvim-tree `on_attach` function (see `:help nvim-tree.on_attach`):

```lua
vim.keymap.set('n', 'P', preview.watch, opts 'Preview (Watch)')
vim.keymap.set('n', '<Esc>', preview.unwatch, opts 'Close Preview/Unwatch')

-- Option A: Simple tab behavior: Always preview
vim.keymap.set('n', '<Tab>', preview.node_under_cursor, opts 'Preview')

-- Option B: Smart tab behavior: Only preview files, expand/collapse directories.
vim.keymap.set('n', '<Tab>', function()
  local ok, node = pcall(api.tree.get_node_under_cursor)
  if ok and node then
    if node.type == 'directory' then
      api.node.open.edit()
    else
      preview.node(node, { toggle_focus = true })
    end
  end
end, opts 'Preview')
```

If you're using [nvim-window-picker](https://github.com/s1n7ax/nvim-window-picker), it's recommended to ignore nvim-tree-preview windows:

```lua
require('nvim-tree').setup {
  actions = {
    open_file = {
      window_picker = {
        enable = true,
        picker = function()
          return require('window-picker').pick_window {
            filter_rules = {
              file_path_contains = { 'nvim-tree-preview://' },
            },
          }
        end,
      },
    },
  },
}
```


## Alternatives

- [JMarkin/nvim-tree.lua-float-preview](https://github.com/JMarkin/nvim-tree.lua-float-preview/)

## License

Copyright (C) 2024 Maddison Hellstrom

MIT License