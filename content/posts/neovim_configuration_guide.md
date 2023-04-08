+++
author = "Samridh"
title = "Neovim Configuration"
date = "2023-03-24"
description = "How to configure Neovim"
tags = [
"neovim",
"lua",
"vim",
"configuration",
"text editor",
]
draft = false
+++

This blog is about configuring **Neovim** for Development. And also I expect you to have some basic knowledge of Lua because we are going to use Lua for configuring Neovim. We are using lua because it allow for more powerfull and flexible configuration options than traditional init.vim which uses vimscript for configuring your editor.

My Neovim configuration repo -> https://github.com/samridht23/nvim

---

## Installing Neovim

**Arch Linux**

```bash
sudo pacman -S neovim
```

> You can also use AUR package to install Neovim latest development version.

**Debian**

```bash
sudo apt-get install neovim
```

**CentOS 8 / RHEL 8**

```bash
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
yum install -y neovim python3-neovim
```

**Fedora**

```bash
sudo dnf install -y neovim python3-neovim
```

> And you also require **xclip** (X11) or **wl-clipboard** (Wayland) to make the system clipboard work with Neovim

---

## Configuring Neovim

In Linux Neovim configuration start at `~/.config/nvim/init.lua`

First create nvim file in .config file

```bash
mkdir ~/.config/nvim
```

We will use **Packer** to manage plugins for neovim.

**Unix and Linux Installation**

```bash
git clone --depth 1 https://github.com/wbthomason/packer.nvim\
 ~/.local/share/nvim/site/pack/packer/start/packer.nvim
```

### Starting Point

`init.lua` is a Lua script file that is used as a configuration file for Neovim and also it is the staring point of our configuration.

Our **neovim** configuration file structure

```
|-- lua
     |--core
        |--init.lua
     |--plugins
        |--init.lua
        |--configs
                |--git-signs.lua
                |--kanawaga.lua
                |--lualine.lua
                |--nvimcmp.lua
                |--nvimlspconfig.lua
                |--telescope.lua
                |--treesitter.lua
|-- .gitignore
|-- init.lua
```

Lua offers a high-level function to load and run libraries called `require` and when you run require('core'), Lua look for file name "core.lua" in the current directory or in any of the directories specified in the package.path variable. If it finds the file, it loads and executes it. If "core.lua" is not found, Lua looks for a directory named "core" instead. If it finds the directory, it looks for an "init.lua" file inside that directory and loads and executes it.

First we will configure main settings provided by neovim

- Create a lua folder in root directory and then make a core directory inside it which will contain our main settings

```
mkdir lua
cd lua
mkdir core
cd core
touch init.lua
```

> lua / core / init.lua

```lua
-- create a local variable 'o' that references
-- the 'opt' object provided by vim/neovim lua API.
-- The 'opt' object provides a way to set vim/neovim options using lua syntax.
local o = vim.opt

-- create a local variable 'g' that references
-- the global vim/neovim lua API object. It provides
-- a way to get global variables in vim/neovim using lua syntax.
local g = vim.g

-- Leader Key Map
-- set the leader key to 'space key'
g.mapleader = " "

-- vim/neovim will wait for 400 milliseconds
-- for a complete key code sequence to be received
-- before assuming that the user has typed a partial sequence
o.timeoutlen = 400

-- the screen will be updated every 200 milliseconds during buffer-modifying operations
o.updatetime = 200

-- number of lines to keep above and below the cursor
-- help to keep cursor in the center of screen
o.scrolloff = 10

-- display number line
o.number = true
o.numberwidth = 2
o.ruler = false

-- enable relative number line
o.relativenumber = true

-- highlight the whole line in which cursor is present
o.cursorline = true

-- display sign column, used to display types of
-- markers,breakpoint in debugging, error/warning
-- indicators and git diff markers
o.signcolumn = "yes"


-- Indenting

-- "TAB" key will insert spaces instead of a tab character
o.expandtab = true
-- For C-like languages neovim will uses the "C-indentin" algorithm for auto-indentation
o.cindent = true
-- When you start a new line, neovim will automatically indent the line based
-- on the indentation level of the previous line.
-- To adjust the indentation of an existing line you
-- can use command `Ctrl + >` or `Ctrl + <'
o.autoindent = true

-- effectively disables automatic text wrapping
-- o.textwidth = 0

-- set tab to 4 spaces
o.tabstop = 4
-- when autoindent is enabled neovim will ues 4 space for each level of indentation
o.softtabstop = 4

-- when we command `Ctrl + >` or `Ctrl + <` to manually indent
-- neovim will use 4 spaces for each level of indentation
o.shiftwidth = 4

-- non-printable character such as spaces, tabs and line endings will be displayed using special symbols.
o.list = true

-- trail:·: The · symbol is used to represent trailing whitespace.
-- nbsp:◇: The ◇ symbol is used to represent non-breaking spaces.
-- tab:→: The → symbol is used to represent tab characters.
-- extends:▸: The ▸ symbol is used to represent lines that are extended due to a line break in a wrapped line.
-- precedes:◂: The ◂ symbol is used to represent lines that precede an extended line.

-- set apperance of non-printable characters
o.listchars = "trail:·,nbsp:◇,tab:→ ,extends:▸,precedes:◂"

-- enable to use system clipboard from neovim (xclip for X11 and wl-clipboard for Wayland)
o.clipboard = "unnamedplus"

-- treat uppercase and lowercase as equivalent when performing serches and substitutions
o.ignorecase = true

-- neovim will perform case-insensitive earches if the search pattern only lowercase and vice-versa for uppercase
o.smartcase = true

-- neovim will not create backup copies of files before saving changes to them.
o.backup = false

-- neovim will not create a backup copy of the original file before overwriting it with changes.
o.writebackup = false

-- allow recover from previous versions of the file even after closing vim
o.undofile = true

-- disables swap files to store changes to the edited file in case of a crash or other interruption.
o.swapfile = false

-- remember 100 items in command line history
o.history = 100

-- Better buffer splitting
o.splitright = true
o.splitbelow = true

-- enables mouse cursor in all modes for scrolling, resizing window, selecting text and clicking on menu items
o.mouse = "a"

-- neovim can use more than 256 colors available in standard 8-bit color palette.
o.termguicolors = true

```

Now we will create plugin folder inside lua folder for our plugin configurations.We are also going to use **Packer** as our plugin manager.

- Inside lua folder

```
mkdir plugins
cd plugins
```

When we require a module in lua `require(plugins)` it will first search for init.lua folder inside provided directory which in this case is plugins folder so it will first search for init.lua file inside the plugins folder and execute it and from there on we add and configure the plugins.

- Inside plugins folder

```bash
touch init.lua # We will add our plugins in this file
```

### Before we install Packer Plugin manager I am going to explain about **VIM EVENT MODEL**.

Vim's editing functions behave as if they are event-driven.Whenever you start a Vim session, open a file, edit a buffer, change your editing mode, switch windows, or interact with the surrounding filesystem, you are effectively queuing an event that Vim immediately receives and handles. This is very important because we can use these event to trigger plugins because if we load all plugins at startup time it will effect Neovim performance. So we will only trigger plugins which are necessary for that event only. Like Code completion plugin (nvim-cmp) will only be triggered when we enter insert mode which is **InsertEnter** event.

> Here Buffer refer to data that is in the memory and not in disk.

#### Some vim events

- BufWinEnter (create a default window)
- BufEnter (create a default buffer)
- VimEnter (start the Vim session):edit example.txt
- BufNew (create a new buffer to contain demo.txt)
- BufAdd (add that new buffer to the session's buffer list)
- BufLeave (exit the default buffer)
- BufWinLeave (exit the default window)
- BufUnload (remove the default buffer from the buffer list)
- BufDelete (deallocate the default buffer)
- BufReadCmd (read the contexts of demo.txt into the new buffer)
- BufEnter (activate the new buffer)
- BufWinEnter (activate the new buffer's window)i
- InsertEnter (swap into Insert mode)
- CursorMovedI (insert a character)
- CursorMovedI (insert a character)
- CursorMovedI (insert a character)
- CursorMovedI (insert a character)
- CursorMovedI (insert a character)<ESC>
- InsertLeave (swap back to Normal mode):wq
- BufWriteCmd (save the buffer contents back to disk)
- BufWinLeave (exit the buffer's window)
- BufUnload (remove the buffer from the buffer list)
- VimLeavePre (get ready to quit Vim)
- VimLeave (quit Vim)

To install Packer plugin manager

> Unix,Linux installation

```bash
git clone --depth 1 https://github.com/wbthomason/packer.nvim\
 ~/.local/share/nvim/site/pack/packer/start/packer.nvim
```

> Windows Powershell Installation

```bash
git clone https://github.com/wbthomason/packer.nvim "$env:LOCALAPPDATA\nvim-data\site\pack\packer\start\packer.nvim"
```

Packer Commands

| Command            | Description                                                                  |
| ------------------ | ---------------------------------------------------------------------------- |
| ` :PackerComplete` | Fires after install, update, clean, and sync asynchronous operations finish. |
| ` :PackerCompile`  | Regenerate compiled loader file                                              |
| ` :PackerClean`    | Remove any disabled and unused plugins                                       |
| ` :PackerInstall`  | Clean, then install missing plugins                                          |
| ` :PackerUpdate`   | Clean, then update and then install plugins                                  |
| ` :PackerSync`     | Perform `PackerUpdate` and then `PackerCompile`                              |

You can use `:PackerSync` after adding a new plugin to check for errors as it will show error if something is wrong in the
configuration. Under the hood `:PackerSync` install and updates all the new and old packages and execute `:PackerCompile` to compile
the plugins which will create a plugin folder in the root of neovim directory.

> lua / plugins / init.lua

```lua
-- This lua function will automatically install and setup packer.nvim on any machine
-- you clone your configurations to and ensure packer installation.
local ensure_packer = function()
    local fn = vim.fn
    local install_path = fn.stdpath('data') .. '/site/pack/packer/start/packer.nvim'
    if fn.empty(fn.glob(install_path)) > 0 then
        fn.system({ 'git', 'clone', '--depth', '1', 'https://github.com/wbthomason/packer.nvim', install_path })
        vim.cmd [[packadd packer.nvim]]
        return true
    end
    return false
end

local packer_bootstrap = ensure_packer()

return require('packer').startup({
    function(use)
        -- Packer will manage itself
        use { "wbthomason/packer.nvim" }
        use { 'lewis6991/impatient.nvim' }
        use { 'nvim-tree/nvim-web-devicons' }
        -- Theme
        -- use {
        --     'nanotech/jellybeans.vim',
        --     as = "jellybeans",
        --     event = "VimEnter",
        --     config = function()
        --         vim.cmd('colorscheme jellybeans')
        --     end,
        -- }
        use {
            "rebelot/kanagawa.nvim",
            event = "VimEnter",
            config = function()
                require("plugins.configs.kanagawa")
                vim.cmd('colorscheme kanagawa')
            end,
        }
        -- Provide Git signs like add, change, delete etc in the sign line before numberline.
        use {
            "lewis6991/gitsigns.nvim",
            config = function()
                require('gitsigns').setup(require('plugins.configs.git-signs'))
            end,
        }
        -- Treesitter -> syntax highlighting
        use {
            'nvim-treesitter/nvim-treesitter',
            event = "VimEnter",
            run = ":TSUpdate",
            config = [[require('plugins.configs.treesitter')]],
        }
        -- Auto Pair
        use {
            "windwp/nvim-autopairs",
            config = function()
                require("nvim-autopairs").setup {}
            end,
        }
        -- Plenary -> provide lua functions and Telescope dependency
        use { "nvim-lua/plenary.nvim", module = "plenary" }
        -- Telescope
        -- dependency "ripgrep"
        -- sudo pacman -S ripgrep
        use({
            {
                "nvim-telescope/telescope.nvim",
                init = function()
                    require("plenary")
                end,
                config = function()
                    require("plugins.configs.telescope")
                end,
            },
            {
                "nvim-telescope/telescope-fzf-native.nvim",
                after = "telescope.nvim",
                run = "make",
                config = function()
                    require("telescope").load_extension("fzf")
                end,
            },
            {
                "nvim-telescope/telescope-symbols.nvim",
                after = "telescope.nvim"
            },
        })
        -- Status Line
        use {
            'nvim-lualine/lualine.nvim',
            requires = { 'nvim-tree/nvim-web-devicons', opt = true },
            config = [[require('plugins.configs.lualine')]]
        }
        -- LSP (Language Server Protocol)
        use {
            "neovim/nvim-lspconfig",
            config = function()
                require("plugins.configs.nvimlspconfig")
            end,
        }
        -- Snippet engine
        use({ "L3MON4D3/LuaSnip" })
        -- Code competion
        use({
            { "hrsh7th/cmp-nvim-lsp" },
            { "hrsh7th/cmp-path" },
            { "hrsh7th/cmp-buffer" },
            { "hrsh7th/cmp-cmdline" }
        })
        use {
            "hrsh7th/nvim-cmp",
            config = [[require('plugins.configs.nvimcmp')]]
        }
    end,
    -- by default packer floating window will show doubled borders.
    -- window appearance configuration
    config = {
        display = {
            open_fn = function()
                return require("packer.util").float({ border = "single" })
            end,
        },
    },
})
```

We will now configure some plugins to our requirement. Most of the plugins comes with default configuration. You can checkout these default configurations by refering to their documentation.

For this we will create a configs folder in which will have our plugins configuration.

```bash
mkdir configs
cd configs
```

The first plugin we are going to configure is git-signs

Here we are create a opts table and returing all the options. So we can call these options inside the plugins setup function.

```lua
config = function()
    require('gitsigns').setup(require('plugins.configs.git-signs'))
end
```

> lua / plugins / configs / git-signs.lua

```lua
local opts = {
    signs                        = {
        add          = { text = '+' },
        change       = { text = '++' },
        delete       = { text = '_' },
        topdelete    = { text = '‾' },
        changedelete = { text = '~' },
        untracked    = { text = '┆' },
    },
    signcolumn                   = true,  -- Toggle with `:Gitsigns toggle_signs`
    numhl                        = false, -- Toggle with `:Gitsigns toggle_numhl`
    linehl                       = false, -- Toggle with `:Gitsigns toggle_linehl`
    word_diff                    = false, -- Toggle with `:Gitsigns toggle_word_diff`
    watch_gitdir                 = {
        interval = 1000,
        follow_files = true
    },
    attach_to_untracked          = true,
    current_line_blame           = false, -- Toggle with `:Gitsigns toggle_current_line_blame`
    current_line_blame_opts      = {
        virt_text = true,
        virt_text_pos = 'eol', -- 'eol' | 'overlay' | 'right_align'
        delay = 1000,
        ignore_whitespace = false,
    },
    current_line_blame_formatter = '<author>, <author_time:%Y-%m-%d> - <summary>',
    sign_priority                = 6,
    update_debounce              = 100,
    status_formatter             = nil,   -- Use default
    max_file_length              = 40000, -- Disable if file is longer than this (in lines)
    preview_config               = {
        -- Options passed to nvim_open_win
        border = 'single',
        style = 'minimal',
        relative = 'cursor',
        row = 0,
        col = 1
    },
}
return opts
```

Theme Plugin Configuration

> lua / plugins / configs / kanagawa.lua

We are doing the same thing that we did for git-signs but in this we are directly setting up the setup code inside the config folder.
And require directly from config like this `config = [[require('plugins.configs.kanagawa.lua)]]`. We can configure it in both way we can
directly put the setup inside the configure folder and require it directly to the config or we can do setup inside the main plugin
folder and then pass the setup options as parameter to setup by requiring the configuration file.

```lua
require('kanagawa').setup({
    compile = false,  -- enable compiling the colorscheme
    undercurl = true, -- enable undercurls
    commentStyle = { italic = true },
    functionStyle = {},
    keywordStyle = { italic = true },
    statementStyle = { bold = true },
    typeStyle = {},
    transparent = false,   -- do not set background color
    dimInactive = false,   -- dim inactive window `:h hl-NormalNC`
    terminalColors = true, -- define vim.g.terminal_color_{0,17}
    colors = {
        -- add/modify theme and palette colors
        palette = {},
        theme = { wave = {}, lotus = {}, dragon = {}, all = {} },
    },
    overrides = function(colors) -- add/modify highlights
        return {}
    end,
    -- themes available
    -- wave -> dark
    -- dragon -> dark
    -- lotus -> light
    theme = "dragon", -- Load "wave" theme when 'background' option is not set
    background = {
        -- map the value of 'background' option to a theme
        dark = "dragon", -- try "dragon" !
        light = "dragon"
    },
})
```

LuaLine Plugin Configuration

Neovim has inbuilt status line but it not that configurable so we are going to use lua line which is highly customizable
You can see all the customization in plugins github repo.

LuaLine has sections to separate the values.Below is the LuaLine sections layout

```
+-------------------------------------------------+
| A | B | C                             X | Y | Z |
+-------------------------------------------------+
```

> lua / plugins / configs / lualine.lua

```lua
require("lualine").setup({
    options = {
        component_separators = { left = '', right = '' },
        section_separators = { left = '', right = '' },
        icons_enabled = true,
        globalstatus = true,
        always_divide_middle = true,
        theme = 'iceberg_dark',
        refresh = {
            statusline = 1000,
            tabline = 1000,
            winbar = 1000,
        }
    },
    sections = {
        lualine_a = { { "mode", color = { gui = "bold" } } },
        lualine_b = { { "branch" }, { "diff", colored = false } },
        lualine_c = { { "filename", file_status = true }, { "diagnostics" } },
        lualine_x = { "filetype", "encoding", "fileformat" },
        lualine_y = { "progress" },
        lualine_z = { { "location", color = { gui = "bold" } } }
    },
    tabline = {
        lualine_a = { { "buffers", buffers_color = { active = "lualine_b_normal" } } },
        lualine_z = { { "tabs", tabs_color = { active = "lualine_b_normal" } } }
    },
    extensions = { "quickfix" }
})

```

nvim-cmp configuration

This plugin provide code completion but this plugin require additional plugins to fully enable it functionality
like we require a snipet engine to provide snippets for the code completion and in this neovim configuration I have used
LuaSnip as my Snippet engine, you can use other snippet engine like **utilsnip**,**vsnip** or **snippy**.

List of plugins that are optionals but can provide more functionality

- cmp-nvim-lsp -> this plugin provide cmp with snippets and completion from your lsp if your lsp supports your current buffer.
- cmp-path -> this plugin provide cmp with snippets and completion of your project paths.
- cmp-buffer -> provide snippets from your current buffer.
- cmp-cmdline -> provide cmp completion for your neovim command line.

> lua / plugins / configs / nvim-cmp.lua

```lua
local cmp = require('cmp')

local function border(hl_name)
    return {
        { "╭", hl_name }, { "─", hl_name }, { "╮", hl_name }, { "│", hl_name },
        { "╯", hl_name }, { "─", hl_name }, { "╰", hl_name }, { "│", hl_name }
    }
end

cmp.setup({
    snippet = {
        -- REQUIRED - you must specify a snippet engine
        expand = function(args)
            -- vim.fn["vsnip#anonymous"](args.body) -- For `vsnip` users.
            require('luasnip').lsp_expand(args.body) -- For `luasnip` users.
            -- require('snippy').expand_snippet(args.body) -- For `snippy` users.
            -- vim.fn["UltiSnips#Anon"](args.body) -- For `ultisnips` users.
        end,
    },
    window = {
        -- completion = cmp.config.window.bordered(),
        -- documentation = cmp.config.window.bordered(),
        -- give border to completion popup
        completion = {
            border = border "CmpBorder",
            winhighlight = "Normal:CmpPmenu,CursorLine:PmenuSel,Search:None"
        },
        -- give border to documentation
        documentation = { border = border "CmpDocBorder" }
    },
    mapping = cmp.mapping.preset.insert({
        -- ["<C-p>"] = cmp.mapping.select_prev_item(), -- move to previous completion
        -- ["<C-n>"] = cmp.mapping.select_next_item(), -- move to next completion
        ['<C-b>'] = cmp.mapping.scroll_docs(-4),
        ['<C-f>'] = cmp.mapping.scroll_docs(4),
        ['<C-Space>'] = cmp.mapping.complete(),
        ['<C-e>'] = cmp.mapping.abort(),
        ['<CR>'] = cmp.mapping.confirm({ select = true }), -- Accept currently selected item. Set `select` to `false` to only confirm explicitly selected items.
        -- Move over Snipets
        ["<Tab>"] = cmp.mapping(
            function(fallback) -- use to map over completion
                if cmp.visible() then
                    cmp.select_next_item()
                elseif require("luasnip").expand_or_jumpable() then
                    vim.fn.feedkeys(vim.api.nvim_replace_termcodes(
                        "<Plug>luasnip-expand-or-jump", true,
                        true, true), "")
                else
                    fallback()
                end
            end, { "i", "s" }),
        -- Move over Snipets reverse
        ["<S-Tab>"] = cmp.mapping(
            function(fallback) -- super tab for jump to snippet locations
                if cmp.visible() then
                    cmp.select_prev_item()
                elseif require("luasnip").jumpable(-1) then
                    vim.fn.feedkeys(vim.api.nvim_replace_termcodes(
                        "<Plug>luasnip-jump-prev", true, true,
                        true), "")
                else
                    fallback()
                end
            end, { "i", "s" }),
    }),
    sources = cmp.config.sources({
        -- provide snippet from LSP
        { name = 'nvim_lsp' },
        -- { name = 'vsnip' }, -- For vsnip users.
        { name = 'luasnip' }, -- For luasnip users.
        -- { name = 'ultisnips' }, -- For ultisnips users.
        -- { name = 'snippy' }, -- For snippy users.
    }, {
        -- provide snippet from buffer
        { name = 'buffer' },
        -- provide file path to luasnip
        { name = 'path' },
        -- provide neovim function for lua development
        { name = "nvim_lua" },
    })
})

-- Set configuration for specific filetype.
cmp.setup.filetype('gitcommit', {
    sources = cmp.config.sources({
        { name = 'cmp_git' }, -- You can specify the `cmp_git` source if you were installed it.
    }, {
        { name = 'buffer' },
    })
})

-- Use buffer source for `/` and `?` (if you enabled `native_menu`, this won't work anymore).
cmp.setup.cmdline({ '/', '?' }, {
    mapping = cmp.mapping.preset.cmdline(),
    sources = {
        { name = 'buffer' }
    }
})

-- Use cmdline & path source for ':' (if you enabled `native_menu`, this won't work anymore).
cmp.setup.cmdline(':', {
    mapping = cmp.mapping.preset.cmdline(),
    sources = cmp.config.sources({
        { name = 'path' }
    }, {
        { name = 'cmdline' }
    })
})

-- Set up lspconfig.
local capabilities = require('cmp_nvim_lsp').default_capabilities()
-- Replace <YOUR_LSP_SERVER> with each lsp server you've enabled.
require('lspconfig')['<YOUR_LSP_SERVER>'].setup {
    capabilities = capabilities
}

```

LSP (Language Server Protocol) Configuration

Language Server Protocol (LSP) is a protocol that allows editors or
IDEs (Integrated Development Environments) to communicate with language servers,
which are separate processes that provide language-specific intelligence, such as
syntax highlighting, code completion, and error checking, code formatting for various programming languages.

You can install Language server for required languages from your system package manager if provided by your system package manager
or you can install through npm vice versa.

To install language server for **Typescript**.

```bash
 npm install -g typescript typescript-language-server
```

To install **clangd** which is a LSP for C and C like languages (C++) you have to
install it's binary via your system package manager

For Arch Linux

```bash
 sudo pacman -S clang
```

For other languages you can refer this to this README file which contain all supported LSP

- [Language Server List](https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md)

> lua / plugins / configs / telescope.lua

```lua
-- Lsp server list -> https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md
-- Setup language servers.
local lspconfig = require('lspconfig')
-- sudo pacman -S pyright
lspconfig.pyright.setup {}
-- npm install -g typescript typescript-language-server
lspconfig.tsserver.setup {}
-- npm i -g svelte-language-server
lspconfig.svelte.setup {}
-- sudo pacman -S clang
lspconfig.clangd.setup {}
-- npm i -g emmet-ls
lspconfig.emmet_ls.setup {}
-- sudo pacman -S gopls
lspconfig.gopls.setup {}
-- sudo pacman -S lua-language-server
lspconfig.lua_ls.setup {}


-- Global mappings.
-- See `:help vim.diagnostic.*` for documentation on any of the below functions
vim.keymap.set('n', '<space>e', vim.diagnostic.open_float)
vim.keymap.set('n', '[d', vim.diagnostic.goto_prev)
vim.keymap.set('n', ']d', vim.diagnostic.goto_next)
vim.keymap.set('n', '<space>q', vim.diagnostic.setloclist)

-- Use LspAttach autocommand to only map the following keys
-- after the language server attaches to the current buffer
vim.api.nvim_create_autocmd('LspAttach', {
    group = vim.api.nvim_create_augroup('UserLspConfig', {}),
    callback = function(ev)
        -- Enable completion triggered by <c-x><c-o>
        vim.bo[ev.buf].omnifunc = 'v:lua.vim.lsp.omnifunc'

        -- Buffer local mappings.
        -- See `:help vim.lsp.*` for documentation on any of the below functions
        local opts = { buffer = ev.buf }
        vim.keymap.set('n', 'gD', vim.lsp.buf.declaration, opts)
        vim.keymap.set('n', 'gd', vim.lsp.buf.definition, opts)
        vim.keymap.set('n', 'K', vim.lsp.buf.hover, opts)
        vim.keymap.set('n', 'gi', vim.lsp.buf.implementation, opts)
        vim.keymap.set('n', '<C-k>', vim.lsp.buf.signature_help, opts)
        vim.keymap.set('n', '<space>wa', vim.lsp.buf.add_workspace_folder, opts)
        vim.keymap.set('n', '<space>wr', vim.lsp.buf.remove_workspace_folder, opts)
        vim.keymap.set('n', '<space>wl', function()
            print(vim.inspect(vim.lsp.buf.list_workspace_folders()))
        end, opts)
        vim.keymap.set('n', '<space>D', vim.lsp.buf.type_definition, opts)
        vim.keymap.set('n', '<space>rn', vim.lsp.buf.rename, opts)
        vim.keymap.set('n', '<space>ca', vim.lsp.buf.code_action, opts)
        vim.keymap.set('n', 'gr', vim.lsp.buf.references, opts)
        vim.keymap.set('n', '<space>f', function()
            vim.lsp.buf.format { async = true }
        end, opts)
    end,
})
```

Telescope Congfiguration

Telescope.nvim is a highly extendable fuzzy finder over lists. Built on the latest awesome features from neovim core. Telescope is centered around modularity, allowing for easy customization.

Telescope also has some optional dependency plugin which will make it more powerfull.

- **telescope-fzf-native.nvim** -> Fuzzy sorter for telescope
- **telescope-symbols.nvim** -> provide its users with the ability of picking symbols and insert them at point.

> lua / plugins / configs / nvim-cmp.lua

```lua
local actions = require("telescope.actions")
local A = vim.api
require("telescope").setup({
    defaults = {
        prompt_prefix = " ❯ ",
        initial_mode = "insert",
        sorting_strategy = "descending",
        layout_config = { prompt_position = "bottom" },
        file_ignore_patterns = {
            ".git/", "node_modules", ".cache", "%.o", "%.a", "%.out", "%.class",
            "%.pdf", "%.mkv", "%.mp4", "%.zip"
        },
        mappings = {
            i = {
                ["<ESC>"] = actions.close,
                ["<C-j>"] = actions.move_selection_next,
                ["<C-k>"] = actions.move_selection_previous,
                ["<TAB>"] = actions.toggle_selection +
                    actions.move_selection_next,
                ["<C-s>"] = actions.send_selected_to_qflist,
                ["<C-q>"] = actions.send_to_qflist
            }
        }
    },
    extensions = {
        fzf = {
            fuzzy = true,
            override_generic_sorter = true, -- override the generic sorter
            override_file_sorter = true,    -- override the file sorter
            case_mode = "smart_case"        -- "smart_case" | "ignore_case" | "respect_case"
        }
    }
})

_G.Telescope = setmetatable({}, {
    __index = function(_, k)
        if vim.bo.filetype == "NvimTree" then
            A.nvim_cmd({ cmd = "wincmd", args = { "l" } }, {})
        end
        return require("telescope.builtin")[k]
    end
})

-- fuzzy finder keymaps
vim.keymap.set("n", "<C-P>",
    "<CMD>lua Telescope.find_files({ hidden = true })<CR>")
vim.keymap.set("n", "<leader>H", "<CMD>lua Telescope.help_tags()<CR>")
vim.keymap.set("n", "'b", "<CMD>lua Telescope.buffers()<CR>")
vim.keymap.set("n", "<C-l>", "<CMD>lua Telescope.live_grep()<CR>")
vim.keymap.set("n", "'c", "<CMD>lua Telescope.git_status()<CR>")
vim.keymap.set("n", "<leader>jj", "<CMD>E<CR>")
```

Treesitter Configuration

This plugin provide better syntax highlighting.

> lua / plugins / configs / telescope.lua

```lua
require 'nvim-treesitter.configs'.setup {
    ensure_installed = { "c", "lua", "vim", "vimdoc", "svelte", "typescript", "javascript", "go", "python", "bash" },
    sync_install = true,
    auto_install = true,
    highlight = {
        enable = true,
        additional_vim_regex_highlighting = false,
    },
}
```

## Conclusion

From setting up keybindings and mappings to installing
plugins and leveraging features such as autocompletion and syntax highlighting,
Neovim offers a wide range of options for customization. By tailoring your Neovim configuration
to your specific requirements, you can boost your productivity, streamline your workflow,
and make your coding sessions more enjoyable.

In conclusion, Neovim is a powerful and highly customizable text editor
that can be tailored to suit your individual needs and preferences. With its rich ecosystem of
plugins and configuration options, Neovim empowers you to create a personalized text editing experience that enhances
your coding productivity and makes your workflow more efficient and enjoyable.
