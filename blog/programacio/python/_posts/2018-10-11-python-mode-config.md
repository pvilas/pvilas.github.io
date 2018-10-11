---
layout: post
title: My python-mode config
author: Pere Vilas
tags: python
---

That is my [python-mode](https://github.com/python-mode/python-mode) configuration on ```~/.vimrc```

```
  1 syntax enable
  2 set tabstop=4
  3 set shiftwidth=4
  4 set expandtab
  5 set number
  6 filetype indent on
  7 set autoindent
  8 set encoding=utf-8
  9
 10 " let python_highlight_all=1
 11
 12 " configuracio per a python-mode
 13 filetype off
 14 call pathogen#infect()
 15 call pathogen#helptags()
 16 filetype plugin indent on
 17 syntax on
 18 let g:pymode_python = 'python3'
 19 let g:pep8_ignore="E501,W601"
 20 let g:pymode_options_colorcolumn = 0
```

Some useful keys:
- **K** See documentation
- **Ctrl+space** Autocomplete
- **Ctrl+w** Change window
- **Ctrl+c+ro** Organize imports
- **Ctrl+c+g** Goto definition
- **Ctrl+c+rr** Refactor
