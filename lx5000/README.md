# lx5000: Ghostscript driver for Lexmark 500, Z51 and Z82

`lx5000` is a Ghostscript printer device that converts PostScript and PDF
input into the proprietary bitmap data format of some early Lexmark inkjet
printers. It was written by Peter B. West in 1999-2000, shipped as part of
Ghostscript from 2007 to 2011, and removed from Ghostscript in version 9.05.

This directory preserves the last upstream copy of the driver, from the
Ghostscript 9.04 release.

## Status

- **Source:** preserved untouched, byte-identical to Ghostscript 9.04.
- **Build:** does **not** compile against current Ghostscript (see
  [Build status](#build-status)). Compatibility patches will be provided
  separately under `patches/openprinting/`.
- **Hardware:** the original printer hardware was **not available** for
  testing. Nothing in this directory has been verified on a real printer.

## Supported printers

According to the OpenPrinting archive README: Lexmark 500, Z51 and Z82
(the driver header itself refers to the "Lexmark 5000").

Modes documented in the source header: black-and-white and colour at
600x600 (default), 1200x600 and 300x600 dpi, unidirectional. No photo
cartridge support.

## Directory layout

```
lx5000/
├── original-source/
│   └── ghostscript-9.04/
│       └── gdevlx50.c        # driver source, untouched
├── patches/
│   └── openprinting/         # compatibility patches (separate PR)
└── README.md
```

## Provenance record

| Field | Value |
|---|---|
| Driver | `lx5000` (Ghostscript device), file `gdevlx50.c` |
| Preserved version | Copy shipped in Ghostscript 9.04 (`gs/contrib/gdevlx50.c`) |
| Ghostscript tag / commit | `ghostscript-9.04`, `91954fa8aadd8f7168a4bfa5e219c5c73a5e7477` (2011-08-05) |
| RCS keywords in file | `$Id: gdevlx50.c,v 1.4 2002/07/30 18:53:21 easysw Exp $` (ESP Ghostscript's revision, not the author's) |
| Original author | Peter B. West |
| First release | 1999-10-08 (per source header) |
| Based on | Lexmark 5700 driver by Stephen Taylor; Lexmark 7000 work by Henryk Paluch |
| Author's last known version | `gdevlx50.c,v 1.41`, 2000-04-18 (per author's homepage, Wayback Machine) |
| Original project pages | `http://www.powerup.com.au/~pbwest` and later `http://pbw.id.au/lexmark/lexmark.html` (both via Wayback Machine) |
| Original distribution | Source RPM `ghostscript-6.0-3.nosrc.rpm` for Aladdin Ghostscript 6.0 (per `http://pbw.id.au/ghostscript/compileGS.html`); the archived download link returns 404, not located |
| Historical mirror | https://openprinting.org/download/printing/lx5000/ |
| License | GPL-2.0-or-later (alternatively AFPL as part of Aladdin Ghostscript) |
| License location | Header comment of `gdevlx50.c` |
| Last known upstream release | Ghostscript 9.04 (2011-08-05) |
| SHA-256 (`gdevlx50.c`) | `2841bbfc712fd0160a9bbfd8dba9897e087887690fe09451e01bafbae9c05c6d` |
| Size (`gdevlx50.c`) | 116,276 bytes |
| Hardware testing | None (hardware not available) |

### Source verification

The preserved `gdevlx50.c` was compared against two copies:

1. `gs/contrib/gdevlx50.c` at Ghostscript tag `ghostscript-9.04`
   (commit `91954fa8a`), from the Ghostscript git repository.
2. `gdevlx50.c` from the OpenPrinting archive
   (https://openprinting.org/download/printing/lx5000/gdevlx50.c).

Both are byte-identical (`diff` reports no differences; SHA-256 above).

**Note on the author's original file:** FreeBSD's Ghostscript ports
(ghostscript-gnu 7.07, ghostscript8 8.56) fetched `gdevlx50.c` directly from
the author's site (`homepage.powerup.com.au/~pbwest/lexmark/`) and recorded a
size of 99,428 bytes. The Ghostscript 9.04 copy is 116,276 bytes, so it
contains changes made after the author's release (likely in ESP Ghostscript
and later upstream maintenance). The author's original file could not be
retrieved for comparison. Because the RCS keywords were rewritten by ESP
Ghostscript, the author's version number (1.41) can no longer be read from
the file itself.

### History

| Date | Event |
|---|---|
| 1999-10-08 | First release by Peter B. West |
| 2000-04-18 | Author's version 1.41 (fixes an infinite loop in colour printing) |
| 2000 | Distributed by the author as a source RPM for Aladdin Ghostscript 6.0 |
| 2001-04-11 | Added to Red Hat's ghostscript-6.51 RPM |
| 2001-08-17 | Author's Lexmark page last updated (`lexmark.html,v 1.24`) |
| 2001-11-14 | Added to Debian's ghostscript build (Debian bug #58657) |
| 2002-07-30 | Last edit in ESP Ghostscript (RCS rev 1.4, `easysw`) |
| 2007-06-06 | Merged into upstream Ghostscript by Till Kamppeter as part of the ESP Ghostscript merge (`2bce78bce`; earlier attempts `33e99334f` and `36af9160f` were reverted) |
| 2007-2011 | Maintenance-only upstream changes: `4cedb626c` (include `std.h` first), `4dbf3a1c7` (`private` to `static`), `ebec61315` (static helper functions), `781969994` (whitespace) |
| 2011-06 | Driver authors contacted on gs-devel ("Built-in drivers for old Lexmark printers" thread, referenced by openSUSE) |
| 2011-08-05 | Ghostscript 9.04 released: last release containing the driver |
| 2011-08-16 | Removed from Ghostscript by Henry Stiles (`725cd5ded`, `9c074dc8c`): "Fixes 691820", driver hangs in an infinite loop on several files |
| 2012-05-08 | openSUSE stops providing lx5000 PPDs |

### Evidence that upstream is abandoned

- No author release found after version 1.41 (2000-04-18).
- The author's homepage marks the driver installation section as being
  "of historical interest only".
- Last change to the file outside of mechanical upstream cleanups:
  2002-07-30 (ESP Ghostscript).
- Ghostscript removed the driver in 2011 after contacting the authors;
  it was never re-added.
- The author's Lexmark page (`lexmark.html,v 1.24`) was last modified
  2001-08-17, per its archived footer.

## Distribution copies and patches

| Distribution | Finding |
|---|---|
| Debian | Driver downloaded separately and enabled in Debian's ghostscript build on 2001-11-14 (Torsten Landschoff, closes Debian bug #58657). Debian lenny (8.62.dfsg.1-3.2lenny5) and squeeze (8.71~dfsg2-9+squeeze1, +squeeze2): no patch in `debian/patches/` touches `gdevlx50.c`. Debian's `copyright` file lists `contrib/gdevlx50.c` as (C) 1999 Aladdin Enterprises, 1999-2000 Peter B. West, "GPL-2+ or AFPL". |
| Red Hat | Added to the ghostscript-6.51 RPM on 2001-04-11 (Crutcher Dunnavant). Source RPM not retrieved. |
| openSUSE | Listed in `ghostscript-library` built-in drivers (openSUSE 11.1 era). PPDs dropped 2012-05-08 after upstream removal. |
| FreeBSD | ghostscript-gnu 7.07 and ghostscript8 ports fetched `gdevlx50.c` from the author's site (99,428 bytes). |

No distribution was found to carry driver-specific patches, so there are no
`patches/debian/` or other distribution patch directories.

## License

`gdevlx50.c`:

- Copyright (C) 1999, 2000 Peter B. West
- Portions Copyright (C) 1999 Aladdin Enterprises
- Licensed under the GNU General Public License, version 2 or (at your
  option) any later version. The header also allows distribution under the
  Aladdin Free Public License as part of Aladdin Ghostscript; this
  repository uses the GPL option.

The original license header is preserved unmodified in the source file.

The author's HTML protocol documents from the OpenPrinting archive are not included, since they carry no explicit license (see Excluded files).

## Build status

### Original source against current Ghostscript

- Ghostscript: master `e47b17bcd` (2026-10-24)
- Environment: Ubuntu 22.04 on WSL2, x86_64, GCC 13.1.0 (Ubuntu 13.1.0-8ubuntu1~22.04)
- Method: copied `gdevlx50.c` to `contrib/`, added a `contrib/contrib.mak`
  rule modelled on the `lxm3200` rule, added `lx5000` to
  `LEXMARK_CONTIB_DEVS` in `configure.ac`, then:

  ```
  ./autogen.sh
  ./configure --without-x --with-drivers=LEXMARK_CONTRIB
  make
  ```

  (The `LEXMARK_CONTRIB` driver group is not built by default and is marked
  as deprecated in `configure.ac`.)

- **Result: fails to compile.**
  - `gdevlx50.c:789`: the device definition passes a positional
    `gx_device_procs` table, but current Ghostscript expects an
    initialization function (`initialize_device_procs` style).
  - `gdevlx50.c:2020`: `lx5000_print_page()` takes `FILE *`, but current
    Ghostscript declares `print_page` with `gp_file *`.
  - Several `-Wincompatible-pointer-types` warnings: entries of the
    positional procs table no longer line up with the current procedure
    slots.

### Original source within Ghostscript 9.04

- Ghostscript: tag `ghostscript-9.04` (`91954fa8a`), built from `gs/`
- Environment: Ubuntu 22.04 on WSL2, x86_64, GCC 13.1.0
- Method: `./autogen.sh --without-x` (copied `config.guess`/`config.sub`
  from `/usr/share/misc` because current autoconf requires them), then
  `./configure --without-x` and `make obj/gdevlx50.o`
- `lx5000` is enabled in Ghostscript 9.04's default driver list
- **Result: compiles**, with one warning:
  - `gdevlx50.c:2148`: `pens[YELLOW_X][LO_PEN].finalLine` is used
    uninitialized in `lx5000_print_page()` (`-Wuninitialized`). This may
    be related to bug 691820 but has not been investigated.
- Only the driver object was built; a full Ghostscript 9.04 build with
  GCC 13 was not attempted.

### Known issues

- Ghostscript bug 691820: the driver hangs in an infinite loop on several
  input files (reason for removal in 2011; bug 692399 is a duplicate).

## Excluded files

The OpenPrinting archive (https://openprinting.org/download/printing/lx5000/)
contains additional files. None of them are part of the Ghostscript 9.04
source tree. They remain available at that URL.

| File | Reason for exclusion |
|---|---|
| `contrib.mak.addon` | Same build rule as in Ghostscript 9.04's `gs/contrib/contrib.mak` (lines 514 onward); documented here instead |
| `lx5000.patch` | OpenPrinting's patch to re-add the driver to Ghostscript 9.05 (2011); superseded by the compatibility patches planned under `patches/openprinting/` |
| `README.txt` | OpenPrinting's archive notes, not part of the original driver; its content is reflected in this README |
| `blckalgn.out`, `blckhcln.out`, `showcart.out`, `hidecart.out` | Printer maintenance command data (head alignment, head cleaning, cartridge movement), not driver source |
| `lx.control`, `lexp5` | Author's maintenance helper scripts for Red Hat's historical `rhs-printfilters` setup |
| `*.gif` | Web page decoration |
| `initialisation.html`, `printheads.html`, `protocol.html`, `swipecmd.html` | Author's printer protocol documentation (Peter B. West, 1999; author RCS IDs). No license statement in the files, on the author's archived pages, or in Debian's copyright records, so not redistributed here. SHA-256: `ed16bdd2396da29ab40e4afb49a0bfc2ca6b6f30e494db4fb78b50b44c3fb1fa` (initialisation.html), `ab86947ab75d38ce74eb2f8f29c803f706aced1e6b7803d953bbd6e336e5c29c` (printheads.html), `c2d4acab1875bd0cdfb541a37f218d702d4f522bbffabf177488dc16b67520f3` (protocol.html), `c72e692c69d2e58dbb99f5abcab8b1a427d398f43d8e573026d98d2903ff3e9e` (swipecmd.html) |

## References

- OpenPrinting archive: https://openprinting.org/download/printing/lx5000/
- Ghostscript bug 691820: https://bugs.ghostscript.com/show_bug.cgi?id=691820
- Author's homepage (Wayback Machine): http://web.archive.org/web/20190303235842/http://pbw.id.au/lexmark/lexmark.html
- Author's Ghostscript 6.0 build page (Wayback Machine): https://web.archive.org/web/2019/http://pbw.id.au/ghostscript/compileGS.html
- Issue: https://github.com/OpenPrinting/legacy-drivers/issues/18
