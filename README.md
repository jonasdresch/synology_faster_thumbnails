# Speeding Up Thumbnail Generation on Older Synology NAS Devices

This repository provides optimized configuration files and tools for dramatically improving the speed of **photo** and **video** thumbnail generation on older Synology NAS models such as the **DS216play** and similar ARM‑based or low‑power systems. These devices often struggle with Synology’s default high‑resolution thumbnail pipeline, causing indexing to take hours or even days on large libraries.

The repository contains:

* **thumb.conf_improved** – a lower‑resolution, lower‑quality thumbnail profile for *much* faster photo thumbnail creation.
* **ffmpeg41** – a custom wrapper that accelerates *video* thumbnail generation by avoiding Synology’s expensive seek operation.

---

## 1. Faster Photo Thumbnail Generation

Synology generates multiple thumbnail sizes for each image, and the default settings can overwhelm older CPUs. This repository includes **two different configurations**:

* **thumb.conf** – reduces *only JPEG quality* while keeping default Synology resolutions. This provides a moderate speed‑up while preserving the visual sharpness of thumbnails.
* **thumb.conf_improved** – reduces *both quality and thumbnail resolutions*, resulting in the fastest possible indexing performance. This is ideal for older, resource‑limited NAS devices where processing speed matters more than thumbnail detail.

### Steps

1. **Back up the original thumbnail configuration**:

   ```sh
   cp /usr/syno/etc.defaults/thumb.conf /usr/syno/etc.defaults/thumb.conf.backup
   ```

2. **Replace it with the improved version** from this repository:

   ```sh
   cp thumb.conf_improved /usr/syno/etc.defaults/thumb.conf
   ```

   This version reduces sizes and quality, resulting in dramatically faster generation on older systems.

3. **Restart Synology's thumbnail services**:

   ```sh
   synoservicecfg --restart pkgctl-SynoFinder
   synoservicecfg --restart synomkthumbd
   ```

After the restart, Synology will regenerate thumbnails using the optimized settings, significantly reducing CPU load and overall processing time.

---

## 2. Faster Video Thumbnail Generation

The provided `ffmpeg41` wrapper replaces Synology’s default behavior. Synology normally invokes:

```
ffmpeg41 -ss 00:00:03 ...
```

This 3‑second seek (`-ss 00:00:03`) is extremely slow on older NAS units. The optimized script removes that seek entirely so thumbnail extraction occurs instantly from the first frame.

### How the Optimized Script Works

For a detailed explanation of what the wrapper changes and how it behaves, **please refer to the comments inside the file itself**.

### Installation Steps

1. **Navigate to Synology’s CodecPack ffmpeg folder**:

   ```sh
   cd /var/packages/CodecPack/target/pack/bin/
   ```

2. **Rename the original binary**:

   ```sh
   mv ffmpeg41 ffmpeg41.orig
   ```

3. **Copy the optimized wrapper from this repository**:

   ```sh
   cp /path/to/repo/ffmpeg41 /var/packages/CodecPack/target/pack/bin/ffmpeg41
   chmod +x ffmpeg41
   ```

4. **Restart Synology media services** to activate the new behavior.

### Result

Video thumbnails are now generated from the **first frame**, avoiding expensive seeks and enabling *much* faster processing—especially on ARM‑based models.

---

## Notes

* Tested only on **DSM 7.2.2‑72806 Update 2** running on a **DS216play**.
* Results may vary on other Synology models or DSM versions.
* Keep backups of original system files as DSM updates may overwrite modifications.
* While reduced quality speeds up indexing substantially, it may slightly affect thumbnail appearance in Synology Photos.
* These tweaks are safe and reversible.

---

## License

Released under the MIT License. Contributions and improvements are welcome.
