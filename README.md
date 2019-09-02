# nix-shorts

A collection of short notes about [Nix](https://nixos.org/), down to what is immediately needed for users.

The aim of this collection is to provide some instantly usable information with clear code demonstrations.

While you can read the posts in this repo in any order, you might start from how to build your first derivation, to understand how Nix derivations work: <https://github.com/justinwoo/nix-shorts/blob/master/posts/your-first-derivation.md>

## How to read articles in this repo

All articles are meant to be easy enough to approach with clear examples of terminal commands, code, or file setups. When project setups are needed, appropriate directories will be in this repo and referenced.

When applicable, usernames are replaced with `your-user` and hashes replaced with `some-hash`. E.g.

```
$ readlink ~/.nix-profile
/nix/var/nix/profiles/per-user/justin/profile
$ readlink -f ~/.nix-profile
/nix/store/mwgv8fzlr6n9kkb5nyz27fv1l66jc7nf-user-environment
```

...will be shown as

```
$ readlink ~/.nix-profile
/nix/var/nix/profiles/per-user/your-user/profile
$ readlink -f ~/.nix-profile
/nix/store/some-hash-user-environment
```

## Contributing

Small fixes and elaborations can be contributed via PR. Content suggestions can be done via issues. However, many topics that are not critically important to a new user will not be written about here.

## Installation of Nix

From nixos.org <https://nixos.org/nixos/nix-pills/install-on-your-running-system.html#idm140737316665792>:

```
To install Nix, run curl https://nixos.org/nix/install | sh as a non-root user
and follow the instructions. Alternatively, you may prefer to download the
installation script and verify its integrity using GPG signatures. Instructions
for doing so can be found here: https://nixos.org/nix/download.html.
```

For more esoteric information, readers should try looking through the [Nix Pills](https://nixos.org/nixos/nix-pills/).

## FAQ

### Why not Nix Pills?

Readers of Nix Pills should well know that the purpose of the Nix Pills are clearly stated multiple times in its contents:

> These articles are not a tutorial on using Nix. Instead, we're going to walk through the Nix system to understand the fundamentals.
