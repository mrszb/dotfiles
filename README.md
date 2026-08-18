## Cross toolchains (~/x-tools, not managed by chezmoi)

- `gcc-arm-none-eabi-10.3-2021.10` — official ARM Developer tarball
  (GNU Arm Embedded Toolchain, last release before the "Arm GNU Toolchain" rename)
- `avr-gcc-11.1.0-x64-linux` — Zak Kemble's builds, blog.zakkemble.net/avr-gcc-builds/
  (avr-gcc 11.1.0, binutils 2.36.1, avr-libc SVN, gdb 10.2)

Both are x86_64 Linux prebuilts, relocatable, extracted into ~/x-tools.
Referenced by dot_bashrc.d/40-dev (PATH) and dot_conan2/profiles/* (compiler paths).
