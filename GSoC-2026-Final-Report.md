# Google Summer of Code 2026 Final Report

Contributor: Jayanth Vennamreddy  
Organization: Apache Software Foundation  
Project: CyberShuttle / CS-FileSystem  
Repository: https://github.com/cyber-shuttle/CS-FileSystem

## Project Goals

This project built a user-space filesystem for exposing CyberShuttle
AI-for-science dataset metadata through familiar filesystem interfaces. The goal
was to make scientific dataset metadata browsable through materialized
directories, FUSE mounts, and NFS serving, while keeping the dataset connector
layer extensible for future CyberShuttle workflows.

## Work Completed

- Implemented ATLAS metadata loading from TSV files.
- Exposed ATLAS protein entries as directories containing `metadata.json`.
- Added support for additional table-backed datasets including mdCATH,
  MemProtMD, and GPCRmd.
- Implemented a dataset-agnostic metadata flow for TSV/CSV-backed datasets.
- Added `index.json` so each filesystem instance describes the datasets loaded
  in that run.
- Added an official dataset registry exposed through `registry.json` and the
  `datasets/` directory.
- Implemented materialized filesystem export to normal directories.
- Added FUSE mounting support.
- Added NFS server mode for exposing the same dataset tree over NFS.
- Added sample metadata tables under `examples/` so the project can be demoed
  without full public datasets.
- Added release packaging support for macOS and Linux binaries.
- Added user, developer, and distribution documentation.
- Updated the README with setup, materialization, FUSE, NFS, and documentation
  links.

## Current State

The filesystem can currently expose demo and local metadata datasets through
three modes:

1. Materialized directory export
2. FUSE mount
3. NFS server

The repository includes sample tables for reproducible demos. Full public
metadata tables can be used locally but are not committed to the repository.

The project is metadata-first. Large public datasets, restricted scientific
datasets, and datasets requiring accounts or terms acceptance are represented in
the registry without being downloaded automatically during normal demos.

## Key Code / Pull Requests

- Main repository: https://github.com/cyber-shuttle/CS-FileSystem
- Main GSoC pull request: https://github.com/cyber-shuttle/CS-FileSystem/pull/2
- Final report, release packaging, and expanded documentation are included in
  follow-up project maintenance work.

Relevant commits:

- https://github.com/cyber-shuttle/CS-FileSystem/commit/c56008d - Add ATLAS metadata filesystem source
- https://github.com/cyber-shuttle/CS-FileSystem/commit/2f038fa - Add materialized ATLAS export mode
- https://github.com/cyber-shuttle/CS-FileSystem/commit/a8b71a9 - Add NFS server mode for ATLAS metadata
- https://github.com/cyber-shuttle/CS-FileSystem/commit/26b03a0 - Add table-backed datasets and filesystem index
- https://github.com/cyber-shuttle/CS-FileSystem/commit/5495651 - Add official dataset registry metadata connector

## How To Run

Run the sample materialized export:

```bash
cargo run --release -- materialize \
  examples/atlas_sample.tsv \
  mdcath=examples/mdcath_sample.tsv \
  memprotmd=examples/memprotmd_sample.tsv \
  gpcrmd=examples/gpcrmd_sample.tsv \
  /tmp/cs_sample_export
```

Inspect the generated filesystem tree:

```bash
ls /tmp/cs_sample_export
cat /tmp/cs_sample_export/index.json
cat /tmp/cs_sample_export/registry.json
cat /tmp/cs_sample_export/atlas/1r6w_A/metadata.json
```

The same logical tree can also be exposed through FUSE or NFS. See the README
and `docs/user-guide.md` for full commands.

## Challenges / Lessons Learned

- Designing a filesystem abstraction around metadata-first scientific datasets
  required separating dataset-specific parsing from the core filesystem tree.
- FUSE and NFS support required thinking about stable filesystem semantics, not
  just metadata parsing.
- Many scientific datasets have different access patterns, licensing, size
  constraints, or account requirements, so the registry needed to represent
  datasets even when the full data could not be downloaded for a normal demo.
- Keeping the project demoable required small sample metadata tables while still
  supporting larger local metadata files outside the repository.

## Remaining Work

- Prepare a Nexus integration demo that shows the filesystem being used inside
  or alongside Nexus workflows.
- Expand the filesystem from metadata browsing toward deeper CyberShuttle
  workflow integration.
- Add more dataset-specific connectors beyond table-backed metadata.
- Use the stable filesystem artifact and Nexus demo as the basis for a future
  paper or technical writeup.
- Continue hardening release artifacts after review by tagging a stable
  `gsoc-2026-final` snapshot.
