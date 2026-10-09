# Compute and storage

The project processes satellite imagery, aerial photography and airborne laser scanning (LiDAR) over a whole
municipality, and trains deep-learning models on them. This page describes the computing set-up that work
needs, and the one the project has acquired.

## What the work demands

Three kinds of work set the requirements:

- **Large rasters held in memory.** Change detection compares whole-municipality image stacks from several
  dates and sensors at once. Keeping them in memory, rather than reading them piece by piece from disk, is
  what makes an analysis take minutes instead of hours. Memory is therefore the first limit we meet.
- **Deep-learning training and inference.** Building detectors are convolutional neural networks trained on
  image tiles. Graphics memory (VRAM) decides how large a tile and how large a batch fit on the graphics
  processor, and with that how fast a model trains and how much context it sees.
- **Point clouds and very high-resolution imagery.** The reference data — LiDAR with billions of points and
  aerial photography at under 10 cm — runs to hundreds of gigabytes per survey. It needs fast local storage
  and enough processor cores to work through it in parallel.

## Development workstation

For development, experiments and small tests:

| Component | Requirement |
|---|---|
| Processor | A modern multi-core CPU |
| Memory | As much as practical: **at least 64 GB**, preferably **96 GB or more** |
| Graphics | **NVIDIA GeForce RTX 5090, 32 GB VRAM**, for model development and small training runs |
| Storage | Fast local SSD for the working data set and caches |

## Project server

For full-scale training, municipality-wide analysis and continuous operation, the project has acquired a
rack-mounted server built around:

| Component | What it provides |
|---|---|
| Graphics | **NVIDIA RTX PRO 6000 Blackwell, 96 GB VRAM**: three times the workstation's graphics memory, for training on larger tiles, larger batches and larger models |
| Memory | A large pool of **ECC memory** (error-correcting): long runs on large data must not be corrupted silently by a single memory error |
| Processor | A server-class CPU for parallel processing of point clouds and imagery |
| Power and cooling | A power supply and cooling sized for the graphics card and processor running at full load for days at a time |
| Storage | Drive capacity for the archive of satellite, aerial and LiDAR data and the results built from it (see below) |

The workstation and the server run the same software, so a method developed and tested on the workstation
moves to the server unchanged.

## Storage

### What the project holds today

Measured in October 2026 for the test municipality (about 1,170 km², 60 % of it water), after the first
year of development. Sizes in decimal gigabytes (GB).

| Data | Volume | What it is |
|---|---|---|
| NISAR (L-band radar) | ~950 GB | One summer (June to September 2026), four orbit tracks, all four product types |
| Aerial photography | ~830 GB | Two surveys: 2018 (colour) and 2024 (colour and near-infrared), under 10 cm resolution |
| Airborne LiDAR | ~680 GB | Two surveys, 2018 and 2024; the 2024 survey alone holds about 21 billion points |
| Sentinel-1 (C-band radar) | ~73 GB | Three periods (2018, 2024, 2026), about a month each |
| Sentinel-2 (optical) | ~50 GB | The same three periods |
| PlanetScope (3 m optical) | < 1 GB | Two dates over a 146 km² test area |
| Results | ~70 GB | Features, models, detections, evaluations |
| Working cache | ~40 GB | Regenerable copies kept on fast local disk |
| **Total** | **~2.7 TB** | |

Raw input is over 95 % of the volume. Radar archives and very high-resolution reference surveys dominate;
the satellite optical data the method runs on is small.

### Planning figures for your own municipality

Data falls into two kinds, and they scale differently:

- **Area data** — aerial photography, LiDAR, commercial satellite imagery ordered to an outline, and
  results. Its volume grows with the area covered, so it is given per km².
- **Scene data** — free satellite archives (Sentinel-1, Sentinel-2, NISAR). They are delivered as whole
  scenes tens to hundreds of kilometres across, so a small municipality downloads as much as a large one.
  Its volume depends on how many acquisitions are kept, not on the municipality's area — unless the scenes
  are cut to the municipality after processing, which shrinks them to almost nothing.

**Area data**, measured on our deliveries:

| Data | Per km² | Measured on | Scales with |
|---|---|---|---|
| Aerial photography, 8 cm, colour + near-infrared, uncompressed GeoTIFF | **0.78 GB** per survey | 2024 survey, 942 km² (0.44 colour, 0.34 near-infrared) | area; about 1/resolution² |
| Aerial photography, 8 cm, colour, JPEG-compressed | **0.08 GB** per survey | 2018 survey, 579 km² | area |
| LiDAR, compressed (COPC / LAZ) | **~6.5 bytes per point**: 0.13 GB at 20 points/m² | 2024 survey, 21.4 billion points, 138 GB | area × point density |
| LiDAR, uncompressed (LAS) | **~22 bytes per point**: 0.45 GB at 20 points/m² | 2024 survey, same points | area × point density |
| PlanetScope, 3 m, 8 bands | **1.6 MB** per date | 2026 delivery, 331 km² | area × number of dates |
| PlanetScope, 3 m, 4 bands | **0.9 MB** per date | 2018 delivery, 146 km² | area × number of dates |
| Results (features, models, detections) | **~0.06 GB** | first development year, 1,170 km² | area; grows with the number of experiments |

Compression is the largest single lever: the same kind of survey takes 0.08 or 0.78 GB per km² depending
on how it is delivered, and LiDAR delivered in two formats is stored twice.

**Scene data**, per acquisition as downloaded (independent of the municipality's area):

| Data | Per acquisition | Acquisitions a year (typical) |
|---|---|---|
| Sentinel-1 GRD (C-band radar, both polarisations) | ~0.9 GB per scene | one orbit track: 30–60 |
| Sentinel-2 L2A (optical) | ~15–18 GB per month of usable dates | depends on cloud cover |
| NISAR RSLC (raw radar, full resolution) | ~14 GB per frame | per track: ~30 (12-day repeat) |
| NISAR GSLC (geocoded, full resolution) | ~13 GB per frame | as above |
| NISAR GUNW (interferogram) | ~2.5 GB per pair | as above |
| NISAR GCOV (geocoded backscatter) | ~1.7 GB per frame | as above |

Cut to the municipality after processing, scene data costs very little: a 10 m raster of one band in
32-bit floating point is 10,000 pixels × 4 bytes = **40 kB per km² per date** (calculated, not measured).
Even at several bands and dozens of dates a year, that is well under 1 GB per km² a year.

**A rule of thumb** for a municipality of *A* km²:

    storage ≈ A × (aerial survey + LiDAR survey + results)          area data, per survey cycle
            + A × PlanetScope per date × dates kept                 if commercial imagery is used
            + scene archive (fixed, set by which products are kept)

Example, *A* = 100 km², compressed surveys (0.08 + 0.13 GB/km²), weekly 8-band PlanetScope: 21 GB per
survey cycle, 8 GB a year of PlanetScope, ~6 GB of results — plus the scene archive, which at
30–200 GB a year for Sentinel data alone is the largest item unless it is cut to the municipality.

### What continuous operation adds

An **estimate**, not a measurement: one municipality the size of the test site, analysed continuously
through the year. Each row starts from the measured volume above.

| Source | Basis (measured) | Assumption for continuous running | Per year |
|---|---|---|---|
| NISAR | ~950 GB for three months | all products kept | ~3.8 TB |
| | | processed products only; raw radar deleted once processed | ~0.5 TB |
| Sentinel-1 | ~0.9 GB per scene | one scene every 6–12 days | 30–60 GB |
| Sentinel-2 | ~15–18 GB per month-long period | every usable date, all year | 50–200 GB |
| PlanetScope | ~1.6 MB per km² per date (8 bands) | whole municipality monthly to weekly | 25–100 GB |
| Aerial photography and LiDAR | ~1.3 TB per survey (2024) | a new survey every 2–3 years | ~0.5 TB |
| Results and caches | ~110 GB after the development year | one analysis a month | 0.1–0.3 TB |
| **Total** | | lean (processed NISAR only) | **~1–1.5 TB** |
| | | full (all NISAR products kept) | **~4.5–5 TB** |

Three years of operation add roughly **3–15 TB** to today's 2.7 TB, so **6–18 TB** in total, depending
mostly on how much raw radar is kept. With headroom for reprocessing and a second study area, plan for
**10–25 TB of usable storage**, and the same again for backup. A second municipality adds less than the
first: the scene archives can be shared between neighbouring municipalities, and the reference surveys
depend on what that municipality flies.

## Lessons learned from the first year

What the first year of development taught us about computing, and what it means for the project server.

- **Network storage needs a fast link, and a local working disk.** Data on a shared network drive and
  software reached over a remote-desktop link were the largest single slowdown we measured: loading the
  Python analysis libraries took about **400 s** from the network share against 22 s from a local disk, and browsing the 2024 aerial
  survey zoomed out was impractical until we built a 27 GB overview copy of the 445 GB original (a
  whole-municipality view then drew in 0.18 s). Reading satellite images straight from a cloud archive
  cost 1–3.5 s per view; a local copy brought that under a second. The server therefore needs a **fast
  network link (10 Gbit/s or more) to the central storage**, and fast local SSD (NVMe) for the working set
  and caches.
- **Keep one authoritative copy.** Over the year the data spread across several machines and drives,
  and reconciling them took real effort. The archive should live in one place, with local disks holding
  only regenerable caches that can be deleted without loss.
- **Memory runs out before the processor does.** Training data at 0.5 m resolution had to be stored as
  8-bit values because full-precision tiles (64 MB each) would not fit in memory, and building the
  municipality-wide terrain model from LiDAR hit the operating system's memory limit. Hence large ECC
  memory in the server.
- **Graphics memory sets the size of the model.** The detectors so far are deliberately compact (a
  ResNet-34 U-Net trained from scratch on 256-pixel patches). The next steps — training on all eight
  PlanetScope bands, several dates at once, and larger models — need more graphics memory, both for larger
  inputs and for larger batches. Intensive training sessions are what the 96 GB card is for.
- **The storage grows with the radar archive.** A single summer of NISAR was the largest data set in the
  project. Deciding early which radar products to keep, and deleting raw data once processed, decides
  whether storage grows by one or by five terabytes a year.
