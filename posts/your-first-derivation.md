# Your First Derivation

## What is a derivation?

A derivation in Nix is a recipe to take some inputs and use those to create some outputs, something commonly called a "build". The inputs are usually some sources in a `src` attribute and the outputs are a path in the Nix store of the form `/nix/store/some-hash-pkg-name`. This path can either be a file (if the output is a single file) or a folder containing any number of files and folders and it will result in a `result` symlink pointing to this path or a new generation of your user environment.

## Basic derivation example

Start with some inputs you want to build with in this case a `src` folder with a text file in it.

```bash
$ mkdir src

$ echo hello > src/hi.txt

$ ls src
hi.txt
```

Then prepare a `default.nix`, which will be the default Nix file used when we run `nix-build` in a bit.

```nix
# Set nixpkgs to the default but allow it to be overridden if necessary
{ pkgs ? import <nixpkgs> {} }:

# Let's write a derivation using the standard env nixpkgs mkDerivation function
pkgs.stdenv.mkDerivation {

  # The name of our derivation
  name = "basic-derivation";

  # The sources that will be used for our derivation.
  src = ./src;

  # Override the [installPhase](https://nixos.org/nixpkgs/manual/#ssec-install-phase) 
  # which normally "creates the directory $out and calls `make install`"
  # In this case since there is nothing to `make` or compile we'll do it ourselves
  installPhase = ''
    # $out is a filepath and in this case it'll be a directory
    # It could also be just a single file
    mkdir -p $out
    
    # $src is defined as the location of the `src` attribute above
    # Everything in there is copied to the $out folder (in this case it'll be only the `hi.txt` file)
    cp -rv $src/* $out
  '';
}
```

Some notes about this to know:

### Nix files are expressions

Every Nix source file must be a single valid expression. In this case, our `default.nix` is an expression that defines function which takes an Attribute Set, provides a default value for `pkgs`, and then returns the derivation value created by the function `mkDerivation` inside of the `stdenv` attribute of `pkgs`.

When this file is called with `nix-build`, the function will be called with the default argument as set above.

### Derivations must have names

This is satisfied by our `name` attribute defined above.

### Derivations created with `mkDerivation` must have sources

This is satisfied by putting the relative path `./src` in `src` above.

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

This output path is simply where the Nix Store output (`$out`) has been created. Notice that when you run `nix-build` again, no inputs have changed, so the derivation does not need to be built again.

`nix-build` will create a symlink to the output by default in `./result`. You can inspect the symlink and its contents and see that the output is indeed just a symlink to folder we created and things we copied into it in the `installPhase` of our derivation.

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
