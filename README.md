# SDV EveryWare: the manifest

The local manifest that lays the repositories of SDV EveryWare over the public AOSP tree. One
branch per AOSP release: this one, `everyware-17.0.0_r1`, is for the tag `android-17.0.0_r1`.

```bash
# WHAT: the AOSP tree at the release tag, our repositories on top, the sync
mkdir everyware && cd everyware
repo init -u https://android.googlesource.com/platform/manifest -b android-17.0.0_r1
git clone -b everyware-17.0.0_r1 https://github.com/sdv-everyware/manifest .repo/local_manifests
repo sync -c -j8
# WHAT: Linaro's prebuilt vendor package for the RB3 (firmware), once; env.sh says the URL
device/linaro/dragonboard/vendor-package-ver.sh url
# WHAT: the environment of the product and the build
source device/rb3/sdv_media_db845c/tools/env.sh
m
```

What the manifest does:

| | |
|---|---|
| `super-harry`, `dragonboard` | take the place of AOSP's `packages/services/display_safety` and `device/linaro/dragonboard` (`remove-project`, then our fork at its branch `everyware-17.0.0_r1`) |
| `libffi`, `wayland`, `wayland-protocols` | take the place of AOSP's `external/libffi`, `external/wayland` and `external/wayland-protocols`: one commit each in `Android.bp` (`product_available`, any APEX, `min_sdk_version`), so that harry's Wayland zones link them into an app's vendor APEX. No source of the libraries is changed |
| `device-rb3` | the product `sdv_media_db845c` |
| `everyware`, `ui-lib`, `dnsproxyd`, `third_party-rust`, `third_party-chrony` | under `vendor/sdv-everyware/` |
| `smarthome-app`, `tv-app`, `hub-app`, `pairs-app` | the apps, under harry's `utils/` |

The design tools take DesignCompose's importer from
[`automotive-design-compose`](https://github.com/sdv-everyware/automotive-design-compose)
(branch `everyware`) with cargo; it is not in the tree.

A new AOSP release: a new branch here, named after its tag, pointing the forks at their
branches for that release.
