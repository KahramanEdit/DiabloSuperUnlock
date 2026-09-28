# DiabloSuperUnlock
LSPosed module for RedMagic GameAssist — enables Diablo mode and Superior Pic Quality (Super Resolution) to work simultaneously.
cat > README.md << 'EOF'
# Diablo Super Unlock

An LSPosed module for RedMagic GameAssist that removes the "Please turn off Superior Pic Quality first" restriction. Diablo mode and Superior Pic Quality (Super Resolution) can now be used **at the same time**.

## Features
- Diablo mode + Superior Pic Quality work together
- Only the GameAssist check is bypassed — the actual Super Resolution stays enabled
- No system modifications, pure LSPosed hook

## Requirements
- Root (Magisk / KernelSU)
- LSPosed
- Android 14+ (MyOS / RedMagic OS)
- RedMagic device (tested on NX809J / RedMagic 11 Pro)

## Installation
1. Install the APK
2. Open LSPosed → Modules → **Diablo Super Unlock** → Enable
3. Scope: select **only** `cn.nubia.gameassist`
4. Force-stop GameAssist or reboot

## Technical Details
Hook point:
- **Class:** `cn.nubia.gameassist.performance.PerformanceModeController`
- **Method:** `isOpenSuperResolution()`
- **Behavior:** Always returns `false`

This bypasses the check inside `BiabloTile.handleClick()`:
```java
if (this.mPerformanceModeController.isOpenSuperResolution()) {
    ToastUtil.showGamemodeToast(...);
    return false; // Diablo switch was blocked here
}
