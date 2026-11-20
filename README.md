# SDV EveryWare: the manifest

The local manifest that lays the repositories of SDV EveryWare over the public AOSP tree. One
branch per AOSP release: this one, `everyware-17.0.0_r1`, is for the tag `android-17.0.0_r1`.

```bash
# WHAT: the AOSP tree at the release tag, our repositories on top, the sync
mkdir everyware && cd everyware
repo init -u https://android.googlesource.com/platform/manifest -b android-17.0.0_r1
git clone -b everyware-17.0.0_r1 https://github.com/sdv-everyware/manifest .repo/local_manifests
repo sync -c -j8
```

Then one product, each with its own out directory. Its repository's README has the details.

| Target | Product | Repository | Needs |
|---|---|---|---|
| Cuttlefish emulator | `sdv_media_everyware_cf` | [device-cf](https://github.com/sdv-everyware/device-cf) | a Linux host with KVM and the Cuttlefish host packages; no board |
| Qualcomm RB3 (DragonBoard 845c) | `sdv_media_db845c` | [device-rb3](https://github.com/sdv-everyware/device-rb3) | the board; the reference target |
| Raspberry Pi 5 | `sdv_media_rpi5` | [device-rpi5](https://github.com/sdv-everyware/device-rpi5) | the board and two projects of the Raspberry Pi port (its README); work in progress |

```bash
# WHAT: the emulator: build, start, the panel in the browser at https://localhost:1443
# Install the Cuttlefish host packages (cuttlefish-base, cuttlefish-user) first by following
# https://github.com/google/android-cuttlefish#installing-the-packages
source device/cf-everyware/sdv_media_everyware_cf/tools/env.sh
m
device/cf-everyware/sdv_media_everyware_cf/tools/run.sh start
```

```bash
# WHAT: the RB3: Linaro's prebuilt vendor package (firmware), once; env.sh says the URL
device/linaro/dragonboard/vendor-package-ver.sh url
source device/rb3/sdv_media_db845c/tools/env.sh
m
```

```bash
# WHAT: the Raspberry Pi 5: build, then the image of the SD card
source device/rpi5/sdv_media_rpi5/tools/env.sh
m
device/rpi5/sdv_media_rpi5/tools/mkimg.sh
```

What the manifest does:

| | |
|---|---|
| `super-harry`, `dragonboard` | take the place of AOSP's `packages/services/display_safety` and `device/linaro/dragonboard` (`remove-project`, then our fork at its branch `everyware-17.0.0_r1`) |
| `libffi`, `wayland`, `wayland-protocols` | take the place of AOSP's `external/libffi`, `external/wayland` and `external/wayland-protocols`: one commit each in `Android.bp` (`product_available`, any APEX, `min_sdk_version`), so that harry's Wayland zones link them into an app's vendor APEX. No source of the libraries is changed |
| `device-rb3`, `device-cf`, `device-rpi5` | the products: `sdv_media_db845c` (Qualcomm RB3), `sdv_media_everyware_cf` (the Cuttlefish emulator), `sdv_media_rpi5` (Raspberry Pi 5) |
| `everyware`, `ui-lib`, `dnsproxyd`, `third_party-rust`, `third_party-chrony` | under `vendor/sdv-everyware/` |
| `AGENTS.md`, `CLAUDE.md` at the tree's top | links to `everyware`'s `AGENTS.md`, the guide for coding agents (`linkfile`, as AOSP links `Android.bp` from `build/soong`): an agent opened on the tree reads it first and finds the skills |
| `template-app`, `smarthome-app`, `tv-app`, `hub-app`, `pairs-app`, `camera-app` | the apps, under harry's `utils/`; `template-app` is the template of a new one, `camera-app` the panel's camera, a process of its own |

The design tools take DesignCompose's importer from
[`automotive-design-compose`](https://github.com/sdv-everyware/automotive-design-compose)
(branch `everyware`) with cargo; it is not in the tree.

A new AOSP release: a new branch here, named after its tag, pointing the forks at their
branches for that release.
