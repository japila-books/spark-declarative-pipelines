# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## About This Repository

This is "The Internals of Spark Declarative Pipelines" — an online reference book by Jacek Laskowski documenting the internal architecture of Apache Spark Declarative Pipelines (SDP) in Spark 4.1.1. It is a **documentation-only** project (no application code).

Published at: https://books.japila.pl/spark-declarative-pipelines

## Commands

```bash
# Install dependencies
pip install -r requirements.txt

# Local development server (live reload)
mkdocs serve

# Deploy to GitHub Pages
mkdocs gh-deploy --force
```

Deployment also happens automatically via GitHub Actions on every push to `main`.

## Architecture

The documentation is built with **MkDocs + Material theme**. Key configuration is in `mkdocs.yml`. Navigation is managed by the `mkdocs-awesome-nav` plugin via `docs/.nav.yml` — edit that file to add or reorder pages.

All content lives under `docs/`. The structure:

- `docs/*.md` — top-level concepts: overview, Python API, SQL API, configuration properties
- `docs/demo/` — hands-on tutorials (CLI, Python API, Scala API, Delta Lake)
- `docs/logical-operators/` — SQL logical operator reference
- Individual component files (`DataflowGraph.md`, `PipelinesHandler.md`, etc.) document internal Spark classes

Architecture diagrams are maintained as OmniGraffle sources in `graffles/` and exported as PNGs to `docs/images/`.

## Content Conventions

- Each component doc covers: the class/object being described, its role in the system, key methods, and cross-links to related components
- Code examples use MkDocs content tabs (`=== "Python"` / `=== "Scala"` / `=== "SQL"`) for multi-language samples
- Use admonition blocks (`!!! note`, `!!! tip`, etc.) for callouts
- Spark source references link to the Spark GitHub at tag `v4.1.1`
