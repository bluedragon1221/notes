# Configuring ZSH on Arch
This is my ideal configuration of ZSH in Linux

#### IDEAS
no framework (oh my zsh, etc)

plugins (manual)

PS1
	failed command color
	starship

## No Framework
I do not use a framework like OhMyZSH.
Instead, I manually clone the git repos and source them.
I do this for a few reasons:

### Avoiding Bloat
Frameworks like OhMyZSH include a lot of bloat by default.
I know there are other frameworks that are less bloated, but I still prefer a manuall approach.

### Speed
Because of the amount of bloat in these frameworks, they aren't very fast.
Standard times on OhMyZSH are about `0.7 sec`.
As opposed to standard ZSH's `0.25 sec`.

### Control
