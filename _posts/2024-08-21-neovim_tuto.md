---
title: "Neovim Tutorial"
categories: [Others]
tags: [vim] # TAG names should always be lowercase
---

# Setup
Arch Linux:
```shell
sudo pacman -S neovim
# or
yay -S neovim
# And Optionally set alias
echo 'alias vim=nvim' >> .zshrc # ".zshrc" might be changed
```

# Install NvChad Config
```shell
git clone -b v2.0 https://github.com/NvChad/NvChad ~/.config/nvim --depth 1 && nvim
rm -rf ~/.local/share/nvim 
# To use NvChad, Nerd font is required
```

# Neovim Usage

### Help
`space c h` : open/close cheat sheet

`space` : suggest command

### Theme
`space t h`


### Syntax Highlight
`:TSInstall $language` # Install syntax Highlight for the language

`:TSInstallInfo` : check list of supported language for syntax Highlight


### File Tree
`Ctrl + n` : Open file tree. Select the file and **`Enter` to open the file**. Can **mark with `m`**

On file tree `a` : Create file.

On file tree `{c, p, r}` : {copy, paste, rename}


### File Naviate
`Space f f` : find file

`Space f b` : find file in already openned files


### Window Naviate
`Ctrl + {h,j,k,l}` : Navigate pannel

`:vsp` : vertial split

`:sp` : horizontal split

`Space n` : Toggle line number

`Space r n` : Toggle **Relative** line number

### Tab Buf
`{shift}tab` : change tab forward/backward

`Space x` : close tab

### Terminal
`space {h, v}` : {horizontal, vertical} window for Terminal
