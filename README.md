# Building Detection with EO and AI

**Towards smarter permitting and urban planning.** An open-source framework for finding new buildings
automatically from satellite data — Earth observation (EO) — and artificial intelligence (AI), so that a
municipality checks as few places as possible and most of them turn out to be real.

> **Status:** work in progress (2025–2027). This repository holds the project's public documentation. The
> code, trained models and their parameters will be published here as open source.

## Why

Sweden's building permit system (*bygglov*) is changing: permits will no longer be required for many small
complementary buildings — small houses (*attefallshus*), sheds and extensions. Municipalities have relied on
the permit process and the survey check after construction (*lägeskontroll*) to keep their base map
(*baskarta*) and the national building database up to date. Without permits, new small buildings appear in
unknown places, and keeping the building record current gets harder.

That record matters. It underpins the municipality's long-term strategic plan (*översiktsplan*), its
detailed development plans (*detaljplan*), and the planning of water, sewage and electricity networks. Many
small, unrecorded buildings add up to lost green space, more sealed surfaces and more load on infrastructure.
Building-permit departments, meanwhile, move from approving permits towards enforcement.

Mapping buildings today relies on manual stereo photogrammetry from aerial or drone imagery: accurate, but
labour-intensive, updated about once a year, and in some places restricted (drones near airports, for
example). Satellites cover the whole municipality every few days, and radar sees through cloud.

## What the project does

The project develops and tests a method that combines radar (synthetic aperture radar, SAR) and optical
satellite imagery — starting with the free Copernicus Sentinel data from the European Space Agency — with
AI, to point out where new buildings have appeared. Flagged places are checked against higher-resolution
imagery and the municipal base map, and the results are shown over time in a map viewer.

**Four objectives:**

1. **Bridge research and municipalities** — pilot EO and AI methods together with Stockholm University,
   building long-term knowledge transfer and capacity.
2. **Build EO- and AI-capable infrastructure** — define the computing needed for AI-assisted EO processing,
   so municipalities can build capabilities that reach beyond building detection.
3. **Advance EO sensor fusion** — use AI to combine radar and optical imagery, balancing accuracy and cost.
   The trained models and parameters will be published openly.
4. **Create open-source monitoring and visualisation tools** — an automated monitoring workflow, and a
   time-based plugin (time slider) for the open-source [Origo](https://github.com/origo-map/origo) web
   map, to support building-permit departments in their new enforcement role.

**Targets:**

- Detect buildings of 30 m² and larger with at least 80 % accuracy across varied Swedish landscapes
  (urban, rural, archipelago), validated with combined radar and high-resolution optical data.
- Reduce manual verification by at least 30 % compared with photogrammetry.
- Support building-inventory updates twice a year with an open-source framework.
- Publish the detections through automated workflows to Origo, and train at least five building-permit
  staff to use the tools.
- Engage at least one master's student to evaluate the framework in a municipality outside the
  consortium.
- Release all code, models and documentation as open source within three months of the project's end.

**Study areas:** Haninge municipality is the primary testbed — dense urban districts, rural land, coast and
archipelago. Sundbyberg municipality tests how well the method carries over to a dense urban setting.

## Documentation

| Page | What it covers |
|---|---|
| [Method](docs/method.md) | How places are flagged and checked |
| [Data](docs/data.md) | Which satellite and reference data are used |
| [Results](docs/results.md) | What has been measured, and how it is counted |
| [Compute and storage](docs/compute.md) | The computing set-up the project needs and has acquired, data volumes per km², lessons learned |

## Partners and funding

| Partner | Role |
|---|---|
| Sundbyberg municipality | Applicant; integration into municipal workflows and visualisation; validation site |
| Haninge municipality | Primary testbed; validation data |
| Stockholm University | Research and development of the EO and AI methods; master's theses |
| Spatial Impact | Project leader and coordination |

Funded by the Swedish National Space Agency (*Rymdstyrelsen*), call *Klimat och miljö 2025*, November 2025 to
October 2027.

<!-- TODO: the acknowledgement wording the funder requires, if any. -->

## Contact

<!-- TODO: contact person and address. -->

## Licence

No licence has been chosen yet. Until one is, all rights are reserved: you may read this material, but not
reuse or redistribute it without permission.
