# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A fork of `lpw25/girards-paradox` (Leo White's encoding of Girard's paradox in OCaml's module system: `girard.ml` is a closed, well-typed inhabitant of the empty type, so the fact that it typechecks demonstrates the module system is logically inconsistent). **The only thing this fork adds is CI.** Do not modify `girard.ml`; it is upstream's, and the value-add here is the workflow and README. For the proof's internals, see the companion repo `girards-paradox` and its CLAUDE.md.

## The workflow

`.github/workflows/ocaml-version-sweep.yml` is a `fail-fast: false` matrix over OCaml 4.02 through 5.4. Each job builds that compiler via opam, runs `timeout 45m ocamlc -c girard.ml`, and records one row: COMPILED (with seconds), did-not-finish, compiler-build-FAILED (the bootstrap failed on the runner toolchain, which says nothing about `girard.ml`), or compile-ERROR. A final job aggregates a table into the run summary. Jobs deliberately never hard-fail, so the badge means "the sweep ran", not "the paradox compiled".

Conventions to preserve when editing the workflow:
- `on.push.paths-ignore: ["**.md"]` keeps README edits from triggering the expensive sweep; for commits that touch the workflow itself but should not run it, put `[skip ci]` in the commit message.
- Action tags are pinned to the current latest (`actions/checkout@v6.0.3`, `actions/upload-artifact@v7.0.1`, `actions/download-artifact@v8.0.1`); re-check latest before editing.
- The single `${{ matrix.ocaml }}` used inside a `run:` step is passed via `env:` (injection-safety hygiene; the matrix values are trusted regardless).

## Key finding

OCaml 4.11.2 typechecks `girard.ml` in under a second; 4.14.x and the 5.x line do not finish in 45 minutes; 4.02-4.08 fail to build on the runner's 2024 toolchain. So the pathological slowdown is a regression in OCaml 4.12 or 4.13. The live per-version table is in each run's summary (and mirrored into `README.md`).

## Companion repos

- `FranklinChen/girards-paradox-scala`: the analogous Hurkens encoding attempted in Scala 3 (blocked at Scala's stable-path rule).
- Local `~/girards-paradox`: the OCaml source plus research notes and a local opam version-sweep script.
