# Working with Nix Shell

Nix shells are quite useful for a number of different reasons, but the main reason is that it can provide you programs you want at specific versions.

## Shells with `-p`

Using `nix-shell` with `-p` can get you quickly set up with a shell with packages from NixPkgs installed.

```bash
# from your top level
$ nix-shell -p hello

# nix shell with hello installed
$ which hello
/nix/store/some-hash-hello-2.10/bin/hello
$ hello
Hello, world!
$ exit
exit

# back to your top level shell
```

### Expressions as arguments

But instead of only top level attributes, you can also put entire expressions in the `-p` argument. For example:

```bash
$ nix-shell \
    -p "let pkgs = import <nixpkgs> {}; in [ pkgs.hello pkgs.ripgrep pkgs.watchexec ]" \
    --run "which hello; which rg; which watchexec;"
/nix/store/nb3j6lj31kibiyp8jc4xdxf788nj600z-hello-2.12.1/bin/hello
/nix/store/xp39mgfiz25567l6kbl4z1v24f8b6fhv-ripgrep-14.1.0/bin/rg
/nix/store/ni614bq3zi6c6n0wmwwrnxxnrniv7cqm-watchexec-2.0.0/bin/watchexec
```

## NixPkgs mkShell

To work with Nix shells in some organized manner, you probably will want to use a source-controlled file defining a derivation. To make this concise and explicit, you can use `mkShell` from NixPkgs.

Note that `nix-shell` will consume certain files in the current directory by default. In our example, we will create a `shell.nix` file. If this file does not exist, then a `default.nix` file will be used.

```nix
# shell.nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {
  buildInputs = [ pkgs.hello ];
}
```

And we can see this used like so:

```console
$ nix-shell --run 'which hello; hello' # implicitly does `nix-shell shell.nix --run ...`
/nix/store/some-hash-hello-2.10/bin/hello
Hello, world!
```

## Links

* Nix manual nix-shell: <https://nixos.org/nix/manual/#sec-nix-shell>
* NixPkgs manual mkShell: <https://nixos.org/nixpkgs/manual/#sec-pkgs-mkShell>
