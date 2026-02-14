## fetch
### git
```sh
revision 943ac082804413a2f11d7330082095aa94def6c2 # muon's wayland module is broken TODO should be easy fix
```
1. https://gitlab.freedesktop.org/mesa/mesa
2. https://github.com/chaotic-cx/mesa-mirror
## build
### meson
```sh
gallium-drivers=radeonsi,zink
vulkan-drivers=amd
amd-use-llvm=false
glx=disabled
platforms=wayland
```
