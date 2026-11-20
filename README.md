<p align="center">
  <img src="assets/steamling-logo.svg" alt="Steamling logo" width="180">
</p>

<h3 align="center">Steamling base</h3>

<p align="center">The frozen set of Arch Linux ARM packages that every Steamling build and handheld installs from.</p>

<p align="center">
  <a href="https://github.com/Steamling/steamling"><b>Steamling</b></a> ·
  <a href="https://github.com/Steamling/steamling/blob/main/docs/building/own-base.md"><b>How the base is made</b></a> ·
  <a href="https://github.com/Steamling/base/releases"><b>Releases</b></a>
</p>

## What this is

Steamling is made of two kinds of packages. The ones we build ourselves (the kernel, Mesa,
gamescope, Governor and the rest) live in the [main repo](https://github.com/Steamling/steamling).
Everything else comes from [Arch Linux ARM](https://archlinuxarm.org), and that's the **base**.

Arch Linux ARM updates every day, which is great for a desktop and bad for a handheld you want to
just work. So Steamling takes a snapshot of exactly the packages a build used, around 1,100 of
them, and keeps it here. Every build and every handheld then gets the same versions, and the base
only moves when we decide to move it.

There's nothing to download here by hand. pacman reads these releases directly.

## How it's laid out

The repo has no code, only releases:

| Release | What's in it |
| --- | --- |
| `base` | The live base: the package database, the lockfile, the Arch Linux ARM rootfs tarball the build starts from, and the first third of the packages |
| `base-2`, `base-3` | The rest of the packages |
| `base-prev`, `base-prev-2`, `base-prev-3` | The base before this one, kept so the last release can still be rebuilt |
| `base-next*` | Only there while a new base is uploading. It replaces `base*` once every file is up |

GitHub allows 1,000 files per release and the base has over 2,000 (every package comes with its
signature), which is why it's split. Each release's title says which snapshot it belongs to, and
`SNAPSHOT_ID` in `base` holds the id of the live one.

## Using it

A Steamling image already has this set up, so you don't need to do anything. For reference, the
section in `/etc/pacman.conf` looks like this:

```ini
[steamling-base]
SigLevel = Required DatabaseOptional
CacheServer = https://github.com/Steamling/base/releases/download/base-2
CacheServer = https://github.com/Steamling/base/releases/download/base-3
Server = https://github.com/Steamling/base/releases/download/base
```

pacman tries the `CacheServer` lines first and quietly moves on when a package isn't there, so it
finds every package whichever release holds it. The database only ever comes from `base`.

`sudo steamling-base status` on a handheld shows which snapshot it's on.

## Can I trust these files?

The packages are Arch Linux ARM's own builds, unchanged, and each one comes with Arch Linux ARM's
signature. pacman checks every one against their key (`SigLevel = Required`) before installing
anything, so a package that was altered after they signed it is refused.

You'll notice some file names have a `_` where Arch Linux ARM's have a `:` or a `+`
(`alsa-card-profiles-1_1.6.9-1-aarch64.pkg.tar.zst`, for example). GitHub doesn't allow those
characters in release files. Only the name changes: the signature covers the contents, so it still
checks out.

## How a new base gets here

It's cut on the build machine with one command from the main repo:

```bash
./build.sh --build-base
```

That builds Steamling against live Arch Linux ARM, collects exactly what the build installed,
uploads it here and then builds again from the new base to make sure it works.
[docs/building/own-base.md](https://github.com/Steamling/steamling/blob/main/docs/building/own-base.md)
walks through it, including how to host a base of your own somewhere else.

## Licenses

Every package here keeps its own license; you'll find it inside the package, under
`/usr/share/licenses`. Steamling only collects and hosts them, as built by
[Arch Linux ARM](https://archlinuxarm.org). Steamling's own code is
[GPL-2.0-or-later](https://github.com/Steamling/steamling/blob/main/LICENSE.md).
