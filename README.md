WARNING: There will be a lot of `push --force` until things run smooth.

# programs.sqlite for Nix Flake based systems

## TL;DR

Add to `inputs` in `flake.nix`:
```nix
flake-programs-sqlite.url = "github:wamserma/flake-programs-sqlite";
flake-programs-sqlite.inputs.nixpkgs.follows = "nixpkgs";
```

Add `flake-programs-sqlite` to the arguments of the flake's `outputs` function.

Add to system configuration:

```nix
programs.command-not-found.dbPath = flake-programs-sqlite#programs-sqlite;
```

## Why?

NixOS systems configured with flakes and thus lacking channels usually have a broken
`command-not-found`. The reason is that the backing database `programs.sqlite` is only
available on channels. The problem is that the channel url can not be determined from
the `nixpkgs` revision alone, as it also contains a build number.

This is an attempt to provide a usable solution, motivated by <https://discourse.nixos.org/t/how-to-specify-programs-sqlite-for-command-not-found-from-flakes/22722/3>

## How?

The channel page is regularly scraped for the revision and file hashes, then a
[lookup table](./sources.json) from revisions to URL and hashes is amended with any
new information.
The lookup table is used to create a fixed-output-derivation (FOD) for `programs.sqlite`
based on the revision of `nixpkgs` passed as input of this flake.

## Usage

see TL:DR above

## Development

The flake provides a minimal devshell, but hacking on the code with a editor and
running `nix run .#updater` is valid, too.

Development happens on the `tooling` branch, which is then merged into the `main`
branch. Updates to the JSON file go directly to `main`. Releases of the tooling are
also cut from the `tooling` branch. There are no releases for the JSON files.

## Alternatives

- [nix-index](https://github.com/bennofs/nix-index#usage-as-a-command-not-found-replacement)

## Licensing

The Nim code to scrape the metadata is released under MIT License.
The Nix code to provide is the FODs is released under MIT License.
The database itself (JSON) is public domain.
