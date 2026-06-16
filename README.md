# girards-paradox (OCaml version-sweep fork)

[![OCaml version sweep](https://github.com/FranklinChen/girards-paradox/actions/workflows/ocaml-version-sweep.yml/badge.svg)](https://github.com/FranklinChen/girards-paradox/actions/workflows/ocaml-version-sweep.yml)

A fork of [lpw25/girards-paradox](https://github.com/lpw25/girards-paradox) by Leo White.
`girard.ml` encodes Girard's paradox in OCaml's module system: it is a closed, well-typed
inhabitant of the empty type, so the mere fact that it typechecks demonstrates that OCaml's
module system is logically inconsistent. All credit for `girard.ml` belongs to Leo White;
this fork adds only a CI workflow and this README.

## What this fork adds

A GitHub Actions matrix, [`.github/workflows/ocaml-version-sweep.yml`](.github/workflows/ocaml-version-sweep.yml),
that attempts to compile `girard.ml` under many OCaml versions (4.02 through 5.4) on x86-64
Ubuntu. For each version it records one of:

- **COMPILED** (with the wall-clock seconds `ocamlc` took),
- **did NOT finish** within the per-compile timeout (45 minutes),
- **compiler build FAILED** on the runner toolchain (the compiler itself would not build, which
  says nothing about `girard.ml`), or
- **compile ERROR** (a genuine type error).

A final job aggregates every row into one table in the run summary.

Motivation: on Apple Silicon (arm64) opam only offers OCaml 4.14 and newer, and even 4.14 and
the 5.x line do not finish the typecheck within 20 minutes there. This sweep exercises the
older x86-only compilers that Apple Silicon cannot build, and produces a public, reproducible
record of which OCaml versions can typecheck the paradox and roughly how long it takes.

## How to read the badge

The badge reports only that the **sweep workflow ran to completion**, not that `girard.ml`
compiled. The jobs deliberately never fail (each one records its outcome instead), so a green
badge does **not** mean the paradox compiled. The actual per-version verdicts live in the
**summary table of each run**, reachable via the Actions tab that the badge links to.

## Reproduce locally

```sh
opam switch create sweep ocaml-base-compiler.<version>
opam exec --switch sweep -- ocamlc -c girard.ml   # may take a very long time, or not finish
```
