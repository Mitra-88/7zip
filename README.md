# 7-Zip on GitHub
7-Zip website: [7-zip.org](https://7-zip.org)

## About this fork

Personal fork of [ip7z/7zip](https://github.com/ip7z/7zip). The 7-Zip source tree is
kept identical to upstream — all changes live in `.github/` — and a CI pipeline builds
the console binary (`7zz`) with aggressive optimization flags and publishes it as one
rolling nightly release.

### Comparison with upstream

| | Upstream (`ip7z/7zip`) | This fork (`Mitra-88/7zip`) |
|---|---|---|
| 7-Zip sources | reference | unchanged, merged from upstream |
| Windows x64 build | — | MSVC 2026, `/arch:AVX2 /GL /Gw /Ob3 /O2` + `/LTCG` |
| Linux x64 build | — | GCC 15, `-march=x86-64-v3 -O3 -flto=auto` + `--gc-sections` |
| macOS arm64 build | manual workflow | Apple clang, `-mcpu=apple-m1 -O3 -flto=thin` |
| macOS x64 build | manual workflow | upstream's `macos-build.yml`, kept as-is |
| When it builds | manual only | push to `main`, daily 03:00 UTC, manual (with `force`) |
| Rebuild gate | — | skipped when only `DOC/` or `*.md` changed since the last nightly |
| Output | workflow artifacts, 7-day retention | rolling `nightly` GitHub Release: zip + tar.gz, SHA-256/SHA-512, changelog |
| Test before publishing | `7zz b -md22 -mtic=31 -mmt2 -bt` (macOS only) | same round-trip benchmark on every platform |
| Failed build diagnostics | `build.log` artifact on macOS | `build.log` printed and uploaded for every platform |
| Scope | 7-Zip project | `7zz` console binary only; no GUI, no file manager |

### The nightly release

One release, tagged `nightly`, is deleted and recreated on each build, so the tag always
marks the newest binaries and their source commit:

- `7zz-windows-x64-v3-<sha>-<date>.zip`
- `7zz-linux-x64-v3-<sha>-<date>.tar.gz`
- `7zz-macos-arm64-<sha>-<date>.tar.gz`

The x86-64 binaries require a Haswell (2013) / Zen (2017) class CPU because of the
`x86-64-v3` level; the arm64 binary requires macOS 11 or later.

### Keeping in sync with upstream

`main` is a merge of upstream `main` plus the CI commits, so upstream fixes arrive with a
plain merge. Every sync keeps those CI commits — the workflow files are the fork's own
and are never replaced by upstream's:

```sh
git fetch https://github.com/ip7z/7zip.git main
git merge FETCH_HEAD
git push origin main
```
