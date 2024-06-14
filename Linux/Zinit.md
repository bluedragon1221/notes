---
tags:
  - tutorial
description: In this article,we'll go through the steps of building a minimalist zsh configuration
---
Since [[Customizing ZSH|this article]], I have become a zinit convert. So here's everything I know about zinit, with a lot of examples.
## The Basics
- `zinit ice` *modifies* the next zinit call. For example, we use the `depth"1"` ice to pass `--depth 1` when zinit clones the plugin's git repo.
- `zinit light` installs a plugin. It is called `light` because it is faster than the default `load` command.

## Install zinit
You can't install any plugins without a plugin manager! . Add this snippet to your `.zshrc` file
```zsh
ZINIT_HOME="${XDG_DATA_HOME:-${HOME}/.local/share}/zinit/zinit.git"
[ ! -d $ZINIT_HOME ] && mkdir -p "$(dirname $ZINIT_HOME)"
[ ! -d $ZINIT_HOME/.git ] && git clone https://github.com/zdharma-continuum/zinit.git "$ZINIT_HOME"
source "${ZINIT_HOME}/zinit.zsh"
```

This basically sets a variable `$ZINIT_HOME`, installs zinit there if not already installed, and activates zinit. Now, if you `exit` zsh and start an new instance, you'll see our little script clone zinit into the right place and source it. Were all set!

## Example: Install some plugins
```zsh
zinit light zsh-users/zsh-autosuggestions
zinit light zsh-users/zsh-syntax-highlighting
```

## Example: Install `eza`
```zsh
zinit ice from"gh-r" lbin"!eza"
zinit light eza-community/eza
```

Here's how it works:
- `from"gh-r"` tells zinit to download from the github releases of the repo instead of the repo itself. If the repo has multiple releases for different OSes, zinit will pick the one for your system.
- `lbin"!eza"` comes from the `binary-symlink` annex, so you'll have to install that:
```zsh
zinit light zdharma-continuum/zinit-annex-binary-symlink
```
It symlinks the specified binary to a location at `$ZPFX/bin`. the `!` means to use a softlink instead of a hardlink

## Example: Install `bat`
```zsh
zinit ice \
  from"gh-r" \
  lbin"!bat*/bat -> cat" \
  atload"export BAT_THEME=base16"
zinit light sharkdp/bat
```

`lbin"!bat*/bat -> cat"` Does a lot of things. Let's break it down
1. `lbin` symlinks the binary to a location at `$ZPFX/bin`. same as last time
2. `!` specifies a softlink instead of a hardlink. same as last time
3. `bat*/bat` uses a shell glob to select the binary. We need this because the binary is in a directory that might look something like `bat-v0.24.0-x86_64-unknown-linux-gnu`. But that directory name will change depending on the version, OS, architecture, etc.
4. `-> cat` says to call the symlink `cat`, which is what we would normally be calling, not `bat`

`atload` does exactly what it soulds like: it runs that command when the plugin loads. In this case, we are exporting the `BAT_THEME` variable, to change the way bat looks.

## The `for` syntax
```bash
zinit ice from"gh-r" lbin"!eza"
zinit light eza-community/eza
```

Say we want to install more packages from gh-r. It would be quite repetitive to have the same `zinit ice from"gh-r"` multiple times. This is why zinit has the for syntax. Instead, we could write it this way:
```zsh
zinit from"gh-r" for \
	lbin"!eza" eza-community/eza \
	lbin"!bat*/bat -> cat" @sharkdp/bat \
	...
```
This way, we can still have unique options for single packages, but also apply `from"gh-r"`

The `@` is also new. Because of the way that zinit parses commands, it would actually read it the same as if `sharkdp...` was an ice. Since `sh` is actually a valid ice, zinit gets confused. To prevent this, prefix the repo name with an `@`.
## Example: Install LSP servers
```zsh
  zinit light-mode from"gh-r" for \
    lbin"!marksman* -> marksman"             artempyanykh/marksman \
    lbin"!markdown-oxide* -> markdown-oxide" Feel-ix-343/markdown-oxide \
    lbin"!bin/lua-language-server"           LuaLS/lua-language-server
```

This is an example of how you can use multiple plugins in a `for` syntax.
The other thing introduced here is the `light-mode` ice. It's the same as `zinit light`, but as an ice.

## All Ices
