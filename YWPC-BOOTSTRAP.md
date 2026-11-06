# YWPC Bootstrap

This fork builds Termux packages for the app id `com.termux.ywpc` and prefix `/data/data/com.termux.ywpc/files/usr`. The Termux app Java namespace remains `com.termux`; the termux-am implementation keeps its own `com.termux.termuxam` namespace.

## Build the bootstrap

Run from the repository root on Linux with Docker available:

```bash
./scripts/run-docker.sh ./scripts/build-bootstraps.sh -f --add openssh --architectures aarch64,arm,i686,x86_64
```

The command produces `bootstrap-aarch64.zip`, `bootstrap-arm.zip`, `bootstrap-i686.zip`, and `bootstrap-x86_64.zip` in the repository root. `openssh` is included so the app's SSH host manager can connect on first launch.

## Build the Android app

Point the app build at this directory. For signed, ABI-specific release APKs, provide a private keystore through environment variables; keep the keystore and passwords outside the repository:

```bash
TERMUX_BOOTSTRAP_DIR=/path/to/termux-packages-ywpc \
TERMUX_SPLIT_APKS_FOR_RELEASE_BUILDS=1 \
TERMUX_RELEASE_KEYSTORE=/path/to/release-key.p12 \
TERMUX_RELEASE_KEY_ALIAS=termux-ywpc-release \
TERMUX_RELEASE_STORE_PASSWORD='...' \
./gradlew :app:assembleRelease
```

`TERMUX_RELEASE_KEY_PASSWORD` is optional and defaults to the store password. The app build verifies every bootstrap archive before embedding it. Do not set `TERMUX_ALLOW_UPSTREAM_BOOTSTRAP=1` for a usable APK.

## Package repository compatibility

The bootstrap contains the initial package set plus OpenSSH. The default upstream Termux apt repository publishes binaries compiled for `/data/data/com.termux/files/usr`; those packages are not compatible with this fork's prefix. Installing or upgrading packages after bootstrap requires a package repository rebuilt and hosted for `com.termux.ywpc`.
