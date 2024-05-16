---
tags:
  - tools
  - configuration
aliases: 
publish: true
---

 # Description
 [chezmoi](https://www.chezmoi.io/what-does-chezmoi-do/) tool to automate backing up my dotfiles to my [github repository](https://github.com/audryhsu/dotfiles) and manage dotfiles across different machiens.
- See [chezmoi daily commands](https://www.chezmoi.io/user-guide/command-overview/) for more information.
## Concepts
- The *destination directory* aka your home directory is the directory chezmoi manages
- A *target* is a file, directory, or symlink in the destination/home directory
- *Source directory* is where chezmoi stores working copy of state
	- *Source state* declares the desired state of your home directory.
- *Target state* is the desired state of the destination/home directory 

-- wikilink
![Drawing_2024-05-16 13.30.19.excalidraw.svg](../images/Drawing_2024-05-16%2013.30.19.excalidraw.svg.md)
%%[🖋 Edit in Excalidraw](../images/Drawing_2024-05-16%2013.30.19.svg)%%

-- markdown link
![](../images/Drawing_2024-05-16%2013.30.19.excalidraw.svg)
%%[🖋 Edit in Excalidraw](../images/Drawing_2024-05-16%2013.30.19.svg)%%

Chezmoi wants you to make changes on the *working copy* (source directory), and then apply changes to the files in your home directory, and version control the source state into a remote respository.

Workflow
1. `chezmoi init` creates a new local git repository in `~/.local/share/chezmoi` where source state is stored 
2. Add files to be tracked by chezmoi, which copies the source dotfile to the working copy dotfile
3. Preview the changes chezmoi would make
4. Apply changes
Note: `~/.config/chezmoi/chezmoi.toml` is configured with `autoCommit` and `autoPush` on, so changes to source directory are automatically pushed to the remote repository. 🤖
```bash
# edit target 
vim ~/.vimrc

# add targets to source state
# if already added, source state is replaced with `~/.bashrc` to `~/.local/share/chezmoi/dot_bashrc`.
chezmoi add .vimrc   

# see state difference
chezmoi diff

# after editing a file, preview changes in verbose and dryrun mode to mirrored copy 
chezmoi -v -n apply

# push changes to git 
chezmoi cd && git add . && git commit -m
git push
```

![Pasted image 20240516131409.png](../images/Pasted%20image%2020240516131409.png)