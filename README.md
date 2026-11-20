# Proprietary vendor blobs — Infinix Smart 9 HD (X6532C)

Prebuilt vendor blobs for the Infinix Smart 9 HD (**X6532C**, MediaTek **MT6765**),
extracted from the device's own firmware (the SP Flash Tool package
`X6532C-MF380ABCDEFGH-UGo-OP-260417V1675`).

This repository provides the contents of the `vendor` partition that the
device tree (`android_device_infinix_X6532C`) expects at `vendor/infinix/X6532C`.

- **1993** files, **464 MB**
- Made with LineageOS `tools/extract-utils` (Android.bp + product makefiles)
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
<project name="android_vendor_infinix_X6532C"
         path="vendor/infinix/X6532C"
         remote="github"
         revision="main" />
```

Then the device tree inherits it with:

```make
$(call inherit-product, vendor/infinix/X6532C/X6532C-vendor.mk)
```

## Local fix applied after extraction

`extract_utils` unconditionally maps any `vendor/odm/` path onto the **ODM
partition** (`$(TARGET_COPY_OUT_ODM)`), even though this device has no ODM
partition — its `odm/` is a plain directory inside the `vendor` image. The 8
affected entries were re-pointed at `$(TARGET_COPY_OUT_VENDOR)/odm/...` so the
blobs land exactly where the stock vendor image keeps them. Re-apply if the
blob list is ever regenerated:

```sh
sed -i 's|\$(TARGET_COPY_OUT_ODM)/odm/|$(TARGET_COPY_OUT_VENDOR)/odm/|g' X6532C-vendor.mk
```

`extract_utils` juga meninggalkan `vendor/etc/vintf/compatibility_matrix.xml`
dan `vendor/etc/vintf/manifest.xml` di `PRODUCT_COPY_FILES`, dan build/make
menolaknya dengan *VINTF metadata found in PRODUCT_COPY_FILES*. Keduanya
dijadikan modul `prebuilt_etc_xml` dengan `sub_dir: "vintf"`, dipasang apa
adanya di path yang sama, lalu dimasukkan ke `PRODUCT_PACKAGES`. Kalau daftar
blob digenerate ulang, keluarkan lagi dua entri itu dari `PRODUCT_COPY_FILES`.

`extract_utils` also leaves `vendor/etc/vintf/compatibility_matrix.xml` and
`vendor/etc/vintf/manifest.xml` in `PRODUCT_COPY_FILES`, which build/make
rejects with *VINTF metadata found in PRODUCT_COPY_FILES*. Both are declared as
`prebuilt_etc_xml` modules with `sub_dir: "vintf"` instead, installed verbatim
at the same paths and listed in `PRODUCT_PACKAGES`. If the blob list is
regenerated, move those two entries out of `PRODUCT_COPY_FILES` again.

## Notes

- Only **regular files** are included. The vendor partition also has 186
  symlinks (mostly toybox applets in `vendor/bin/*`) which are **not** part of
  the blob list. This does not matter while the stock vendor partition is kept.
- The vendor image produced from these blobs is not meant to replace a working
  stock vendor; the ROM keeps the stock vendor and only replaces system.
- Blobs originate from Infinix/MediaTek/Transsion. All rights belong to their
  owners.

---

# Blob vendor (proprietary) — Infinix Smart 9 HD (X6532C)

Blob `vendor` prebuilt untuk Infinix Smart 9 HD (**X6532C**, MediaTek
**MT6765**), diekstrak dari firmware device itu sendiri (paket SP Flash Tool
`X6532C-MF380ABCDEFGH-UGo-OP-260417V1675`).

Repo ini menyediakan isi partisi `vendor` yang dipakai device tree
(`android_device_infinix_X6532C`) di path `vendor/infinix/X6532C`.

- **1993** file, **464 MB**
- Digenerate dengan `tools/extract-utils` LineageOS (Android.bp + product makefile)
- Sisi vendor **Android 12 (SDK 31)**, VNDK **31**

## Perbaikan lokal setelah ekstraksi

`extract_utils` selalu memetakan path `vendor/odm/` ke **partisi ODM**
(`$(TARGET_COPY_OUT_ODM)`), padahal device ini tidak punya partisi ODM — folder
`odm/` ada di dalam image `vendor`. 8 entri yang terdampak dialihkan ke
`$(TARGET_COPY_OUT_VENDOR)/odm/...` supaya blob duduk persis seperti di image
vendor stock. Kalau daftar blob digenerate ulang, terapkan lagi:

```sh
sed -i 's|\$(TARGET_COPY_OUT_ODM)/odm/|$(TARGET_COPY_OUT_VENDOR)/odm/|g' X6532C-vendor.mk
```

## Catatan

- Hanya **file biasa** yang disertakan. Partisi vendor punya 186 symlink
  (kebanyakan applet toybox di `vendor/bin/*`) yang **tidak** masuk daftar blob.
  Ini tidak masalah selama partisi vendor stock tetap dipakai.
- Image vendor hasil build dari blob ini bukan untuk menggantikan vendor stock
  yang sudah jalan; ROM tetap pakai vendor stock dan hanya ganti system.
- Blob berasal dari Infinix/MediaTek/Transsion. Hak milik ada pada pemiliknya.
