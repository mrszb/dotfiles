# dotfiles

Shell, git, and toolchain configuration managed with [chezmoi](https://chezmoi.io).

## Machines

| Host | OS | Notes |
|---|---|---|
| (desktop) | Fedora | RTX 2080 Ti, cross-development, CUDA |
| Pengwin | WSL2 (Debian) | RTL-SDR via usbipd |
| `pinas` | Raspberry Pi OS Bookworm (aarch64) | Pi 4 |
| `pi5` | Raspberry Pi OS Bookworm (aarch64) | Pi 5 |

Bootstrap a new machine with:

```sh
chezmoi init --apply mrszb
```

## Layout

- `dot_bashrc`, `dot_bashrc.d/` — shell config, split by numeric prefix and
  sourced in name order. `30-platform` exports `IS_WSL`, which later files
  depend on, so the ordering is load-bearing.
- `dot_gitconfig.tmpl` — templated per platform.
- `dot_conan2/profiles/` — Conan 2 cross-compilation profiles (see below).
- `.chezmoiignore` — per-platform exclusions.

## Cross toolchains (~/x-tools, not managed by chezmoi)

- `gcc-arm-none-eabi-10.3-2021.10` — official ARM Developer tarball
  (GNU Arm Embedded Toolchain, last release before the "Arm GNU Toolchain" rename)
- `avr-gcc-11.1.0-x64-linux` — Zak Kemble's builds, blog.zakkemble.net/avr-gcc-builds/
  (avr-gcc 11.1.0, binutils 2.36.1, avr-libc SVN, gdb 10.2)

Both are x86_64 Linux prebuilts, relocatable, extracted into `~/x-tools`.
Referenced by `dot_bashrc.d/40-dev` (PATH) and `dot_conan2/profiles/*` (compiler paths).
Fastest way to replicate on another x86_64 box is to tar the directory across
rather than re-download — the Conan profiles pin `compiler.version`, so a
different build of the same version would silently fragment the package cache.

Conan profiles are composed: `arm-none-eabi` and `avr` carry the toolchain,
per-target profiles (`cortex-m4`, `atmega328p`) `include()` them and add MCU
flags. Use as `conan install . -pr:b default -pr:h cortex-m4`. The `default`
profile is generated locally with `conan profile detect` and deliberately not
tracked, since it is machine-specific.

## Gotchas

- **`.chezmoiignore` is implicitly a template — do not add a `.tmpl` suffix.**
  A `.chezmoiignore.tmpl` is silently never read. Same applies to
  `.chezmoiremove` and `.chezmoiexternal`.
- **The Conan profiles use Jinja `{{ }}` for their own path expansion.** They
  must stay non-templated in chezmoi, or chezmoi's Go templating will consume
  the expressions at apply time and produce profiles with empty paths.
- **Do not pin `UV_PYTHON`.** An earlier `export UV_PYTHON=$(command -v python3)`
  in `40-dev` broke every project with a `requires-python` narrower than the
  distro's current Python. uv's defaults (managed interpreters, automatic
  downloads) give the same version across all machines.
- `conan` is installed per-machine with `uv tool install conan`; uv tools do not
  travel through chezmoi.
  

## Cross toolchains (~/x-tools, not managed by chezmoi)

- `gcc-arm-none-eabi-10.3-2021.10` — official ARM Developer tarball
  (GNU Arm Embedded Toolchain, last release before the "Arm GNU Toolchain" rename)
- `avr-gcc-11.1.0-x64-linux` — Zak Kemble's builds, blog.zakkemble.net/avr-gcc-builds/
  (avr-gcc 11.1.0, binutils 2.36.1, avr-libc SVN, gdb 10.2)

Both are x86_64 Linux prebuilts, relocatable, extracted into ~/x-tools.
Referenced by dot_bashrc.d/40-dev (PATH) and dot_conan2/profiles/* (compiler paths).

