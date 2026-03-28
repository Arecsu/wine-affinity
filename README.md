# wine-affinity

Wine patches for running Affinity on Linux.

These patches apply cleanly on top of Wine upstream commit [`53d513e6`](https://gitlab.winehq.org/wine/wine/-/commit/53d513e626205d5506b7e959bf73b22fd1c17908) (Wine 11.5).

## Patches

| Patch | Description |
|-------|-------------|
| 0001 | wintypes: Hack in some calls to RoResolveNamespace |
| 0002 | d2d1: Stub `Widen` with empty geometry to prevent caller freeze |
| 0003 | opencl: Implement `cl_khr_d3d10_sharing` extension for DXVK compatibility |
| 0004 | opencl: Use substring matching for OpenCL/DXGI device name comparison |
| 0005 | d2d1: Prevent runaway bezier splitting and recursion in geometry processing |
| 0006 | comdlg32: Use XDG Desktop Portal for native file dialogs (Wine MR !10060) |
| 0007 | comdlg32: Fix portal dialog compatibility and responsiveness for Affinity |

## What these fix

- **GPU OpenCL acceleration** works through DXVK's DXGI layer (patches 0003, 0004)
- **Application freezes** on complex vector paths due to unbounded bezier splitting (patches 0002, 0005)
- **DXCore adapter enumeration** with correct PCI ID reporting (patch 0003)
- **Native file dialogs** via XDG Desktop Portal — requires `libdbus-1` and `xdg-desktop-portal` (patches 0006, 0007)

## Pre-built DLLs

Pre-built DLLs are available in [Releases](https://github.com/Arecsu/wine-affinity/releases). To use them, copy the files into your Wine prefix:

```bash
# Replace these with your actual Wine prefix path
PREFIX=~/.wine

cp d2d1.dll dxcore.dll opencl.dll "$PREFIX/drive_c/windows/system32/"
cp opencl.so "$PREFIX/drive_c/windows/system32/"
```

Then set the DLL overrides so Wine uses these instead of the builtins:

```bash
WINEPREFIX="$PREFIX" wine reg add 'HKCU\Software\Wine\DllOverrides' /v d2d1 /d native /f
WINEPREFIX="$PREFIX" wine reg add 'HKCU\Software\Wine\DllOverrides' /v dxcore /d native /f
WINEPREFIX="$PREFIX" wine reg add 'HKCU\Software\Wine\DllOverrides' /v opencl /d native /f
```

## Building from source

Apply the patches and build:

```bash
git clone https://gitlab.winehq.org/wine/wine.git
cd wine
git checkout 53d513e626205d5506b7e959bf73b22fd1c17908
git am /path/to/wine-affinity/*.patch
./configure --enable-win64
make -j$(nproc) dlls/d2d1 dlls/dxcore dlls/opencl dlls/comdlg32
```

The built DLLs will be in `dlls/d2d1/`, `dlls/dxcore/`, `dlls/opencl/`, and `dlls/comdlg32/`.

> Note: patch 0006 requires `libdbus-1-dev` at configure time for `comdlg32.so` to be built.

## Usage

These patches are used by [run-affinity](https://github.com/Arecsu/run-affinity) — a portable, ready-to-use installer for running Affinity on Linux.

## Credits

- [ElementalWarrior (James McDonnell)](https://gitlab.winehq.org/ElementalWarrior/wine) — wintypes hack, dxcore patches, original opencl `cl_khr_d3d10_sharing` implementation
- [Alexander Wilms](https://gitlab.winehq.org/wine/wine/-/merge_requests/10060) — XDG Desktop Portal file dialog integration (Wine MR !10060)
