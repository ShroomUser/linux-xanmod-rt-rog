# Configuration & Clang
Choose your correct CPU microarchitecture with `_microarchitecture=` (Find your CPU in choose-gcc-optimization.sh)
To enable building with clang use `_compiler=clang`

# VFIO not working without reboot after switching to integrated graphics
Some models have issues utilizing VFIO when switching from hybrid to integrated. This kernel includes an experimental patch to fix this,
to enable have `skippatcheck=1` in your kernel boot parameters.
