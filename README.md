# StayNest: The Data Factory Challenge

**Session 9 · Azure Data Factory and Data Orchestration**

A small, hands-on Azure Data Factory (ADF) project that automates moving StayNest's daily data files from a `raw` folder into a `bronze` folder in the data lake.

---

## Background

StayNest, a hotel booking platform, receives daily files from its systems: **hotels**, **customers**, and **bookings**. Today someone copies these into the data lake by hand. The team wants that movement to be automatic, reliable, and easy to extend when a new file type appears, without writing a copy step for every file.

ADF is first an **orchestrator**: it connects to sources, moves the bytes, and schedules the work. For heavy transformation it calls out to compute such as Databricks or SQL rather than doing the work itself.

## What This Project Covers

| # | Task | ADF concept |
|---|------|-------------|
| 1 | Connect to Azure Storage | Linked service |
| 2 | Point at source and sink locations | Datasets |
| 3 | Copy `hotels.csv` from `raw` to `bronze` | Copy activity |
| 4 | List the contents of the `raw` folder | Get Metadata activity |
| Stretch | Move every file in `raw` without naming them | Parameterised dataset + ForEach |

## Architecture

```
            ┌───────────────────────── Azure Data Factory ─────────────────────────┐
            │                                                                      │
  raw/      │   ds_source ──► Copy activity ──► ds_sink                             │     bronze/
  hotels.csv│   (raw/hotels.csv)                (bronze folder)                     │ ──► hotels.csv
  customers.csv                                                                     │
  bookings.csv  ds_raw_folder ──► Get Metadata (Child Items) ──► list of file names │
            │                                                                      │
            └──────────────────── both datasets use one linked service ────────────┘
                                   (Azure Blob Storage / ADLS Gen2)
```
**Get Metadata output** 

**Observation:** 

I created ds_raw_folder pointing at the raw container with no file name,
and added a Get Metadata activity using Child Items. 

After Debug, the activity succeeded and returned childItems listing three files,
all of type File: bookings.csv, customers.csv, hotels.csv. 

This shows Get Metadata can list a folder's contents at run time, which could later feed a ForEach to process each file.


## Key Takeaways

- A **linked service** is the reusable connection; **datasets** describe data locations on it; **activities** do the work.
- ADF is an orchestrator: it moves and schedules, and delegates heavy transformation to other compute.
- **Get Metadata + ForEach** replaces one copy step per file with a single reusable pipeline.

## Tech Stack

Azure Data Factory · ADLS Gen2 · CSV
