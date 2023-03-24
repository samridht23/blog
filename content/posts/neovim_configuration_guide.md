---
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
draft = true
+++
---

This blog is about configuring **Neovim** for Development. And also I expect you to have some basic knowledge of Lua because we are going to use Lua for configuring Neovim.

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
