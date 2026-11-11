# ValkyOS — Android source manifests

ValkyOS is an experimental privacy- and security-focused Android ROM based on **LineageOS 24.0 (Android 17)**, adding features ported from **GrapheneOS**. The first target is the **Motorola Edge 50 Pro (`eqe`)**.

This repository is the **eqe's personal manifest**: it pins the exact ValkyOS forks and device sources used for the Motorola Edge 50 Pro. The general ValkyOS manifest is [`ValkyOS/android`](https://github.com/ValkyOS/android) (branch `17`); device sources are added with a local manifest such as [`dvy-jp/local_manifests`](https://github.com/dvy-jp/local_manifests).

## Status (V50, November 2026)

Built and installed on the device (`userdebug`, signed with the builder's own keys), with the LineageOS **2026-11-01** security patch:

- **Sandboxed Google Play (GmsCompat)**, ported from GrapheneOS: Play Store and Play services run as **regular, unprivileged apps**. Sign-in, app installs and updates, push notifications, Google sign-in inside apps (Dynamite modules) and location rerouted to the OS work. GmsCompat stays enabled across reboots.
- **Camera**: Motorola camera HAL and Moto Camera work, including portrait (blur) photos.
- **Automatic security reboot**: after eight continuous hours locked the device reboots, so data returns to its encrypted state. Toggle in Settings > Security & privacy > More security.
- **Contact Scopes**, ported from GrapheneOS: instead of granting the Contacts permission, you can choose which contacts, groups, numbers or emails an app may see. Option in the app's Contacts permission screen.
- **Storage Scopes**, ported from GrapheneOS: instead of granting storage or media permissions, you can choose which folders and files an app may access; the app still sees the files it creates. Option in the app's Photos and videos / Files permission screen.
- **Sensors permission**, ported from GrapheneOS: a new per-app "Sensors" permission covers sensors that had no permission (accelerometer, gyroscope, compass, barometer...). New apps get it by default and it can be denied per app; apps installed before the update keep access.
- **Vanadium**, the GrapheneOS browser and WebView, replaces the LineageOS browser and WebView (always the latest release from branch `17`).
- **Per-user options**, ported from GrapheneOS: in Settings > System > Users, each user gets "App installs and updates" (enabled, first party sources only, or disabled), "Install available apps" and "Allow running in background".
- **Internet connectivity checks**, ported from GrapheneOS: captive portal detection and the DNS-over-TLS/HTTPS probes use GrapheneOS servers by default instead of Google; Settings > Network & internet > Internet connectivity checks lets you pick GrapheneOS, standard (Google) or off. Vanadium follows the same setting.
- **Play Integrity API notification and per-app blocking**, ported from GrapheneOS: when an app uses the Play Integrity API a notification is shown, and App info > Play Integrity API lets you block it for that app (it then gets the "API not available" error). The option appears once the app has used the API. Nothing is spoofed.
- **ValkyOS branding**: ValkyOS name in About phone and Settings, logo on the Settings homepage and in recovery, and seven ValkyOS wallpapers in the wallpaper picker (replacing the LineageOS ones).`n- **No LineageOS leftovers in About phone**: the LineageOS legal entry and the contributors screen are removed.
- **No LineageOS statistics**: the anonymous statistics option is hidden (Trust, setup wizard and Settings search) and the upload is disabled, so nothing is sent to stats.lineageos.org.
- **System updates without LineageOS**: the Updater has no update server for now (the check shows an error) and shows ValkyOS names, logo and version.
- **Build output**: the update package is named `ValkyOS-<version>.zip` (no `lineage-` prefix and no build type).
- **USB protection**: LineageOS already provides it (Settings > Privacy > Trust > Restrict USB); it works on `eqe`.
- **Per-app network access**: LineageOS already provides it (App info > Mobile data & Wi-Fi > Network access).
- **GrapheneOS App Store** is included (always the latest release from branch `17`) to install sandboxed Google Play and GrapheneOS apps.

Known limitations:

- The bootloader must stay unlocked (Motorola does not support a custom root of trust), so verified boot reports **orange**.
- **Play Integrity is not promised**; apps that require it may refuse to run.
- Sandboxed Google Play is protected like on GrapheneOS: Play services and Play Store are installed and updated through the GrapheneOS App Store (or `adb`), not by arbitrary installers.
- Postponed in GmsCompat: Google Camera client overrides and Android Auto support.

## Privacy

ValkyOS itself collects no data and runs no servers of its own.

- The LineageOS anonymous statistics are disabled and hidden; nothing is sent to stats.lineageos.org.
- System updates have no update server yet, so the Updater makes no requests.
- Internet connectivity checks use GrapheneOS servers by default (Settings > Network & internet lets you choose GrapheneOS, standard Google servers, or off).
- The GrapheneOS App Store and Vanadium check GrapheneOS servers for updates.
- Network time still uses the standard Android server (time.android.com).
- Google Play is optional and sandboxed; if you install it, Google's own policies apply to it.

## Build it yourself

Install the Android `repo` utility, Git and **Git LFS** according to the LineageOS build requirements. Then, in an **empty workspace**:

```bash
mkdir -p ~/valkyos && cd ~/valkyos
repo init -u https://github.com/ValkyOS/android -b 17 --git-lfs
git clone https://github.com/dvy-jp/local_manifests -b valkyos-17 .repo/local_manifests
repo sync -c -j8
```

That is the general ValkyOS flow. This repository (`android_manifest`) stays as the eqe's
personal manifest, pinned to the exact revisions used for the Edge 50 Pro.

No setup script is needed after syncing: every ValkyOS change lives in the repositories pinned by the manifest (see [`vendor/valkyos/build/README.md`](https://github.com/ValkyOS/android_vendor_valkyos/blob/main/build/README.md)).

### Signing keys

Use **your own** release keys. Generate them as described in the LineageOS signing documentation and place them in `vendor/lineage-priv/keys` before building. ValkyOS never publishes keys; without your own keys the build uses public test keys.

### Compile

```bash
source build/envsetup.sh
brunch eqe
```

The flashable ZIP is written to `out/target/product/eqe/`.

## Persisting work across rented servers

1. **Commit and push every change** (framework patches, GrapheneOS ports, device changes) to the repository that contains it, then update the pinned revisions in this manifest. **This manifest does not back up uncommitted files.**
2. Record the exact revisions of a successful build with `repo manifest -r -o valkyos-revisions.xml` if you need to reproduce it later.
3. Keep signing keys, credentials and other secrets **out of public repositories**, with encrypted backups outside the build server.
4. Keep ROM ZIPs separately. The `out/` directory does not need to be preserved.

## Attribution

GmsCompat and the other ported features are the work of **GrapheneOS** (https://grapheneos.org). The camera compatibility approach follows the LineageOS `hardware/lineage/compat` shims. LineageOS, GrapheneOS and the original device-source maintainers retain their own authorship, trademarks and licenses. ValkyOS is an independent experimental project, not an official build of either project.
