# wine-affinity

Wine patches for running Affinity v3 (Designer, Photo, Publisher) on Linux.

These patches apply cleanly on top of Wine upstream commit [`53d513e6`](https://gitlab.winehq.org/wine/wine/-/commit/53d513e626205d5506b7e959bf73b22fd1c17908) (Wine 11.5).

## Applying

```bash
git clone https://gitlab.winehq.org/wine/wine.git
cd wine
git checkout 53d513e626205d5506b7e959bf73b22fd1c17908
git am /path/to/wine-affinity/*.patch
```

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

## Building the patched DLLs

After applying, configure and build only what you need:

```bash
./configure --enable-win64
make -j$(nproc) dlls/d2d1 dlls/dxcore dlls/opencl
```

The built DLLs can then be copied into any Wine prefix.

## Credits

- [ElementalWarrior (James McDonnell)](https://gitlab.winehq.org/ElementalWarrior/wine) — wintypes hack, dxcore patches, original opencl `cl_khr_d3d10_sharing` implementation
