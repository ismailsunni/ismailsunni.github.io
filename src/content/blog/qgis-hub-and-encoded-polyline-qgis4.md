---
title: QGIS Hub Plugin 0.6.0 and Encoded Polyline 1.0.0 are QGIS 4 ready
date: 2026-06-02
description: "Two of my QGIS plugins — QGIS Hub Plugin and Encoded Polyline — got new releases with QGIS 4 / Qt6 support, plus a few quality-of-life improvements."
tags: ["QGIS", "QGIS Plugin", "Open Source", "Python", "Qt6", "QGIS Hub", "Encoded Polyline"]
category: "Development"
draft: false
toc: true
ogImage: "/media/qgis-hub-resource-browser.png"
---

QGIS 4 is around the corner, and it brings a big change for plugin developers: the move from Qt5 to Qt6. To get ahead of it, I spent some time making sure two of my plugins keep working on the new version. I'm happy to share that both [QGIS Hub Plugin](https://github.com/qgis/QGIS-Hub-Plugin) `0.6.0` and [Encoded Polyline](https://github.com/ismailsunni/qgis-encoded-polyline) `1.0.0` are now out, and both run on QGIS 3 / Qt5 **and** QGIS 4 / Qt6.

## QGIS Hub Plugin 0.6.0

The QGIS Hub Plugin lets you browse and fetch resources from the [QGIS Hub](https://hub.qgis.org/) — styles, processing models, processing scripts, 3D models, layer definitions, maps, and more — directly from inside QGIS, without hunting through a website and downloading files manually.

![QGIS Hub Plugin resource browser](/media/qgis-hub-resource-browser.png)
*The QGIS Hub Plugin resource browser*

The headline of this release is **QGIS 4 / Qt6 support**, but a number of smaller improvements landed too. Here are the highlights:

### QGIS 4 / Qt6 support

- The plugin now declares `supportsQt6=True` and works on both QGIS 3 (Qt5) and QGIS 4 (Qt6).
- All Qt enum usage was migrated to the fully-scoped form (`Qt.ItemDataRole.UserRole`, `QSizePolicy.Policy.Minimum`, `QNetworkReply.NetworkError.NoError`, and so on) so the same code runs on both PyQt5 and PyQt6.
- `QRegExp` was replaced with `QRegularExpression`, since Qt6 dropped the former.
- A Qt6 / QGIS 4 integration test job (`qgis/qgis:4.0`) was added to CI, running alongside the existing Qt5 LTR job, so we catch regressions on both targets.

One quirk worth mentioning: Qt6 in QGIS 4 doesn't ship the webp image-format plugin, so webp thumbnails wouldn't render. To work around it, the plugin now has a [Pillow](https://python-pillow.org/)-based webp thumbnail fallback (Pillow is an optional dependency).

### Quality-of-life improvements

- A **"Clear cache"** button in the settings to wipe the cached resource list and downloaded thumbnails.
- A **thumbnail download progress bar** in the QGIS message bar while the resource browser is being populated for the first time.
- **Uniform square-canvas icons**, so list-view cells are the same size regardless of the thumbnail's aspect ratio.
- A clearer error message when you try to add a PyQt5-only processing script on Qt6, instead of an opaque traceback.

There were also a handful of fixes — translation extraction for the new error branches, recursive file counting in the cache cleaner, and a couple of Qt6-specific UI glitches. You can read the full list in the [changelog](https://github.com/qgis/QGIS-Hub-Plugin/blob/master/CHANGELOG.md).

## Encoded Polyline 1.0.0

The [Encoded Polyline](https://github.com/ismailsunni/qgis-encoded-polyline) plugin decodes and visualizes an [encoded polyline](https://developers.google.com/maps/documentation/utilities/polylinealgorithm) and loads it into QGIS as a memory layer. Polyline encoding is a lossy compression algorithm that stores a series of coordinates as a single string — it's what routing services like Google Maps and Valhalla return.

![Encoded Polyline dialog](/media/encoded-polyline-dialog.png)
*The Encoded Polyline dialog*

I originally built it to debug Valhalla's routing output, because the online tools I found were limited — they couldn't compare two polylines, hung on long lines, or couldn't save the result. The plugin supports a custom layer name, custom precision (Google uses 5, OpenStreetMap uses 6), and ships with a few samples to play with.

![Yogyakarta Ring Road decoded from an encoded polyline](/media/encoded-polyline-jogja.jpg)
*Sample output: the Yogyakarta Ring Road, decoded from an encoded polyline*

This `1.0.0` release marks its first stable, non-experimental version. The changes:

- **QGIS 4 / Qt6 support** (`qgisMaximumVersion=4.99`).
- Dropped the vendored `six` dependency — no longer needed now that Python 2 is long gone.
- Added a GitHub Actions CI with lint, syntax check, and polyline tests.
- The packaging now bundles only the files actually needed for the plugin upload.

## Why bother with QGIS 4 now?

The Qt5 → Qt6 migration is the kind of work that's easy to put off until it breaks. But doing it early has a nice payoff: the same codebase keeps working on the current QGIS 3 LTR while being ready for QGIS 4 the day it lands. Setting up a CI matrix that tests both Qt5 and Qt6 means I get a heads-up the moment something diverges, instead of finding out from a bug report after release.

If you use either plugin, please update and let me know if anything breaks on QGIS 4. Issues and PRs are very welcome:

- QGIS Hub Plugin: <https://github.com/qgis/QGIS-Hub-Plugin>
- Encoded Polyline: <https://github.com/ismailsunni/qgis-encoded-polyline>

Fin.
