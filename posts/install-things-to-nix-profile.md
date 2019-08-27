# Install things to Nix Profile

## What is Nix Profile?

Nix Profile is a symlink location in `~/.nix-profile` when you install Nix.

```bash
$ readlink ~/.nix-profile
/nix/var/nix/profiles/per-user/your-user/profile

$ readlink -f ~/.nix-profile
/nix/store/some-hash-user-environment
```

Depending on what you have installed, various things will be in the directory.

```
$ ls ~/.nix-profile
bin  etc  include  lib  manifest.nix  share
```

Notice that `~/.nix-profile/bin` is in your PATH (if you have installed Nix correctly). Whenever you install or remove things from your environment using `nix-env`, some other `/nix/store/some-other-hash-user-environment` will be used.

You will see that the directory structure in `~/.nix-profile` matches that of your file system root directory.

## How do I install packages?

To install packages from nixpkgs, you can use the `-A` flag to specify which attribute you want to install.

```bash
$ nix-env -iA nixpkgs.python
installing 'python-2.7.16'

$ which python
/home/your-user/.nix-profile/bin/python
```

To see what all kinds of packages you can install, you can use the Nix repl. See <https://nixos.wiki/wiki/Nix-repl>

```bash
$ nix repl '<nixpkgs>'
Welcome to Nix version 2.2.2. Type :? for help.

Loading '<nixpkgs>'...
Added 10647 variables.

nix-repl> pkgs.python
«derivation /nix/store/8qn1lnscayyw9kg7gnxygwwqgpk6257p-python-2.7.16.drv»

nix-repl> pkgs.python.pname
"python"

nix-repl> pkgs. # you can tab complete from here
```

## How do I uninstall packages?

The easiest method is to use `nix-env -e` (short for `uninstall`) and tab-complete.

```bash
$ nix-env -e python
uninstalling 'python-2.7.16'
```

## How do I find out what is installed in my environment?

To find out what packages you have installed in your environment, you can use `nix-env -q` (short for "query").

```bash
# example, may not match your results
$ nix-env -q
alacritty
autorandr-1.8.1
bash-completion-2.8
bat-0.11.0
colordiff-1.0.18
```

## Links

* Nix package manager guide quick start: <https://nixos.org/nix/manual/#chap-quick-start>
* Nix package manger guide on Profile: <https://nixos.org/nix/manual/#sec-profiles>
