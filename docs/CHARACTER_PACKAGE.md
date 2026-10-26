# Character Package (`.toko` / `.zip`) Creation Guide 🐾

**English** | [日本語 (Japanese)](CHARACTER_PACKAGE.ja.md)

This guide explains how to bundle your custom sprite sheet together with a **character name, role title, status quote, and multi-language (`i18n`) settings** into a single **Open Character Package (`.toko` / `.zip`)** for easy importing and sharing in "TokoToko".

---

## 1. Supported Character Formats

You can import characters into TokoToko's character slots (Slot 1 through Slot 4) via drag-and-drop using any of the following three formats:

| Format | Extension | Purpose & Overview |
| :--- | :--- | :--- |
| **Single Sprite Sheet** | `.png` / `.webp` | Best for quickly testing or registering a standalone sprite sheet image (👉 [Sprite Sheet Specification Guide](SPRITE_SPEC.md)). |
| **Open Character Package** | **`.toko` / `.zip`** | **[Covered in this Guide]** A standard ZIP archive containing a sprite sheet and `manifest.json` (name, role, status quote, etc.), ideal for creators distributing original characters. |
| **Official Character Package** | `.tokochar` | Dedicated package format distributed officially by the TokoToko Project. Can be imported via drag-and-drop just like open packages. |

---

## 2. Package Structure (`.toko` / `.zip`)

An Open Character Package is a standard ZIP archive containing the following two files **at the root level (flat structure, without nested folders)**.  
You may keep the `.zip` extension or rename it to `.toko`—TokoToko supports both identically.

```text
my_character.toko (or my_character.zip)
├── manifest.json       # Character metadata file (Required / UTF-8)
└── sprite.webp         # Sprite sheet image (Required / sprite.png also supported)
```

### Package Constraints
To ensure security and maintain ultra-lightweight performance, packages must adhere to these limits:
- **Maximum Uncompressed Size**: Up to **20 MB** total.
- **Maximum File Count**: Up to **20 files** inside the archive.
- **Flat Archive Structure**: Do not include subdirectories or relative path traversals (`..`). Select the files directly when creating the ZIP archive.
- **Sprite Sheet Dimensions**: Aspect ratio must be **8:11** (8 columns × 11 rows), with a recommended and **maximum resolution of 2048 × 2816 px**. See the [Sprite Sheet Specification Guide](SPRITE_SPEC.md) for details.

---

## 3. Writing `manifest.json`

Save `manifest.json` using **UTF-8** encoding.

### 3.1 Basic Example
```json
{
  "id": "my_original_chara",
  "name": "Kohaku",
  "version": "1.0.0",
  "author": "Your Name",
  "role": "Navigator",
  "statusText": "Let's enjoy coding together today!",
  "spriteFile": "sprite.webp"
}
```

### 3.2 Multi-Language (`i18n`) Example
By adding an optional `i18n` block, the character's name, role, and status quote will automatically switch to match the user's selected UI language (`ja` or `en`) in TokoToko.

```json
{
  "id": "my_original_chara",
  "name": "コハク",
  "version": "1.0.0",
  "author": "Your Name",
  "role": "Navigator",
  "statusText": "今日も楽しくコーディングしようね！",
  "spriteFile": "sprite.webp",
  "i18n": {
    "en": {
      "name": "Kohaku",
      "role": "Navigator",
      "statusText": "Let's enjoy coding together today!"
    }
  }
}
```

### 3.3 Field Reference

| Key | Type | Required | Default | Description |
| :--- | :--- | :---: | :--- | :--- |
| `id` | `string` | **Required** | — | Unique identifier for the character (alphanumeric and underscores recommended). Used as the tracking key for Kizuna (cumulative bond time). |
| `name` | `string` | **Required** | — | Display name shown on the slot card. |
| `version` | `string` | Optional | `"1.0.0"` | Package version string. |
| `author` | `string` | Optional | `""` | Creator / author credit. |
| `role` | `string` | Optional | `"Custom"` | Role or title badge shown on the slot card (e.g., `"Navigator"`, `"Healer"`). |
| `statusText` | `string` | Optional | `""` | Short quote or status line scrolling across the slot card ticker. |
| `spriteFile` | `string` | Optional | `"sprite.webp"` | Filename of the bundled sprite sheet image. If omitted, `sprite.webp` or `sprite.png` is detected automatically. |
| `i18n` | `object` | Optional | `null` | Language-specific overrides (`"ja"`, `"en"`) for `name`, `role`, and `statusText`. |

> 💡 **Kizuna (Bond Time) Persistence:**  
> TokoToko tracks the time you spend with each character based on their `id`. As long as a package shares the same `id`, your accumulated Kizuna rank and bond time are preserved even if you remove the character or move them to a different slot.

---

## 4. How to Build the Package

### 🍏 macOS
1. Prepare `manifest.json` and `sprite.webp` (or `sprite.png`) in a working folder.
2. **Select the two files directly** (not the parent folder), right-click, and choose **Compress 2 Items**—or run the following in Terminal:
   ```bash
   zip my_character.toko manifest.json sprite.webp
   ```
3. Your `.zip` (or renamed `.toko`) package is ready!

### 🪟 Windows
1. **Select both `manifest.json` and `sprite.webp`** (or `sprite.png`) inside your working folder.
2. Right-click and choose **Compress to ZIP file** (or **Send to** → **Compressed (zipped) folder**).
3. Optionally rename the file extension from `.zip` to `.toko` (both `.zip` and `.toko` work out of the box).

---

## 5. How to Import into TokoToko

1. Right-click the mascot on your desktop (or the system tray icon) and select **`⚙️ System Core...`**.
2. Open the **Character Slots** tab.
3. **Drag and drop** your `.toko` / `.zip` file (or an official `.tokochar` file) onto any card from **Slot 1 to Slot 4** (Slot 0 is reserved for Toko).
4. Click the **ATTACH** button on the card to summon your character to the desktop!
