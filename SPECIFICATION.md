## Tarot Deck Specification v1.0

### Overview
This specification defines a standard format for tarot decks used by tarot applications. The format is designed to:

- Separate **presentation** (images, names) from **interpretation** (meanings, readings).
- Support internationalization (i18n) and localization (l10n).
- Allow flexibility for suit and court card renaming.
- Support extensions for decks with extra or missing cards.
- Ensure compatibility with vendor-provided **interpretation files**, which are designed to extend the functionality of tarot decks without being part of this specification. Vendor-provided interpretation files are external and define meanings, astrological associations, and other occult or interpretive material. This specification focuses exclusively on presentation.

Decks must be delivered as a directory with a mandatory `deck.toml` file at the root.

---

## Schema Versioning
- This specification introduces a **schema version number** to ensure compatibility and future extensibility.
- The schema version is specified in the `deck.toml` file under `[deck]` as `schema_version`.
  
---

## Directory Skeleton

```
<deck-directory>/
  deck.toml
  scalable/               # Vector images (SVG)
    major_arcana/
      00.svg               # The Fool
      01.svg               # The Magician
      ...
    minor_arcana/
      wands/
        ace.svg
        two.svg
        ...
      cups/
        ace.svg
        ...
      swords/
        ...
      pentacles/
        ...
  h512/                    # Raster images (optional)
    (same structure as scalable/)
  names/en.toml            # Optional localized names
  names/es.toml            # (etc)
```

---

## deck.toml Schema

### Common Fields

```toml
[deck]
id = "rider-waite-smith"         # Required: Unique identifier
schema_version = "1.0"           # Required: Schema version (this document)
name = "Rider-Waite-Smith Tarot" # Required: Human readable name
version = "1.0"                  # Required: Deck version
icon = "deck-icon.png"           # Optional: deck preview image
author = "Pamela Colman Smith"   # Optional
license = "Public Domain"        # Optional
aspect_ratio = 0.5789            # Optional; default 11:19
description = "The classic Rider-Waite-Smith tarot deck, first published in 1909." # Optional
```

### Optional Presentation Aliases

```toml
[aliases.suits]
wands = "Staves"
pentacles = "Disks"

[aliases.courts]
page = "Princess"
knight = "Prince"
queen = "Queen"
king = "Knight"
```

### Optional Major Arcana Remapping

```toml
[remap_major_arcana]
08 = "justice"  # Swap Justice to 8
11 = "strength" # Swap Strength to 11
```

### Optional Custom Cards and Dynamic Card Groups

Custom cards or groups of cards can be defined under `[custom_cards]`. This allows for decks with entirely new suits or additional major arcana.

```toml
[custom_cards.wild]
id = "wild"
name = "The Wild Card"
image = "scalable/major_arcana/wild.svg"
alt_text = "A card depicting chaos and unpredictability."

[custom_cards.my_suit]
name = "Orbs"
cards = [
  { id = "ace", name = "Ace of Orbs", image = "scalable/minor_arcana/orbs/ace.svg", alt_text = "The Ace of Orbs, symbolizing new beginnings." },
  { id = "two", name = "Two of Orbs", image = "scalable/minor_arcana/orbs/two.svg", alt_text = "The Two of Orbs, symbolizing choices and balance." }
]
```

---


## Canonical ID and File Behavior

Cards are referenced internally using **canonical IDs**:

- Major Arcana: `major_arcana.00` to `major_arcana.21`
- Minor Arcana: `minor_arcana.<suit>.<rank>` where:
  - `<suit>`: `wands`, `cups`, `swords`, `pentacles`
  - `<rank>`: `ace`, `two`, ..., `ten`, `page`, `knight`, `queen`, `king`

### File-Location-Based Defaults
Applications are designed to **automatically detect and use files** placed in the expected directory structure without requiring additional configuration. For example:
- Dropping an image into `h1024/minor_arcana/wands/ace.png` will automatically map it to the card with the canonical ID `minor_arcana.wands.ace`.

This ensures that creating a deck can be as simple as placing files into the correct folders.

While applications should not **depend** on user-visible names (such as localized names) or arbitrary filenames for logic, they **must** depend on the specified directory structure and file naming conventions to ensure the default behavior works as intended.


## Aspect Ratio

- Standard assumed aspect ratio is **11:19** (~0.5789).
- Raster folders are named `h<height>/`, e.g., `h512/`, `h1024/`.
- Applications must preserve the aspect ratio when scaling images.

---

## Internationalization

- Localized names go into `names/<lang>.toml`, e.g., `names/en.toml`.
- Structure mirrors canonical IDs:

```toml
[major_arcana]
00 = "The Fool"
01 = "The Magician"
...

[minor_arcana.wands]
ace = "Ace of Wands"
two = "Two of Wands"
...
```

For any card without a localized name, applications should use the names from the default deck (most likely rider-waite-smith).

- **Alt Text Localization**:
  - Alt-text descriptions for images should also be included in the localization files under an `alt_text` field:

```toml
[major_arcana.alt_text]
00 = "A card showing a figure stepping off a cliff."
01 = "A card depicting a magician with one hand raised."
...
```

---

## Validation Rules

Applications should validate the following:

1. **Required Files**:
   - `deck.toml` must exist and adhere to the schema.
   - All referenced images and name files must exist.

2. **Canonical ID Mapping**:
   - Ensure all canonical IDs referenced in `deck.toml` have corresponding images in the defined directories.

3. **Localization Validation**:
   - Verify that all localization files have consistent structures and no missing translations for required fields.

---

## Licensing and Attribution

- A `license` field should be included in `deck.toml` to indicate the deck's licensing terms.
- Attribution requirements must be specified (if any):

```toml
[deck]
license = "CC-BY-SA-4.0"
attribution = "Artwork by Pamela Colman Smith (Public Domain)."
```

---

## Extensibility for Applications

- A reserved namespace `[app]` allows applications to include custom configurations without conflicts:

```toml
[app.my_tarot_app]
default_layout = "grid"
favorite_cards = ["major_arcana.00", "minor_arcana.wands.ace"]
```

---

## Examples

### Rider Waite Smith
```toml
[deck]
id = "rider-waite-smith"
name = "Rider-Waite-Smith"
version = "1.0"
author = "Pamela Colman Smith"
license = "Public Domain"
description = "The classic Rider-Waite-Smith tarot deck, first published in 1909."
schema_version = "1.0"
icon = "deck-icon.png" # Optional: deck preview image

[cards.major_arcana]
the_fool = { id = "00", image = "scalable/major-arcana/00.svg", alt_text = "A card showing a figure stepping off a cliff." }
the_magician = { id = "01", image = "scalable/major-arcana/01.svg", alt_text = "A card depicting a magician with one hand raised." }
```

### Custom Deck

This spec is designed so that creating a custom deck should only require creating a minimal `deck.toml` file with your card
images placed in a reasonable directory structure.

```toml
# deck.toml
[deck]
id = "tux-tarot"
name = "Tux Tarot"
author = "Tux the Anointed"
version = "1.0"
schema_version = "1.0"
description = "A Rider-Waite-Smith style deck based on Tux on their Fool's Journey."
```

Directory structure:
```
tux-tarot/
  deck.toml
  h1024/
    major_arcana/
      00.png  # The Fool
      01.png  # The Magician
      ...
      21.png  # The World
    minor_arcana/
      wands/
        ace.png
        two.png
        ...
        king.png
      cups/
        ace.png
        two.png
        ...
        king.png
      swords/
        ace.png
        two.png
        ...
        king.png
      pentacles/
        ace.png
        two.png
        ...
        king.png
```

By following this structure and file-naming convention, applications can **automatically detect the images** and map them to the correct cards based on their canonical IDs.

