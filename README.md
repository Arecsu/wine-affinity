# wine-affinity

Wine patches for running Affinity v3 (Designer, Photo, Publisher) on Linux.

These patches are meant to be applied on top of [Wine upstream](https://gitlab.winehq.org/wine/wine) (tested against Wine 11.5).

## Patches

| Patch | Description |
|-------|-------------|
| 0001 | wintypes: Hack in some calls to RoResolveNamespace |
| 0002 | d2d1: Stub `Widen` with empty geometry to prevent caller freeze |
| 0003 | opencl: Implement `cl_khr_d3d10_sharing` extension for DXVK compatibility |
| 0004 | opencl: Use substring matching for OpenCL/DXGI device name comparison |
| 0005 | d2d1: Prevent runaway bezier splitting and recursion in geometry processing |

## What these fix

- **GPU OpenCL acceleration** works through DXVK's DXGI layer (patches 0003, 0004)
- **Application freezes** on complex vector paths due to unbounded bezier splitting (patches 0002, 0005)
- **DXCore adapter enumeration** with correct PCI ID reporting (patch 0003)

## Applying

```bash
git clone https://gitlab.winehq.org/wine/wine.git
cd wine
git am /path/to/wine-affinity/*.patch
```

## Building the patched DLLs

After applying, configure and build only what you need:

```bash
./configure --enable-win64
make -j$(nproc) dlls/d2d1 dlls/dxcore dlls/opencl
```

The built DLLs can then be copied into any Wine prefix.

## Credits

- [ElementalWarrior](https://github.com/AlfCraft07/ElementalWarrior-wine) / [__avg__](https://gitlab.winehq.org) — original opencl `cl_khr_d3d10_sharing` implementation and dxcore patches
- [James McDonnell](https://github.com/AlfCraft07/ElementalWarrior-wine) — wintypes RoResolveNamespace hack
