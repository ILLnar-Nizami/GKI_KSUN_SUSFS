# How to Build Kernels with KernelSU Next 3.0 + SUSFS 2.0

## Quick Start

Your workflow is already configured to build kernels with **KernelSU Next 3.0** and **SUSFS 2.0**!

## Steps to Build

### 1. Go to GitHub Actions
Navigate to: `https://github.com/YOUR_USERNAME/GKI_KSUN_SUSFS/actions`

### 2. Select "Build Kernels" Workflow
Click on the "Build Kernels" workflow in the left sidebar

### 3. Click "Run workflow"
Click the "Run workflow" button (top right)

### 4. Configure Build Options

**Required Settings:**
- **Release Type**: Choose `Actions` (for testing) or `Release` (for publishing)
- **KernelSU Variant**: Select **`Next`** ← This is important!
- **KSU Commit**: Leave empty (uses default `dev` branch with Next 3.0)

**Select Kernels to Build:**
Check the boxes for the Android versions you want:
- ✅ Android 12 - 5.10
- ✅ Android 13 - 5.10
- ✅ Android 13 - 5.15
- ✅ Android 14 - 5.15
- ✅ Android 14 - 6.1 ← **This is the one that was failing**
- ✅ Android 15 - 6.6
- ✅ Android 16 - 6.12
- ✅ Custom Builds

### 5. Start the Build
Click the green "Run workflow" button at the bottom

## What You'll Get

After the build completes successfully, you'll find these artifacts:

### AnyKernel3 Flashable ZIPs
- Format: `6.1.145-android14-2025-09-Normal-Next-AnyKernel3.zip`
- Location: Actions tab → Your workflow run → Artifacts section
- Contains: Kernel with KernelSU Next 3.0 + SUSFS 2.0

### Build Variants
Each kernel version will have:
- **Normal**: Standard build
- **Bypass**: Module check bypass enabled

## Configuration Details

### KernelSU Next 3.0
- Source: `https://github.com/WildKernels/KernelSU-Next` (wild/dev branch)
- Patch: `Next-3.0.0-susfs-2.0.0-AIO.patch`

### SUSFS 2.0
- Version: `2.0.0`
- Features: Root hiding kernel patches
- Module: https://github.com/sidex15/ksu_module_susfs

### Kernel Configuration
```
CONFIG_KSU=y
CONFIG_KSU_KPROBES_HOOK=n
CONFIG_KSU_SUSFS=y
CONFIG_KSU_SUSFS_SUS_SU=n
```

## Flashing Instructions

1. Download the AnyKernel3 zip from Artifacts
2. Boot to recovery (TWRP/OrangeFox recommended)
3. Flash the zip
4. Reboot
5. Install KernelSU Next manager: https://github.com/KernelSU-Next/KernelSU-Next
6. Install SUSFS module: https://github.com/sidex15/ksu_module_susfs

## Troubleshooting

### Build Failed?
- Check the "Combined Reject Summary" step (now non-blocking)
- Download the `*-Rejects` artifact to see patch issues
- The build will still produce AnyKernel3 zips even with minor patch rejects

### Need Specific Commit?
In the "KSU Commit" field, enter a specific commit hash from:
- https://github.com/KernelSU-Next/KernelSU-Next/commits/next

### Want to Build Only One Kernel?
Uncheck all boxes except the one you want to build

## Advanced: Custom Builds

Edit `.github/workflows/kernel-custom.yml` to add your own kernel configurations.

## Notes

- ✅ SUSFS 2.0 is automatically applied for Next variant
- ✅ The android14-6.1-145 build error has been fixed
- ✅ Combined Reject Summary won't block your builds anymore
- ✅ All builds include BBG (Baseband-guard) protection
- ✅ Wireguard, BBR v1, and IPSet support included

## Support

- 📖 KernelSU Next: https://github.com/KernelSU-Next/KernelSU-Next
- 🛡️ SUSFS: https://gitlab.com/simonpunk/susfs4ksu
- 📦 SUSFS Module: https://github.com/sidex15/ksu_module_susfs

