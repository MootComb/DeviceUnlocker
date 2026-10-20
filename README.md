# DeviceUnlocker

An Xposed module for **Vector** that allows you to programmatically unlock the device screen (dismiss the keyguard) via the broadcast intent `com.mootcomb.unlocker.UNLOCK`.

---

## 📥 Installation

### Option 1 — Prebuilt APK

1. Download the latest APK from the [**Releases**](https://github.com/MootComb/DeviceUnlocker/releases) page.
2. Install the APK on your device.
3. Open **Vector** → **Modules** → enable **DeviceUnlocker**.
4. In the module settings, select the scope: **System UI** (`com.android.systemui`).
5. Restart SystemUI:
   ```bash
   su -c "killall com.android.systemui"
   ```
   or reboot the device.

### Option 2 — Build from source

```bash
git clone https://github.com/MootComb/DeviceUnlocker.git
cd DeviceUnlocker
./gradlew assembleRelease
```

The APK will appear in `build/outputs/apk/release/`. Then follow the same steps 2–5.

---

## ⚙️ How it works

The module runs in the `com.android.systemui` process and:

1. **Hooks `Application.attach(Context)`** to obtain the SystemUI `Context` and register a `BroadcastReceiver`.
2. **Hooks `KeyguardViewMediator` methods** (`start`, `onSystemReady`, `hideLocked`, `dismiss`, `setShowingLocked`, `doKeyguardLocked`, `doKeyguardTimeout`, `onStartedWakingUp`, `onStartedGoingToSleep`, `onFinishedWakingUp`) to capture the `KeyguardViewMediator` instance (via `chain.getThisObject()`).
3. Finds the unlock method: first `hideLocked()`, then `dismiss()`.
4. Upon receiving the **`com.mootcomb.unlocker.UNLOCK`** broadcast — invokes the found method on the saved instance on the main thread.

If the instance/method is not ready yet — the module retries after 2 seconds.

> ⚠️ For the module to work, it must be activated in **Vector** **and applied to `com.android.systemui`**. After activation — restart SystemUI or the device.

---

## 📋 Requirements

- Android with **Vector** — [github.com/byemaxx/Vector-SR](https://github.com/byemaxx/Vector-SR)
- **API 102+** support (`libxposed` API)
- Module activated in Vector with the scope **`com.android.systemui`**
- Root (Magisk / KernelSU / APatch)

---

## 🔓 Usage

Send the broadcast:

```bash
adb shell am broadcast -a com.mootcomb.unlocker.UNLOCK
```

Via shizuku:
```bash
am broadcast -a com.mootcomb.unlocker.UNLOCK
```
---

## 🛠 Building from source

**Requirements:** JDK 17+, Android SDK, Gradle.

```bash
git clone https://github.com/MootComb/DeviceUnlocker.git
cd DeviceUnlocker
./gradlew assembleRelease
```

The APK will appear in `build/outputs/apk/release/`.

### Signing the release build

Create `local.properties` (or use environment variables):

```properties
STORE_FILE=release.jks
STORE_PASSWORD=your_store_password
KEY_ALIAS=your_key_alias
KEY_PASSWORD=your_key_password
```

And configure `signingConfigs` in `build.gradle`.

---

## 🐛 Debugging

Module logs are disabled by default. To enable them — in `Main.java` set:

```java
private static final boolean DEBUG = true;
```

Then check logcat:

```bash
adb logcat -s DeviceUnlocker
```

---

## 🔗 Useful links

- [Vector-SR](https://github.com/byemaxx/Vector-SR) — xposed framework

---

## 📜 License

See [LICENSE](LICENSE).
