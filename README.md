# 7-Zip on GitHub
7-Zip website: [7-zip.org](https://7-zip.org)

Fork of [ip7z/7zip](https://github.com/ip7z/7zip). The 7-Zip sources are unchanged. Everything added is under `.github/`: it builds the console binary `7zz` with tuned compiler flags and publishes it as a nightly release.

## Differences from upstream

| | Upstream | This fork |
|---|---|---|
| Builds | macOS only, manual | Windows x64, Linux x64, macOS arm64 |
| Trigger | manual | push to `main`, daily 03:00 UTC, manual with `force` |
| Skip unchanged | no | skips when only `DOC/` or `*.md` changed |
| Flags | makefile defaults | MSVC `/arch:AVX2 /GL /Gw /Ob3 /O2` + `/LTCG`; GCC `-march=x86-64-v3 -O3 -flto` `--gc-sections`; clang `-mcpu=apple-m1 -O3 -flto=thin` |
| Test | `7zz b` benchmark | same benchmark on every platform |
| Failed build | log artifact, macOS only | `build.log` printed and uploaded per platform |
| Output | artifacts, 7 days | rolling `nightly` release with zip/tar.gz and SHA-256/512 |

## Binaries

The `nightly` release is deleted and recreated on every build:

- `7zz-windows-x64-v3-<sha>-<date>.zip`
- `7zz-linux-x64-v3-<sha>-<date>.tar.gz`
- `7zz-macos-arm64-<sha>-<date>.tar.gz`

x86-64 builds need Haswell (2013) or Zen (2017); arm64 needs macOS 11.

## Syncing with upstream

```sh
git fetch https://github.com/ip7z/7zip.git main
git merge FETCH_HEAD
git push origin main
```
