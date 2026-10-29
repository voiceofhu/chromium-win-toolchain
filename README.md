# Chromium Windows Toolchain Packager

This repository packages a Windows VS/SDK toolchain for Chromium cross-builds
without requiring access to Google's private `chrome-wintoolchain` bucket.

## Run

1. Open the `Package Chromium Windows Toolchain` workflow in GitHub Actions.
2. Click `Run workflow`.
3. Keep `include_arm64` as `true`, especially when cross-building from an
   Apple Silicon Mac. Chromium may need the ARM64 toolchain environment even
   when `target_cpu` is `x64`.
4. Download the `chromium-win-toolchain` artifact when the run finishes.

The artifact contains a `<hash>.zip` toolchain package and `usage.txt`.

## Use Locally

Unzip the artifact and put `<hash>.zip` in a local directory, for example:

```bash
mkdir -p /Volumes/sn@root/toolchains/chromium-win
```

Then export:

```bash
export DEPOT_TOOLS_WIN_TOOLCHAIN_BASE_URL=/Volumes/sn@root/toolchains/chromium-win
export GYP_MSVS_HASH_3bfcb536c8=<hash>
```

`<hash>` is the zip filename without `.zip`. `3bfcb536c8` is Chromium's
`TOOLCHAIN_HASH` in `src/build/vs_toolchain.py` (M156/M157); use the value from
your checkout when Chromium rolls its toolchain.

Then rerun Chromium's Windows build:

```bash
cd /Volumes/sn@root/Documents/workspaces/voh/chromium
make build-win
```

## Notes

- This workflow uses the GitHub-hosted `windows-2025-vs2026` runner.
- It packages Visual Studio `2026` and Windows SDK `10.0.28000.0`, which
  Chromium M156+ requires. The runner image only ships the 26100 SDK, so the
  workflow first installs `winsdksetup.exe` (10.0.28000.2526, SHA256-checked,
  all features including Debugging Tools). To move to another SDK, update the
  `WINDOWS_SDK_*` and `CHROMIUM_TOOLCHAIN_HASH` values in the workflow `env`.
- The generated package is for your own build environment. Do not publish or
  redistribute it without confirming the relevant Microsoft license terms.
