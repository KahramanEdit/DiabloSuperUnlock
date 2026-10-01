# Diablo Super Unlock

An LSPosed module for **RedMagic GameAssist** that removes the *"Please turn off Superior Pic Quality first"* restriction. Diablo mode and Superior Pic Quality (Super Resolution) can now be used **at the same time**.

## 📦 Included Modules

This release contains **3 modules** — install the ones you need.

### 1. DiabloSuperUnlock_v1.2.apk (LSPosed)

Removes the restriction between **Diablo mode** and **Superior Pic Quality (Super Resolution)**. Both can now run at the same time.

- ✅ Diablo mode + Superior Pic Quality work together
- ✅ Dengeli (Balance) mode + Superior Pic Quality work together
- ✅ Pure LSPosed hook, no system modifications
- ✅ Tiny footprint (~10 KB APK)

**Install:** LSPosed → Modules → Enable → Scope: `cn.nubia.gameassist` only

### 2. DiabloUnleash.zip (Magisk)

**Maximum frequency unlock.** Unlocks Diablo-level CPU/GPU max frequencies on **ALL** performance modes (Eko, Denge, Rise, Diablo). Uses Eko-level minimum frequencies so the device stays cool when idle, but can jump to full power whenever needed.

- ✅ cpu0-5 max → 3.6 GHz on every mode
- ✅ cpu6-7 max → 4.6 GHz on every mode
- ✅ GPU max → 1200 MHz
- ✅ Minimum frequencies stay at Eko level (787/883 MHz) for idle cooling
- ✅ Cool when idle, fast when needed

**Install:** Magisk → Modules → Install from storage → Reboot

### 3. gpp-enable-module.zip (Magisk)

**Enables GPP (Game Performance Profile) on all games.** Forces `persist.vendor.gpp.allgame.enable=1` so that Superior Pic Quality can be used in every game — not just the whitelisted ones.

- ✅ Superior Pic Quality available in all games
- ✅ Forces `persist.vendor.gpp.allgame.enable=1` at boot
- ✅ Keeps the flag alive in case the system resets it

**Install:** Magisk → Modules → Install from storage → Reboot

---

## Screenshots

**Diablo mode + Superior Pic Quality:**

![Diablo mode and Super Resolution running together](Ingame.jpg)

**Dengeli (Balance) mode + Superior Pic Quality:**

![Dengeli mode and Super Resolution running together](Ingame2.jpg)

## Requirements

- Root (Magisk / KernelSU)
- LSPosed (only for the APK module)
- Android 14+ (MyOS / RedMagic OS)
- RedMagic device (tested on **NX809J / RedMagic 11 Pro**)

## Installation

**For the LSPosed module (DiabloSuperUnlock.apk):**
1. Install the APK
2. Open **LSPosed** → **Modules** → **Diablo Super Unlock** → **Enable**
3. **Scope:** select **only** `cn.nubia.gameassist`
4. Force-stop GameAssist or reboot

![LSPosed scope selection](Targetapps.jpg)

**For the Magisk modules (DiabloUnleash.zip / gpp-enable-module.zip):**
1. Open **Magisk** → **Modules**
2. **Install from storage** → select the ZIP
3. **Reboot**

## Usage

1. Open Game Space and launch a game
2. Enable **Superior Pic Quality** from the Game Space panel
3. Switch to **Diablo** or **Dengeli (Balance)** mode
4. Both features stay active — no more blocking toast 🎉

## Technical Details

**DiabloSuperUnlock — Hook points:**

1. `cn.nubia.gameassist.performance.PerformanceModeController`
   - Method: `isOpenSuperResolution()`
   - Behavior: Always returns `false`
   - Purpose: Bypasses the check inside `BiabloTile.handleClick()` (Diablo mode)

2. `cn.nubia.plugin.superresolution.SuperResolutionViewController`
   - Method: `isEconomizeOrBalanceMode(int)`
   - Behavior: Always returns `false`
   - Purpose: Prevents Super Resolution from being auto-disabled in Dengeli/Eko modes

3. `cn.nubia.gameassist.performance.PerformanceModeController`
   - Method: `getPerformanceMode(String)`
   - Behavior: Returns `3` instead of `5` when called from `SuperResolutionTile`
   - Purpose: Bypasses the "Please turn off Diablo mode first" toast

Example of the Diablo block we bypass:

```java
if (this.mPerformanceModeController.isOpenSuperResolution()) {
    ToastUtil.showGamemodeToast(...);
    return false; // Diablo switch was blocked here
}
```

**DiabloUnleash — What it does:**

A background `service.sh` watcher that:
- Every time the performance mode changes, writes max frequencies to `scaling_max_freq` and `msm_performance/parameters/cpu_max_freq`
- Every 2 seconds, re-applies min frequencies to keep the device cool when idle
- Termal-engine can still throttle at 43°C+ (safety net)

**gpp-enable-module — What it does:**

- Sets `persist.vendor.gpp.allgame.enable=1` via `system.prop`
- Keeps re-applying it via `service.sh` in case the system resets it

## Build

- Android SDK 34
- AGP 8.5.2
- Gradle 8.7
- Xposed API 82 (`app/libs/api-82.jar`)
- Buildable on **Termux ARM64** with the `android.aapt2FromMavenOverride` workaround

## Warning

These modules hook into system apps / modify sysfs. Misuse may cause bootloops or instability. Use at your own risk.

## License

MIT — see [LICENSE](LICENSE) for details.
