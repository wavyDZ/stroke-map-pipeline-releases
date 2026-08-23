# Stroke Map Pipeline (AO)

A Blender add-on that turns 3D "stroke" geometry sitting on a shell mesh into
flat maps, editable vectors, and a colour 3D print.

Built for footwear design surfaces. You draw strokes on a shell; the add-on
bakes them into masks, traces them to SVG centrelines for you to author
artwork over, then applies that artwork back onto the surface and exports a
3MF with per-triangle filament colour for a Bambu multi-filament print.

**Requires Blender 4.2 or newer.** Tested most heavily on 5.1.

---

## Install

### Recommended: add the extension repository

Set this up once and Blender handles the rest, including updates.

1. **Edit → Preferences → System →** make sure **Allow Online Access** is on
2. Go to **Get Extensions**
3. Click the **⌄** dropdown at the top right → **Repositories…**
4. Press **+ → Add Remote Repository**
5. Paste this URL:

   ```
   https://wavydz.github.io/stroke-map-pipeline-releases/index.json
   ```

6. Confirm, then find **Stroke Map Pipeline (AO)** in the extensions list and
   click **Install**

New versions then show up as updates inside Blender — you don't have to come
back here to find out one exists.

**Allow Online Access is off by default in Blender**, and with it off the
repository simply fails to refresh. If nothing appears after adding the URL,
that's the first thing to check.

### Alternative: install the zip by hand

1. Download the `.zip` from the [Releases](../../releases) page — don't unzip it
2. In Blender: **Edit → Preferences → Get Extensions**
3. Click the **⌄** dropdown at the top right → **Install from Disk…**
4. Pick the zip

You'll need to repeat this for each new version.

#### macOS: Safari unzips it for you, which breaks this route

Safari expands archives automatically, so clicking the download link leaves
you with an unzipped *folder* — and Blender needs the `.zip` itself. Install
from Disk won't accept the folder.

Either:

- **right-click the link → Download Linked File**, which skips the expansion, or
- turn the behaviour off once: **Safari → Settings → General →** untick
  **"Open 'safe' files after downloading"**

Chrome and Firefox don't do this, so a plain click works there.

If you already have an unzipped folder, don't try to re-zip it — macOS adds
metadata files that can confuse the installer. Download it again instead.

The repository route above avoids all of this, since Blender does the
downloading itself.

---

However you install, the panel appears in the 3D viewport sidebar (press
**N**) under a **Stroke Maps** tab.

---

## The pipeline

Five stages, run in order from the panel.

**1. UV Prep** — creases to seams, angle-based unwrap, outer-skin isolation,
shape-aware island orientation, polarity normalisation.

**2. Mask** — shrinkwraps the strokes onto the surface, bakes ambient
occlusion, divides out a self-occlusion reference, and thresholds the result
into a mask. Masks rebuild live as you tune. **Export Maps** writes the PNGs
plus a UV-layout SVG for you to author over.

**3. Vectorise** — skeletonises the mask and emits centreline Bezier SVG.

*— you author the artwork externally here —*

**4. Apply Maps** — assign per-surface colour and height maps, apply material
and/or modifier displacement, set scene-wide subdivision. A perforation map
can be marked here as an overlay: green will be cut, red is refused.

**5. Print** — duplicates the surfaces, applies modifiers, optionally
perforates and decimates, and exports a 3MF carrying per-triangle filament
colour.

There is deliberately no single "run everything" button spanning stages 3 and
4, because artwork gets made in between.

---

## Things worth knowing before you start

**Your mesh needs creased edges or existing UV seams.** A closed shell with
neither unwraps into a single island, which silently puts the outer skin's
artwork on the inner skin too. The add-on warns when it detects this — read
what stage 1 reports rather than clicking past it.

**Perforation is experimental.** It needs a two-skinned shell, and hole shape
follows mesh density: a 2.5 mm hole wants subdivision 5 or more, and a coarse
cage gives square holes. Perforations too close to the UV island border are
refused whole, and the add-on writes a review map showing which.

**Colour comes out of Bambu Studio from 3MF only.** Bambu ignores OBJ
materials and doesn't support texture mapping, so colour is baked to one flat
value per triangle against a filament palette. Expect ragged,
topology-following colour boundaries rather than the crispness of the source
artwork — the print preview shows you exactly this.

**The AO threshold may need tuning on your machine.** Stage 2 rests on a
Cycles AO bake, which is a sampled visibility fraction and need not agree to
the last digit between CPU, CUDA/OptiX and Metal. The shipped default was
tuned on one machine; treat it as a starting point.

**Decimation defaults off.** Colour resolution *is* vertex count, so
decimating trades away colour fidelity. Turn it on when your slicer struggles
with file size, not as a matter of course.

---

## Found a bug?

Open an [issue](../../issues) — that's the easiest place for me to keep track
of them, and it means other people can see what's already been reported.

Useful things to include:

- your Blender version and platform (Windows / macOS / Linux)
- which stage it happened in
- what you expected versus what you got
- for colour or print problems: the filament palette, which is printed to the
  console on every export and is usually the single most useful thing to paste

Screenshots help a lot for anything visual — a mask that looks wrong, a print
preview that doesn't match the artwork, perforations landing somewhere odd.

This is a side project rather than a supported product, so fixes come when
they come. But reports are genuinely welcome: most of what this add-on gets
right came from someone noticing something looked off.

---

## Licence

GPL-3.0-or-later. See [LICENSE](LICENSE).

Blender add-ons link against Blender's Python API, so they must be
GPL-compatible; the add-on ships as readable Python source and you're free to
modify it.
