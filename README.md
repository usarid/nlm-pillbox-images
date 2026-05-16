# NLM Pillbox Images (2020 archive)

A self-contained mirror of the **NLM Pillbox** photographic database of US prescription pills — both the original JPEGs from the National Library of Medicine and a set of **background-removed PNG cutouts** suitable for drop-in use in visual user interfaces.

## What's here

| Path | Contents | Size | Files |
|---|---|---|---|
| `images/` | Original NLM Pillbox JPEG photos | ~1.1 GB | 8,693 |
| `images_nobg/` | Same photos with the gray photographer's surface removed (transparent PNG, RGBA) | ~2.5 GB | 8,693 |
| `pillbox_meta.csv` | NLM's accompanying metadata CSV (NDC, RxCUI, ingredient, brand, imprint, color, shape, dosage form, manufacturer, etc.) | 56 MB | 1 |

Filename pairs match across `images/` and `images_nobg/` — e.g. `images/foo.jpg` ↔ `images_nobg/foo.png`.

## Provenance

- **Original archive:** [Pillbox production images, full set, 202008](https://ftp.nlm.nih.gov/projects/pillbox/pillbox_production_images_full_202008.zip) (1 GB ZIP, NLM FTP)
- **Metadata CSV:** [NLM Data Discovery API](https://datadiscovery.nlm.nih.gov/api/views/crzr-uvwg/rows.csv?accessType=DOWNLOAD)
- **Pillbox status:** NLM **retired** the Pillbox program effective January 2021 and froze the dataset at the August 2020 snapshot. There will be no further updates from NLM.

## Background-removal method

The PNGs in `images_nobg/` were produced by running every original through [`rembg`](https://github.com/danielgatis/rembg) with the **`u2netp`** model (compact U²-Net, ~4 MB). Processing took ~30 minutes on an Apple M2 Pro.

Reproducible from the originals using the script in the companion project:
[`usarid/health-assistant` → `scripts/remove_pillbox_backgrounds.py`](https://github.com/usarid/health-assistant/blob/main/scripts/remove_pillbox_backgrounds.py)

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install rembg onnxruntime pillow
python3 remove_pillbox_backgrounds.py --src images --dst images_nobg
```

If you want higher quality (slower, larger model), pass `--model isnet-general-use` or `--model u2net`.

## License

The underlying NLM data is in the **public domain** (US government work, [CC0 / NLM terms](https://www.nlm.nih.gov/web_policies.html)). The background-removal step is a mechanical transformation that adds no new copyrightable expression, so the derivative PNGs are likewise released into the **public domain (CC0)**. No attribution required, but courtesy citation to NLM is appreciated.

## Limitations

- **Frozen at August 2020.** Any prescription drug launched, reformulated, or repackaged after that date is absent. New post-2020 manufacturers of older drugs may not be photographed either.
- **No OTCs, no supplements, no compounded medications.** Pillbox covered prescription drugs only.
- **Quality varies.** Photos were submitted by manufacturers; some are well-lit and consistent, others are not.
- **rembg artifacts.** The `u2netp` cutouts are clean for the majority of pills but occasionally leave a faint halo or chip the edge of a translucent gel-cap. If you need pixel-perfect cutouts, re-run with `isnet-general-use` or process the originals through a higher-quality tool.

## How this is used in the wild

This repo is consumed by [`usarid/health-assistant`](https://github.com/usarid/health-assistant) (a personal health vault) as the data source for its "Choose picture" pill-identification UI on the Medications tab. The companion project's setup script clones this repo to skip the ~30-minute one-time preprocessing step.

If you're building something else with these images, no need to involve the consumer project — just clone here directly.

## Updating

This dataset is static and will not be updated unless NLM resumes the program (they have not announced any plans to). If a future cleaner background-removal model produces materially better cutouts, `images_nobg/` may be regenerated; the originals in `images/` will remain unchanged.
