# Your First Derivation

## What is a derivation?

A derivation in Nix is a definition of a build, which takes some inputs and produces an output. The inputs are almost always in a `src` attribute, and outputs are almost always some `/nix/store/some-hash-pkg-name` path. Depending on what command you run, the result of building a derivation may result in a new generation of your user environment or a result symlink.

## Basic derivation example

Start with some inputs you want to build with.

```bash
$ mkdir src

$ echo hello > src/hi.txt

$ ls src
hi.txt
```

Then prepare a `default.nix`, which will be used by default when we later run `nix-build`.

```nix
# allow our nixpkgs import to be overridden if desired
{ pkgs ? import <nixpkgs> {} }:

# let's write an actual basic derivation
# this uses the standard nixpkgs mkDerivation function
pkgs.stdenv.mkDerivation {

  # name of our derivation
  name = "basic-derivation";

  # sources that will be used for our derivation.
  src = ./src;

  # see https://nixos.org/nixpkgs/manual/#ssec-install-phase
  # $src is defined as the location of our `src` attribute above
  installPhase = ''
    # $out is an automatically generated filepath by nix,
    # but it's up to you to make it what you need. We'll create a directory at
    # that filepath, then copy our sources into it.
    mkdir $out
    cp -rv $src/* $out
  '';
}
```

Some notes about this to know:

### Nix files are expressions

Every Nix source file must be a valid expression. In this case, our `default.nix` is a an expression for a Function which takes an Attribute Set, provides a default value for `pkgs`, and then returns a derivation value by using the function `mkDerivation` inside of the `stdenv` attribute of `pkgs`.

When this file is called with `nix-build`, the function will be satisfied automatically with the default argument unless overridden.

### Derivations must have names

This is satisfied by our `name` attribute defined above.

### Derivations created with `mkDerivation` must have sources

This is satisfied by using the relative path `./src` above.

### Standard derivations made with `mkDerivation` uses the concept of "phases"

In this derivation, we only needed to define the `installPhase` to override the default from the generic builder. You can see more details about the build phases here: <https://nixos.org/nixpkgs/manual/#sec-stdenv-phases>

### Why use the nixpkgs `stdenv.mkDerivation`?

While derivation can be written from scratch using the builtin function `derivation`, the reality is that there are many standardized steps to a build that should be handled. Most derivations you work with when using Nix will be stdenv derivations or derivatives of them, so you should familiarize yourself with these before moving on.

## Building our derivation

With our derivation defined, we can build this derivation using `nix-build`.

```bash
$ nix-build # or nix-build default.nix
# some build logs
/nix/store/some-hash-basic-derivation
```

This output path is simply where the Nix Store output has been created. Notice that when you run `nix-build` again, no inputs have changed, so the derivation does not need to be built again.

```bash
$ nix-build
/nix/store/some-hash-basic-derivation
```

`nix-build` will create a symlink to the output by default in `./result`. You can inspect the symlink and the contents and see that the output is indeed just a symlink to the inputs as in our derivation `installPhase`.

```bash
$ readlink result
/nix/store/kgjcq77210jkjppc8628vcl27i6f22k8-basic-derivation

$ ls result
hi.txt

$ cat result/hi.txt
hello
```

## Links

* Phases in nixpkgs stdenv derivations: <https://nixos.org/nixpkgs/manual/#sec-stdenv-phases>
