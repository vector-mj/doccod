---
title: Vim configurations
date: 2020-09-24
tags: ["vim"]
cover:
  image: "/images/vim.png"
  # can also paste direct link from external site
  # ex. https://i.ibb.co/K0HVPBd/paper-mod-profilemode.png
  alt: "<alt text>"
  caption: "<text>"
  relative: false # To us
---

### ~/.vimrc
```vim
runtime! ftplugin/man.vim
set number
set nowrap

filetype on
filetype plugin on
filetype indent on
syntax on

set cursorline
#set cursorcolumn
set shiftwidth=4
set tabstop=4

set ignorecase
set smartcase
set wildmenu
```