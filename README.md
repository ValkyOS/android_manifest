# ValkyOS — Android source manifests

ValkyOS is an experimental privacy- and security-focused Android ROM based on **LineageOS 24.0 (Android 17)**, adding features ported from **GrapheneOS**. The first target is the **Motorola Edge 50 Pro (`eqe`)**.

This repository stores **only the local manifest** that points to the ValkyOS forks and the exact device sources used, plus build and recovery instructions. Upstream LineageOS code stays in its original repositories.

## Status (V19, November 2026)

Built and tested on the device (`userdebug`, signed with the builder's own keys):

- **Sandboxed Google Play (GmsCompat)**, ported from GrapheneOS: Play Store and Play services run as **regular, unprivileged apps**. Sign-in, app installs and updates, push notifications, Google sign-in inside apps (Dynamite modules) and location rerouted to the OS work. GmsCompat stays enabled across reboots.
- **Camera**: Motorola camera HAL and Moto Camera work, including portrait (blur) photos.

Known limitations:

- The bootloader must stay unlocked (Motorola does not support a custom root of trust), so verified boot reports **orange**.
- **Play Integrity is not promised**; apps that require it may refuse to run.
- Still missing from GmsCompat: `AppsFilterImpl`, `PackageInstallerSession` and three parts of the GmsCompat app (Play Integrity handling, Google Camera client overrides, Android Auto carrier config).
- Not yet ported: Contact Scopes, Storage Scopes, per-app sensor control, USB protection, auto-reboot.

## Build it yourself

Install the Android `repo` utility, Git and **Git LFS** according to the LineageOS build requirements. Then, in an **empty workspace**:

```bash
mkdir -p ~/valkyos && cd ~/valkyos
repo init -u https://github.com/LineageOS/android.git -b lineage-24.0 --git-lfs
git clone https://github.com/ValkyOS/android_manifest.git .repo/local_manifests
repo sync -c -j8
bash vendor/valkyos/build/setup-valkyos.sh "$PWD"
```

[`valkyos-eqe.xml`](valkyos-eqe.xml) is a **local manifest overlay**, so the `repo init` URL must be the upstream **LineageOS/android** repository, **not this repository**.

`setup-valkyos.sh` reproduces the compatibility changes needed by this Android 17 base (framework ABI bridges, the camera fixes and the OpenEUICC dependencies). It verifies every input by SHA-256 and can be run again safely. See [`vendor/valkyos/build/README.md`](https://github.com/ValkyOS/android_vendor_valkyos/blob/main/build/README.md).

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
