# ValkyOS — Android source manifests

ValkyOS is an experimental privacy- and security-focused Android ROM project based on **LineageOS 24.0 (Android 17)**. The first target is **Motorola Edge 50 Pro (`eqe`)**.

This repository stores **only our device-specific source references and build/recovery instructions**, not a 300+ GB Android checkout. Upstream LineageOS code remains in its original repositories. Our custom changes must be committed to their own Git repositories before the temporary build server is deleted.

> **Status: initial development manifest, not a verified build.** As of this initial commit, the publicly available `eqe` device tree, Qualcomm WFD device tree, Motorola hardware tree and kernel devicetrees still point to **`sixteen` (Android 16)** branches. The A17 kernel, kernel modules and vendor trees have separate public repositories on `main`. We **cannot claim that this mixture compiles or boots on Android 17 yet**. The manifest captures the current public sources while the A17 device integration is completed.

## Set up on a temporary Linux build server

Install the Android `repo` utility, Git and Git LFS according to LineageOS build requirements. Then, in an **empty workspace**:

```bash
mkdir -p ~/valkyos && cd ~/valkyos
repo init -u https://github.com/LineageOS/android.git -b lineage-24.0 --git-lfs
git clone https://github.com/ValkyOS/android_manifest.git .repo/local_manifests
repo sync -c -j8
```

The file [`valkyos-eqe.xml`](valkyos-eqe.xml) is a **local manifest overlay**. For that reason the `repo init` URL must be the upstream **LineageOS/android** repository, **not this repository**. `repo sync` retrieves source code; it does not certify build compatibility.

## Persisting work across rented servers

1. **Commit and push every ValkyOS patch to GitHub**, including framework changes, adaptations of GrapheneOS features and device modifications, in the repositories that contain those changes. **This manifest alone does not back up uncommitted files.**
2. Record the exact source revisions used for a successful build by running `repo manifest -r -o valkyos-revisions.xml`. Store that revision manifest in a separate tagged snapshot/commit if you need to reproduce the exact build later. **Branch names alone do not freeze history.**
3. Keep signing keys, credentials and other secrets **out of public repositories**. Make encrypted backups outside your temporary build server.
4. Keep build outputs or ROM ZIP releases separately (e.g. your own download storage). There is no need to preserve the transient `out/` directory.

## Planned direction (not implemented yet)

- Optional **Sandboxed Google Play** (GmsCompat / unprivileged Google services).
- Selected privacy controls such as Contact Scopes, Storage Scopes and sensor permissions.
- USB restrictions and an inactivity-based auto-reboot, where support permits.
- Additional compatible software hardening.

These goals are **not present** just because this manifest exists. Hardware restrictions and bootloader-unlocked operation must be documented honestly.

## Attribution

LineageOS, GrapheneOS and original device-source maintainers retain their own authorship, trademarks and applicable licenses. ValkyOS is an independent experimental project, not an official build of either project.
