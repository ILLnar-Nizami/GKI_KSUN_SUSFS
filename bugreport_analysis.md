# KernelSU Next Bugreport Analysis

## Device Information
- **Device**: Pixel 6 (oriole)
- **Android Version**: 16 (CP11.251114.006)
- **Kernel**: 6.1.145-android14-11-Wild-Next
- **KernelSU**: 32900 (v3.0.0-49-gb606f0ab-spoofed)
- **SELinux**: Enforcing
- **Rooted**: Yes (KernelSU Next + SUSFS)

## Major Issues Identified

### 1. Regulator Failures
**Error**: `cs35l41 spi13.0: Failed to request core supplies: -517`
**Error**: `cs35l41 spi13.1: Failed to request core supplies: -517`
**Error**: `exynos_pd_hsi0 sub-pd-hsi0: get vdd_hsi regulator failed: -517`

**Impact**: Audio codecs (cs35l41) fail to initialize, potentially causing audio issues.
**Cause**: Missing regulator definitions in device tree for audio codecs.
**Fix**: Add regulator nodes in DT for `vdd-audio` or similar supplies.

### 2. Thermal Zone Configuration Issues
**Error**: `thermal thermal_zone17: Failed to set trips: -11` (and similar for zones 18,19,21,22,24,25)

**Impact**: Thermal management may not work properly, risking overheating.
**Cause**: Missing trip point configurations in device tree.
**Fix**: Add trip points in thermal DT nodes, e.g.:
```
trips {
  trip0 {
    temperature = <50000>;
    hysteresis = <2000>;
    type = "passive";
  };
};
```

### 3. CPU Frequency Control Issues
**Error**: `cpufreq: __target_index: Failed to change cpu frequency: -22`

**Impact**: CPU frequency scaling may fail.
**Cause**: Possible governor issue or invalid frequency table.
**Fix**: Check cpufreq governor configuration or frequency table in DT.

### 4. Devfreq (Device Frequency) Failures
**Error**: `failed to update frequency from PM QoS (-517)` (multiple devfreq instances)

**Impact**: Dynamic frequency scaling for devices like camera, display may not work.
**Cause**: Missing power domains or regulators in DT.
**Fix**: Add power domain references in devfreq DT nodes.

### 5. I2C Bus Issues
**Error**: `i2c i2c-1: Failed to register i2c client at 0x24 (-16)`

**Impact**: OIS (Optical Image Stabilization) device fails to probe.
**Cause**: I2C bus conflict or incorrect DT configuration.
**Fix**: Check I2C bus configuration and device addresses in DT.

### 6. Audio DSP Issues
**Error**: `cs35l41 spi13.1: DSP1: Failed to parse legacy: -19`
**Error**: `cs35l41 spi13.0: DSP1: Failed to parse legacy: -19`

**Impact**: Audio DSP functionality may be limited.
**Cause**: Firmware or configuration issue.
**Fix**: Update DSP firmware or configuration.

### 7. Health Daemon Issues
**Error**: `healthd: Failed to lookup glob /sys/devices/platform/*gcdd/system_dev_stat: -3`

**Impact**: Battery health monitoring may be incomplete.
**Cause**: Missing sysfs entries or device.
**Fix**: Check if gcdd device is present and configured.

### 8. SELinux Denials
**Error**: Multiple `avc: denied` for zygisksu, susfs4ksu modules.

**Impact**: Root modules may not function properly.
**Cause**: Expected in rooted device, but may indicate incomplete policy.
**Fix**: Add SELinux rules for root modules if needed.

### 9. Cgroup Process Management Issues
**Error**: `libprocessgroup: Failed to write 1 to /sys/fs/cgroup/.../cgroup.kill: No such file or directory`

**Impact**: Process killing via cgroup may fail.
**Cause**: Cgroup v2 configuration issue.
**Fix**: Check cgroup setup.

### 10. Service Manager Issues
**Error**: `servicemanager: Tried to unregister apexservice, but there are clients.`

**Impact**: Service management issues.
**Cause**: Clients still using service during unregister.
**Fix**: Ensure proper service lifecycle management.

## Recommendations

1. **Device Tree Updates**: The primary issues appear to be missing or incorrect device tree configurations for Pixel 6 (oriole). Ensure DT includes:
   - Regulator supplies for audio codecs
   - Thermal trip points
   - Power domains for devfreq
   - Correct I2C configurations

2. **Kernel Patches**: Verify that all necessary patches for Pixel 6 are applied, especially for audio, thermal, and power management.

3. **Testing**: Test audio functionality, thermal throttling, camera performance, and battery monitoring.

4. **SELinux Policy**: If root functionality is affected, update SELinux policy for KernelSU and SUSFS.

## Notes
- The kernel is based on Android 14 GKI (6.1.145) but running on Android 16, which may cause compatibility issues.
- SUSFS is integrated for root hiding.
- Device appears to boot successfully despite errors.