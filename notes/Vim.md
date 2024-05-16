---
tags: 
aliases: 
publish: true
---
References: [Vim Surround](https://github.com/tpope/vim-surround)

---
# Config
Where I use vim:
- vim in the terminal
	- `~/.vimrc` has configurations
	- `~/.vim/pack/vendor/start/` has all my plugins 
- intellij ideavim, which is mapped to my `.vimrc`
	- see [emulated vim plugins](https://github.com/JetBrains/ideavim#emulated-vim-plugins)
- Obsidian with vim bindings enabled
	- configured by `.obsidian.vimrc` which lives in my vault root folder

# Commands

### Vim Surround
Targets can be a vim |word|, |WORD|, |sentence|, |paragraph| or |line|.

Bracket types:
- `b` -> `)`
- `B` -> `}`
- `t` + `<tag>` -> `<p>` or `<div>`  
- `r` -> `]`
- `a` -> `>`
Commands:
- `cs"'` - "change surround" `<target>` with `<replacement>`
- `ysiw` - "you surround, insert (around) word"
	- `ysis` - around sentence
	- `ysip` - around paragraph
 - `ds)` - "delete surrounding" `<target>`

Visual Mode Commands:
- `S` - "surround" selection