# Custom Character & Sprite Sheet Specification Guide 🐾

This guide outlines the **sprite sheet specifications** and **registration steps** required to import your own original characters into the desktop mascot "TokoToko".

With a single image file structured according to this specification, TokoToko can render smooth autonomous walking and rich reactive animations directly on your desktop.

---

## 1. Image Specifications

| Item | Specification | Notes |
| :--- | :--- | :--- |
| **File Format** | **PNG** or **WebP** | Must include an alpha channel (transparent background) |
| **Total Resolution** | **2048 px (W) × 2816 px (H)** | 8 columns × 11 rows |
| **Frame Size** | **256 px × 256 px** | Square aspect ratio |
| **Frame Layout** | **8 frames per row** (11 rows, 88 frames total) | Played sequentially from left to right |
| **Color Space** | sRGB | Alpha transparency recommended |

> 💡 **Recommended Settings:**  
> To maintain sharp pixel art or line art while keeping memory consumption low, export as **lossless transparent WebP** or **transparent 32-bit PNG**.

---

## 2. Row Layout & Action Definitions

The sprite sheet is organized vertically into **11 rows (Row 00 through Row 10)**.  
Each row contains **8 frames (Col 0 through Col 7)** that form a single seamless looping animation.

```text
+--------+--------+--------+--------+--------+--------+--------+--------+
| Col 0  | Col 1  | Col 2  | Col 3  | Col 4  | Col 5  | Col 6  | Col 7  |
+--------+--------+--------+--------+--------+--------+--------+--------+
| Row 00 : Idle standing (idle)                                          |
| Row 01 : Walking right loop (running-right)                            |
| Row 02 : Walking left loop (running-left)                              |
| Row 03 : Greeting / peace sign (waving)                                |
| Row 04 : Joyful jump (jumping)                                         |
| Row 05 : Disappointment / sigh (failed)                                |
| Row 06 : Hair tucking / waiting (waiting)                              |
| Row 07 : Pondering / idea discovery (running)                          |
| Row 08 : Typing on floating hologram (review)                          |
| Row 09 : Startled / hand to chest (surprised)                          |
| Row 10 : Bored / sleepy idling (bored)                                 |
+-----------------------------------------------------------------------+
```

### Action Breakdown

| Row Index | Action Identifier (`action`) | Motion / Behavior | Primary Trigger |
| :--- | :--- | :--- | :--- |
| **Row 00** | `idle` | Gentle breathing, blinking | Default standing standby state |
| **Row 01** | `running-right` | Walking / running toward the right | Autonomous roaming (moving right) |
| **Row 02** | `running-left` | Walking / running toward the left | Autonomous roaming (moving left) |
| **Row 03** | `waving` | Smiling, waving hand, peace sign | Random idle action / App startup |
| **Row 04** | `jumping` | Joyful jumping with bounce | Left-click / CLI command success (`Exit 0`) |
| **Row 05** | `failed` | Anxious slump, sigh, drop head | CLI failure (`Exit != 0`) / Build error |
| **Row 06** | `waiting` | Tucking hair behind ear, shifting pose | Extended idle pause / Awaiting input |
| **Row 07** | `running` | Fold arms to ponder → pointing finger | Prompt sent / AI thinking state |
| **Row 08** | `review` | Typing on aerial glowing hologram | Build running / Streaming AI response |
| **Row 09** | `surprised` | Startled hop, hand on chest | Alert popups / New mail / Interruptions |
| **Row 10** | `bored` | Drowsy nodding, yawning, sleepy | Extended user inactivity |

---

## 3. Illustration & Drawing Guidelines

1. **Consistent Baseline**  
   Keep foot ground contact positions level across each 256×256 px cell so the character doesn't bounce irregularly when idling or walking.
2. **Smooth Looping**  
   Frames 0 through 7 should loop seamlessly back into frame 0.
3. **Minimal Viable Character Setup**  
   If filling all 11 rows at once is overwhelming, start with **Row 00 (idle), Row 01 (walk right), and Row 02 (walk left)**. You can temporarily duplicate Row 00 across the remaining rows; TokoToko will still run without issues.

---

## 4. How to Import into TokoToko

Custom sprite sheets can be registered via drag-and-drop:

1. Right-click the active mascot on your desktop and choose **Settings**.
2. Navigate to the **Character Slots** tab in the sidebar.
3. Drag and drop your image file (`.png` or `.webp`) into any open slot card (Slots 2 through 5).
4. Enter a name for your character and save.
5. Click the **Switch** button on the card to switch your active desktop mascot to your original creation!

---

## 5. Templates & Resources

Guideline templates (PSD / PNG with 256×256 px grid overlays) are available to help align your artwork. Create your own companion and let them stroll across your desktop🐾
