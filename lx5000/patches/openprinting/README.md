# OpenPrinting patches for lx5000

Patches that make the preserved driver
(`../../original-source/ghostscript-9.04/gdevlx50.c`) build and run with
current Ghostscript. The preserved source itself is not modified.

## Patch order

| Order | Patch | Required |
|---|---|---|
| 1 | `0001-port-to-current-ghostscript-api.patch` | Yes, for current Ghostscript |

## 0001-port-to-current-ghostscript-api.patch

Ports the driver to Ghostscript API changes made after 9.04. The patch is
limited to these API changes; the driver's printing logic is unchanged.

### Changes

1. **Device procedures: positional table to init function.**
   Current Ghostscript no longer accepts a positional `gx_device_procs`
   table in the device definition; it expects an initialization function.
   - Removed the `lx5000_proctab` macro and the `lx5000_procs` table.
   - Added `lx5000_initialize_device_procs()`, which calls
     `gdev_prn_initialize_device_procs()` (covering the former defaults:
     `open_device`, `close_device`, `output_page`, `get_page_device`) and
     sets the driver's own `get_params`, `put_params`, `map_color_rgb` and
     `map_cmyk_color` by name.
   - The device definition now passes this function instead of the table.

2. **Colour encoding procedures.**
   Current Ghostscript uses `encode_color` / `decode_color`, which did not
   exist when the driver was written. The driver switches between colour
   (CMYK, 4 components, 1 bit each) and black-only (1 component, 1 bit)
   at runtime in `lx5000_put_params()`, swapping its `map_*` procedures.
   The patch sets the matching `encode_color` / `decode_color` in the init
   function (colour is the default mode) and next to each of the four
   existing swaps in `lx5000_put_params()`:

   | Mode | `encode_color` | `decode_color` |
   |---|---|---|
   | Colour | `lx5000_map_cmyk_color` | `cmyk_1bit_map_color_cmyk` |
   | Black-only | `gdev_prn_map_rgb_color` | `gdev_prn_map_color_rgb` |

   `cmyk_1bit_map_color_cmyk` matches the bit layout produced by
   `lx5000_map_cmyk_color` at 1 bit per component (C<<3 | M<<2 | Y<<1 | K).
   The black-only pair matches `gdev_prn_initialize_device_procs_mono()`.
   Without this, `encode_color` stays fixed to the CMYK mapper and
   black-only mode produces no image data.

3. **Printer output stream: `FILE *` to `gp_file *`.**
   The `print_page` procedure now receives a `gp_file *`. Changed the
   stream parameter type in `lx5000_print_page()`, `pageInit()`,
   `pageEnd()` and two helper functions, and replaced the stream calls
   with their Ghostscript equivalents: `fwrite` to `gp_fwrite` (6),
   `putc` to `gp_fputc` (2), `fflush` to `gp_fflush` (1). All of these
   calls write to the printer stream; the arguments are unchanged.

4. **Non-GC memory allocator.**
   `gs_lib_ctx_get_non_gc_memory_t()` no longer exists. In
   `getColourBufs()`, the 3 allocations and 3 frees now use the device's
   own allocator, `lx5000dev->memory->non_gc_memory`, as other contrib
   drivers do (e.g. `gdevlx32.c`).

### Not changed

- The pre-existing `-Wuninitialized` warning in `lx5000_print_page()`
  (`pens[YELLOW_X][LO_PEN].finalLine`), also present in Ghostscript 9.04,
  is left as is. It may be related to Ghostscript bug 691820.

## Applying

```
cp ../../original-source/ghostscript-9.04/gdevlx50.c .
patch -p1 < 0001-port-to-current-ghostscript-api.patch
```

## Building with current Ghostscript

The driver is built as part of Ghostscript. In a Ghostscript (ghostpdl)
source tree:

1. Copy the patched `gdevlx50.c` to `contrib/`.
2. Add a build rule to `contrib/contrib.mak`, modelled on the `lxm3200`
   rule (the recipe lines must start with a tab):

   ```
   ### ----------- Lexmark 5000 device ------------ ###

   lx5000_=$(DEVOBJ)gdevlx50.$(OBJ)

   $(DEVOBJ)gdevlx50.$(OBJ) : $(CONTRIBSRC)gdevlx50.c $(PDEVH) $(gsparam_h) \
                              $(CONTRIB_MAK) $(MAKEDIRS)
   	$(DEVCC) $(DEVO_)gdevlx50.$(OBJ) $(C_) $(CONTRIBSRC)gdevlx50.c

   $(DD)lx5000.dev : $(lx5000_) $(DD)page.dev \
                              $(CONTDEV) $(CONTRIB_MAK) $(MAKEDIRS)
   	$(SETPDEV) $(DD)lx5000 $(lx5000_)
   ```

3. Add `lx5000` to `LEXMARK_CONTIB_DEVS` in `configure.ac`.
4. Build:

   ```
   ./autogen.sh
   ./configure --without-x --with-drivers=ALL,LEXMARK_CONTRIB
   make gs
   ```

   (`--with-drivers=LEXMARK_CONTRIB` alone produces an incomplete build that
   fails to link.)

Note: current Ghostscript's `configure` reports the Lexmark contrib devices,
including `lx5000`, as deprecated and due for removal unless maintainers or
testers volunteer.

## Testing

Tested against Ghostscript master `e47b17bcd` on Ubuntu 22.04 (WSL2),
x86_64, GCC 13.1.0:

- Compiles with no errors; the only warning is the pre-existing
  `-Wuninitialized` one.
- `gs` links and lists the `lx5000` device.
- `examples/colorcir.ps` and `examples/tiger.eps` render to a file in both
  modes, exit code 0, no hang within 60 seconds:
  - colour (default): non-empty output; output begins with the driver's
    `init_string` command bytes
  - black-only (`-dBitsPerPixel=1`): non-empty output

```
gs -q -dNOPAUSE -dBATCH -dSAFER -sDEVICE=lx5000 -sOutputFile=out.prn input.ps
gs -q -dNOPAUSE -dBATCH -dSAFER -sDEVICE=lx5000 -dBitsPerPixel=1 -sOutputFile=out.prn input.ps
```

The original printer hardware was **not available**. The output was not
verified on a real printer.