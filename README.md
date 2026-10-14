# 🎀 Halls of Desire — Modding Guide

Welcome to the official guide to creating custom modifications for **Halls of Desire**!

The game supports a modular content system: you can easily add new photos, artwork and animations, and integrate them into the camera surveillance system, the rewards gallery and phone orders.

---

## 📁 Mod Folder Structure

Each mod is a separate folder located in the `Mods/` directory:

* **On PC (Windows):** `<Game folder>/Mods/<ModName>/`
* **On Android:** The app's internal folder `user://Mods/<ModName>/` (installed directly from the game menu via the `.zip` file picker button).

### Required files and folders:

```text
ExampleMod/
├── Images/                  # Folder with images (photos/artwork)
│   └── mod_image.jpg
├── Animations/              # (Optional) Folder with videos/animations (.ogv)
│   └── scene.ogv
├── info.json                # Mod metadata (name, author, icon)
├── content.json             # Database of content, tags, rarity and prices
└── icon.jpg                 # Mod icon (shown in the game's Mod Manager)
```

> [!TIP]
> Mods can be distributed either as a ready-made folder or as a regular `.zip` archive. On PC, the game automatically extracts any zip archive placed in `Mods/` on startup or when you press the "Refresh" button in the mods menu!

---

## ⚙️ 1. Configuring `info.json`

This file contains the information displayed in the Mod Manager menu.

```json
{
  "Name": "ExampleMod",
  "Author": "UserName",
  "IconPath": "icon.jpg"
}
```

* **`Name`** *(string)* — The name of your mod.
* **`Author`** *(string)* — The nickname of the author or team.
* **`IconPath`** *(string)* — Relative path to the mod's cover image (`.jpg`, `.png` and `.webp` are supported).

---

## 🖼️ 2. Configuring `content.json`

This file is a JSON array (`[...]`) of content objects:

```json
[
  {
    "Path": "Images/mod_image.jpg",
    "Price": 50,
    "Tags": [
      "bathroom",
      "brushes_his_teeth",
      "nsfw",
      "light_hair",
      "brown_eyes"
    ],
    "Rarity": "Common"
  },
  {
    "Path": "Animations/special_scene.ogv",
    "Price": 300,
    "Tags": [
      "studio",
      "night",
      "masturbation",
      "nsfw",
      "blonde"
    ],
    "Rarity": "Epic"
  }
]
```

### Parameters of each item:

| Field | Type | Description |
| :--- | :--- | :--- |
| **`Path`** | *string* | Relative path to the file. If the path starts with `Animations/` or has the `.ogv` extension, the game treats it as an **animation**. All other files are treated as **photos** (`Picture`). |
| **`Price`** | *integer* | Base selling price of the item in the Gallery, in dollars `$` (default `10`). |
| **`Rarity`** | *string* | Item rarity: `Common`, `Rare`, `Epic`, `Legendary`. |
| **`Tags`** | *string[]* | List of key tags that determine where, when and under what conditions the content can be obtained. |

---

## 💎 Content Rarity (`Rarity`)

Rarity affects the frame color in the Gallery and the drop chance when clicking an activity marker on the cameras:

| Rarity (`Rarity`) | UI Color | Drop Weight | Recommended Selling Price (`Price`) |
| :--- | :--- | :---: | :---: |
| **`Common`** | Gray | **100** | $10 – 50 |
| **`Rare`** | Blue | **30** | $60 – 120 |
| **`Epic`** | Purple | **15** | $150 – 300 |
| **`Legendary`** | Gold | **5** | $350 – 1000 |

> [!NOTE]
> If the pool of eligible rewards contains animations (`Animations`) after a mini-game is successfully completed, the game has a **10%** chance of trying to give a video instead of a photo.

---

## 🏷️ Complete Tag Reference (`Tags`)

Tags are the key part of a mod. The reward generation system only selects frames that **contain ALL the tags of the marker** the player clicked (`requiredTags.All(...)`).

In addition, appearance and explicitness tags are used to generate **phone orders** (`PhoneOrderManager`).

---

### 1. Room / Camera Tags (Locations)

Split by availability across game versions:

| Tag | Location | Version Availability | Description |
| :--- | :--- | :---: | :--- |
| **`studio`** | Studio / Bedroom | 🟢 **Free (Demo) and Paid** | The girl's main room (day and night). |
| **`bathroom`** | Bathroom | 🟢 **Free (Demo) and Paid** | Shower, toilet, mirror with sink. |
| **`swimming_pool`**| Pool | 🔒 **PAID ONLY (Full / Patreon)** | Lounge area and outdoor pool. |
| **`laundry_room`** | Laundry Room | 🔒 **PAID ONLY (Full / Patreon)** | Washing machines and tables. |
| **`basement`** | Basement | 🔒 **PAID ONLY (Full / Patreon)** | A hidden bedroom in the basement. |

> [!IMPORTANT]
> In the free Demo version, the buttons for switching to the Pool, Laundry Room and Basement are locked (clicking them opens a Patreon link). If you create content for these three locations, it will be available **only to owners of the full paid version**.

---

### 2. Time of Day Tags

* **`day`** — Daytime (used for daytime markers in the studio).
* **`night`** — Nighttime (used for nighttime markers in the studio).

---

### 3. Required Tag Combinations for Camera Markers

For your image to drop as a reward while watching the monitor, it must contain the **full set of tags** matching the marker:

#### 🟢 Free and Paid versions (Studio and Bathroom):

| Camera / Girl's Action | Required Tags in `Tags` | Scene Description |
| :--- | :--- | :--- |
| **Studio (Day) — Sleep** | `studio`, `day`, `sleep` | The girl naps during the day on the couch/bed. |
| **Studio (Day) — Sport** | `studio`, `day`, `sport` | Warm-up, yoga, fitness in the studio. |
| **Studio (Day) — Masturbation** | `studio`, `day`, `masturbation` | Intimate self-pleasure during the day. |
| **Studio (Night) — Changing Clothes**| `studio`, `night`, `changing_clothes` | Changing clothes before bed. |
| **Studio (Night) — Masturbation**| `studio`, `night`, `masturbation` | Nighttime masturbation in the bedroom. |
| **Studio (Night) — Sleep** | `studio`, `night`, `sleep` | Sleeping in bed at night. |
| **Bathroom — Brushing Teeth** | `bathroom`, `brushes_his_teeth` | Morning/evening tooth brushing. |
| **Bathroom — Toilet** | `bathroom`, `pees` | Using the toilet. |
| **Bathroom — Shower / Washing** | `bathroom`, `washable` | Washing up in the shower or bath. |
| **Bathroom — Towel** | `bathroom`, `is_wiped` | Drying off with a towel after a shower. |

---

#### 🔒 Paid version only (Pool, Laundry Room, Basement):

| Camera / Action | Required Tags in `Tags` | Scene Description |
| :--- | :--- | :--- |
| **Pool — Kissing** | `swimming_pool`, `kissing_a_girl_in_or_by_the_pool` | Romantic kissing between two girls by the water. |
| **Pool — Masturbation** | `swimming_pool`, `girls_masturbating_in_or_near_the_pool` | A girl masturbating on a lounger or in the water. |
| **Pool — Group** | `swimming_pool`, `orgy_with_girls_in_or_by_the_pool` | Group fun with girls in the pool. |
| **Pool — Sex (bonus)** | `swimming_pool`, `sex_with_a_girl_in_or_by_the_pool` | Sex at the edge of the pool. |
| **Laundry Room — Stuck** | `laundry_room`, `got_stuck_in_the_washing_machine` | A girl got stuck in the washing machine. |
| **Laundry Room — Kissing** | `laundry_room`, `kissing_a_boy_in_the_laundry_room` | Kissing a guy in the laundry room. |
| **Laundry Room — On the Table** | `laundry_room`, `masturbating_on_the_table_in_the_laundry_room` | Masturbating on the laundry table. |
| **Basement — Kissing** | `basement`, `kissing_a_boy_in_the_basement` | Romantic hugs and kisses in the basement. |
| **Basement — Sex in Bed** | `basement`, `fucking_a_guy_in_the_basement` | Intercourse on the bed in the basement. |

---

### 4. Content Rating

It is recommended to give each frame one of the following ratings:

* **`sfw`** — Safe/decent content (the girl is fully clothed, everyday activities, brushing teeth, sleeping).
* **`lite`** — Light erotica (underwear, swimsuit, suggestive angle, towel).
* **`nsfw`** — Explicit 18+ content (bare breasts/genitals, intercourse, explicit masturbation).

> [!TIP]
> The `nsfw` tag is frequently requested by phone clients. If you want your frames to be sellable through orders, be sure to tag explicit works with `nsfw`!

---

### 5. Character Appearance (for phone orders)

The phone order system randomly generates tasks for the player. To make your photos match these orders, add appearance tags:

#### Hair Color:
* `blonde` or `light_hair` — Blonde / light hair
* `brunette` or `dark_hair` — Brunette / dark hair
* `redhead` — Red hair
* `pink_hair` — Pink hair
* `blue_hair` — Blue / light blue hair
* `purple_hair` — Purple hair
* `green_hair` — Green hair

#### Eye Color:
* `blue_eyes` — Blue eyes
* `brown_eyes` — Brown eyes
* `green_eyes` — Green eyes
* `purple_eyes` — Purple eyes
* `pink_eyes` — Pink eyes
* `closed_eyes` — Closed eyes (while sleeping/during orgasm)

#### Ethnicity:
* `asian` — Asian appearance
* `ebony` — Dark-skinned girl

---

## 📞 How Phone Orders Work (`Phone Orders`)

The player can go to the phone and take a commercial photo order with a reward of **$300 to $800**.

The order generator may request:
1. A girl with a specific hair color (`blonde`, `brunette`, `redhead`, `asian`, `ebony`) in a specific room (`bathroom`, `studio`, etc.).
2. A photo of a given rarity (`Rarity`: `Common`, `Rare`, `Epic`, `Legendary`) in a specific room.
3. An explicit (`nsfw`) photo of a specific girl.
4. An explicit (`nsfw`) photo with a specific eye color (`blue_eyes`, `green_eyes`, `brown_eyes`).

If the player has obtained a photo from your mod that contains these tags, they can fulfill the order and earn a large sum!

---

## 🎨 Technical Requirements and Media Recommendations

1. **Image format:**
   * Recommended: **`.webp`** — provides the best compression while preserving sharpness and the alpha channel.
   * Also supported: **`.png`**, **`.jpg`** / **`.jpeg`**.
2. **Image resolution:**
   * Optimal: `1824x1024` (Full HD).
   * The game has a built-in Android optimizer: images larger than `2048px` are automatically downscaled on mobile devices to avoid video memory overflow (VRAM OOM).
3. **Video/animation format:**
   * Format: **`.ogv`** (Ogg Theora) — the standard video format in Godot Engine.
   * Video files must be located in the `Animations/` folder or have the `.ogv` extension.

---

## 🚀 Example of a Complete `content.json` Item

```json
{
  "Path": "Images/bathroom/teeth_brush_blonde.webp",
  "Price": 80,
  "Tags": [
    "bathroom",
    "brushes_his_teeth",
    "nsfw",
    "blonde",
    "light_hair",
    "blue_eyes"
  ],
  "Rarity": "Rare"
}
```

This frame:
* Can drop when clicking the tooth-brushing marker in the bathroom.
* Has a blue rarity frame (`Rare`).
* Sells in the shop for `$80`.
* Matches phone orders for a blonde (`blonde`), blue eyes (`blue_eyes`), an explicit photo (`nsfw`) and the `bathroom` location.
* Works in both the **Free** and **Paid** versions of the game!
