# dapp2nix

Manage [Dappsys](http://dapp.tools/dappsys/) dependencies with Nix.

## Install

To make the `dapp2nix` CLI available in your user environment run:

```sh
nix-env -i -f https://github.com/icetan/dapp2nix/tarball/master
```

## Usage

To print the CLI usage message run `dapp2nix help`.

### Getting started

`dapp2nix init` will create an empty lock file (`.dapp.json`) and a nix
expression (`dapp2.nix`) which you can import into your derivation:

```nix
{ srcRoot ? null }:
let
  dapp-pkgs = import (fetchGit {
    url = https://github.com/dapphub/dapptools;
    rev = "a32228048a23ea8f81fdb0e8acb98b914c30144d";
  }) {};

  inherit (dapp-pkgs.callPackage ./dapp2.nix { inherit srcRoot; }) this;
in this
```

Now start adding dependencies with `dapp2nix add <git repo url>`.

List your installed dependencies with `dapp2nix list`.

### Smart contract development workflow

If you need to edit a dependency and don't want to push it to a central GIT repo
before you can test out the changes locally, you can give the `srcRoot`
argument.

First clone the dependencies to a local directory (e.g. `lib`):

```sh
dapp2nix clone-recursive lib
```

Then build like usual but use your local changes as source:

```sh
nix-build --arg srcRoot ./lib
```

### Migrate from dapp submodules

To remove the need for dappsys submodules completely you can run `dapp2nix
migrate` in one of your existing dappsys repos. This will look att the
currently fetched submodules and generate a `.dapp.json` lock file from that.

After this you can `git submodules deinit --all -f` and then `git rm -r <empty submodule
dirs>` to remove the old submodules from your git repo.
