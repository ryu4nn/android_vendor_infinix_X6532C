# Proprietary Vendor Blobs for the Infinix Smart 9 HD (X6532C)

Prebuilt vendor blobs for the Infinix Smart 9 HD (**X6532C**, MediaTek **MT6765**),
extracted from the device's own firmware (the SP Flash Tool package
`X6532C-MF380ABCDEFGH-UGo-OP-260417V1675`).

This repository provides the contents of the `vendor` partition that the device
tree (`android_device_infinix_X6532C`) expects at `vendor/infinix/X6532C`.

- **1993** files, **464 MB**
- Generated with LineageOS `tools/extract-utils` (Android.bp + product makefiles)
- Vendor side is **Android 12 (SDK 31)**, VNDK **31**

## Layout

```
Android.bp              generated Soong modules (cc_prebuilt_*, android_app_import, ...)
Android.mk              stub (kept for compatibility)
BoardConfigVendor.mk    per-vendor board flags
X6532C-vendor.mk        PRODUCT_SOONG_NAMESPACES + PRODUCT_COPY_FILES
proprietary/vendor/...  the blobs, mirroring the vendor partition
```

## Usage

Add this repository to your local manifest so that it lands at
`vendor/infinix/X6532C`, e.g.:

```xml
<project name="ryu4nn/android_vendor_infinix_X6532C"
         path="vendor/infinix/X6532C"
         remote="github"
         revision="main" />
```

Then the device tree inherits it with:

```make
$(call inherit-product, vendor/infinix/X6532C/X6532C-vendor.mk)
```

The makefile copies **1932 files** into the vendor image.

## Local fixes applied after extraction

### ODM paths

`extract_utils` unconditionally maps any `vendor/odm/` path onto the **ODM
partition** (`$(TARGET_COPY_OUT_ODM)`), even though this device has no ODM
partition — its `odm/` is a plain directory inside the `vendor` image. The 8
affected entries were re-pointed at `$(TARGET_COPY_OUT_VENDOR)/odm/...` so the
blobs land exactly where the stock vendor image keeps them. Re-apply if the blob
list is ever regenerated:

```sh
sed -i 's|\$(TARGET_COPY_OUT_ODM)/odm/|$(TARGET_COPY_OUT_VENDOR)/odm/|g' X6532C-vendor.mk
```

### VINTF metadata

`extract_utils` also leaves the VINTF metadata in `PRODUCT_COPY_FILES`, which
build/make rejects with *VINTF metadata found in PRODUCT_COPY_FILES*: the vendor
`compatibility_matrix.xml` and `manifest.xml`, plus the four ODM SKU manifests
under `vendor/odm/etc/vintf/`. All six are declared as `prebuilt_etc_xml`
modules instead, installed verbatim at the same paths and listed in
`PRODUCT_PACKAGES`. The ODM ones are caught even though this device has no ODM
partition, because `TARGET_COPY_OUT_ODM` expands to `vendor/odm`
(`core/board_config.mk:711`). If the blob list is regenerated, move those six
entries out of `PRODUCT_COPY_FILES` again.

### build.prop

The four `build.prop` entries (`vendor/build.prop`, `vendor/odm/etc/build.prop`,
`vendor/vendor_dlkm/etc/build.prop`, `vendor/odm_dlkm/etc/build.prop`) are also
gone from `PRODUCT_COPY_FILES`: `build/make/core/sysprop.mk` generates those
exact files from the product configuration and the generated file replaces the
copied one without any error. The two that carry real content are kept in the
device tree as `vendor.prop` and `odm.prop` and installed through
`TARGET_VENDOR_PROP` / `TARGET_ODM_PROP`; the two DLKM ones only hold partition
identity properties and are regenerated.

## Notes

- Only **regular files** are included. The vendor partition also has 186
  symlinks (mostly toybox applets in `vendor/bin/*`) which are **not** part of
  the blob list. This does not matter while the stock vendor partition is kept.
- The vendor image produced from these blobs is not meant to replace a working
  stock vendor; the ROM keeps the stock vendor and only replaces the system side.
- Blobs originate from Infinix/MediaTek/Transsion. All rights belong to their
  owners.

## Copyright

```
#
# Copyright (C) 2025 The LineageOS Project
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
```
