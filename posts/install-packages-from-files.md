# Build and Install packages from files

While you could use `nix-env -iA` to look for packages by attribute path from nixpkgs, many times you will want to instead explicitly install packages from files.

## From a file of a single derivation expression

Remember that every Nix source file is an expression. You can build and install packages from a file that is a derivation value.

### `test.txt`

```
hello, i am test
```

### `value.nix`

```nix
let
  pkgs = import <nixpkgs> {};
in
pkgs.stdenv.mkDerivation {
  name = "test.txt";
  src = ./test.txt;
  phases = ["installPhase"];
  installPhase = ''
    mkdir -p $out/test
    ln -s $src $out/test/text.txt
  '';
}
```

### Usage

We can build this derivation and see the result as expected:

```bash
$ nix-build value.nix
/nix/store/some-hash-test.txt

$ cat result/test/text.txt
hello, i am test
```

And since our derivation outputs a directory for the root, we can install it:

```bash
$ nix-env -if value.nix
installing 'test.txt'
building '/nix/store/some-hash-user-environment.drv'...
created NNN symlinks in user environment

$ cat ~/.nix-profile/test/text.txt
hello, i am test
```

## From a file of a function with default arguments returning a derivation

This is the most common pattern that you will see for installable derivations, which simply uses "formal arguments", a form of attribute sets, for a function.

```nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.stdenv.mkDerivation {
  name = "test.txt";
  src = ./test.txt;
  phases = [ "installPhase" ];
  installPhase = ''
    ln -s $src $out
  '';
}
```

This can be built and installed in the same way, as the default args are provided for the formal arguments.

```bash
$ nix-build derivation-function.nix
/nix/store/some-hash-test.txt
```

## From a set of derivations

This is mostly useful if you want to version control packages installed in your profile.

```nix
{ pkgs ? import <nixpkgs> {} }:

{
  inherit (pkgs)
    python
    mypy;
}
```

```bash
$ nix-env -if some-pkgs.nix
installing 'mypy-0.701'
installing 'python-2.7.16'
building '/nix/store/some-hash-user-environment.drv'...
created NNN symlinks in user environment
```

## From a list of derivations

This is really the same as sets, but in many ways less convenient. If you'd like to use this, you might also find `builtins.attrValues` useful to convert any attribute sets you have to lists of the values.

```nix
{ pkgs ? import <nixpkgs> {} }:

[
  pkgs.python
  pkgs.mypy
  pkgs.git
]
```

## Links

* Demonstration sources <https://github.com/justinwoo/nix-pkgs-from-files>

* Nix manual examples <https://nixos.org/nix/manual/#examples>
