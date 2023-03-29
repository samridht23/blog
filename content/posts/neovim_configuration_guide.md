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

Our **nvim** file structure

├── app
│ ├── css
│ │ ├── **/\*.css
│ ├── favicon.ico
│ ├── images
│ ├── index.html
│ ├── js
│ │ ├── **/\*.js
│ └── partials/template
├── dist (or build)
├── node_modules
├── bower_components (if using bower)
├── test
├── Gruntfile.js/gulpfile.js
├── README.md
├── package.json
├── bower.json (if using bower)
└── .gitignore.