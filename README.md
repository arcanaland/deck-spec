# Tarot Deck Specification v1.0

> Maintained by [Arcana Land](https://github.com/arcanaland).

## Overview
This specification defines a standard format for tarot decks used by tarot applications. The format is designed to:

- Separate **presentation** (images, names) from **interpretation** (meanings, readings).
- Support internationalization (i18n) and localization (l10n).
- Allow flexibility for suit and court card renaming.
- Support extensions for decks with extra or missing cards.
- Ensure compatibility with vendor-provided **esoterica files**, which are designed to extend the functionality of tarot decks without being part of this specification. Vendor-provided esoterica files are external and define meanings, astrological associations, and other occult or interpretive material. This specification focuses exclusively on presentation.

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
  scalable/               # Vector images (SVG only)
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
  h1024/                   # Higher resolution raster images (optional)
    (same structure as scalable/)
  h2048/                   # Highest resolution raster images (optional)
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

# Additional optional metadata
created_date = "1909-12-01"      # Original creation date
updated_date = "2025-01-15"      # Last update date
publisher = "US Games Systems"   # Publisher information
website = "https://example.com/rws-deck" # Website for the deck
tags = ["traditional", "classic", "beginner-friendly"] # Categorization tags
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

### Optional Excluded Cards

```toml
[deck.excluded_cards]
# List cards that are intentionally excluded from this deck
cards = [
  "minor_arcana.pentacles.page",
  "minor_arcana.pentacles.knight"
]
reason = "This historical Tarocchi deck excludes these court cards."
```

### Optional Custom Cards and Dynamic Card Groups

Custom cards or groups of cards can be defined under `[custom_cards]`. This allows for decks with entirely new suits or additional major arcana.

```toml
[custom_cards]
# Add a completely new card to major arcana
[custom_cards.major_arcana.happy_squirrel]
id = "happy_squirrel"  # This will be referenced as major_arcana.happy_squirrel
name = "The Happy Squirrel"
image = "scalable/major_arcana/happy_squirrel.svg"
alt_text = "A cheerful squirrel gathering acorns, representing preparation and unexpected joy."
position = 22  # Optional - indicates position in sequence after traditional cards

# Add an entire new suit
[custom_cards.minor_arcana.stars]
name = "Stars"
cards = [
  { id = "ace", name = "Ace of Stars", image = "scalable/minor_arcana/stars/ace.svg" },
  { id = "two", name = "Two of Stars", image = "scalable/minor_arcana/stars/two.svg" },
  # ... and so on
]
```

### Optional Companion Files

Decks can link to compatible esoterica files and other companion resources while maintaining separation of concerns:

```toml
[deck.companions]
# Optional section listing compatible companion files
esoterica = [
  { id = "waite-meanings", name = "A.E. Waite's Original Meanings", uri = "https://arcana.land/esoterica/waite-rws.toml" },
  { id = "modern-psychological", name = "Modern Psychological Approach", uri = "local://esoterica/rws-psychological.toml" }
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

## Image Formats and Technical Requirements

### Vector Graphics
- SVG is the only supported vector format
- Vector graphics should be placed in the `scalable/` directory

### Raster Graphics
- Supported formats: PNG (preferred), JPEG, WebP
- Recommended resolutions: h512, h1024, h2048 (height in pixels)
- Each resolution should have its own directory (e.g., `h512/`, `h1024/`, `h2048/`)
- PNG with alpha channel recommended for images requiring transparency

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
00 = "The Fool: A young person in colorful clothes steps off a cliff, carrying a white rose. A small dog jumps at their heels."
01 = "The Magician: A figure standing at a table with the four suit symbols, one hand raised toward the sky, the other pointing to the ground."
...
```

### Alt Text Requirements
Alt text should describe the visual elements of the card. For example:
- "The Fool: A young person in colorful clothes steps off a cliff, carrying a white rose. A small dog jumps at their heels."
- "Five of Cups: A figure in a black cloak looks down at three spilled cups, while two full cups stand behind them."

Note that interpretation elements (such as "represents new beginnings") should be kept in esoterica files, not in the alt text within the deck specification.

---

## Esoterica File Compatibility

While this specification focuses exclusively on presentation aspects of tarot decks, it provides a foundation for companion esoterica files through a standardized mapping system.

### Esoterica Files

Esoterica files must follow the [arcanaland/esoterica-spec](https://github.com/arcanaland/esoterica-spec) specification, which means:

1. They reference the same canonical IDs used in this specification
2. They can be deck-specific or generic
3. They can include meanings, elemental associations, astrological correspondences, etc.

### Mapping System for Non-Standard Decks

To ensure esoterica files can work with non-standard decks:

1. **Aliases**: The `[aliases]` section in deck.toml provides information for converting between non-standard and standard naming conventions
2. **Remapping**: The `[remap_major_arcana]` section maps cards that have different positions in the sequence
3. **Custom Cards**: Custom cards defined in deck.toml can have custom interpretations in esoterica files

### Example of Esoterica Mapping (In Esoterica Files)

```toml
# This would be in an esoterica file, not in deck.toml
[esoterica]
schema_version = "1.0"
name = "Traditional RWS Meanings"
compatible_decks = ["rider-waite-smith", "cosmic-tarot"]

# Standard mappings section that links non-standard names to canonical IDs
[esoterica.card_mappings]
"minor_arcana.torches.prince" = "minor_arcana.wands.knight"
"minor_arcana.disks.princess" = "minor_arcana.pentacles.page"
"major_arcana.adjustment" = "major_arcana.11"  # Thoth's Justice equivalent

# Custom cards have their own dedicated interpretations
[esoterica.custom_cards.major_arcana.happy_squirrel]
keywords = ["surprise", "playfulness", "hidden resources"]
upright_meaning = "Unexpected resources and opportunities appearing in your life"
reversed_meaning = "Missing opportunities due to lack of awareness"
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
created_date = "1909-12-01"
updated_date = "2025-01-15"
publisher = "US Games Systems"
website = "https://example.com/rws-deck"
tags = ["traditional", "classic", "beginner-friendly"]

# Link to compatible esoterica files
[deck.companions]
esoterica = [
  { id = "waite-meanings", name = "A.E. Waite's Original Meanings", uri = "https://arcana.land/esoterica/waite-rws.toml" },
  { id = "golden-dawn", name = "Golden Dawn Attributions", uri = "https://arcana.land/esoterica/golden-dawn.toml" }
]

[cards.major_arcana]
the_fool = { id = "00", image = "scalable/major_arcana/00.svg", alt_text = "The Fool: A young person in colorful clothes steps off a cliff, carrying a white rose. A small dog jumps at their heels." }
the_magician = { id = "01", image = "scalable/major_arcana/01.svg", alt_text = "The Magician: A figure standing at a table with the four suit symbols, one hand raised toward the sky, the other pointing to the ground." }
```

### Custom Deck with Non-Standard Names

```toml
# deck.toml
[deck]
id = "elemental-torches"
name = "Elemental Torches Tarot"
author = "Fire Sage"
version = "1.0"
schema_version = "1.0"
description = "A fire-themed tarot deck with renamed suits and additional elemental cards."
created_date = "2025-01-01"
website = "https://example.com/elemental-torches"
tags = ["elemental", "fire-themed"]

# These aliases help esoterica files map to standard meanings
[aliases.suits]
wands = "Torches"
cups = "Waters" 
swords = "Winds"
pentacles = "Stones"

[aliases.courts]
page = "Student"
knight = "Warrior"
queen = "Priestess"
king = "Master"

# Add a custom elemental card
[custom_cards.major_arcana.elemental_force]
id = "elemental_force"
name = "The Elemental Force"
image = "scalable/major_arcana/elemental_force.svg"
alt_text = "A vortex of the four elements swirling together in perfect harmony."
position = 22

# Link to a dedicated esoterica file for this deck
[deck.companions]
esoterica = [
  { id = "elemental-meanings", name = "Elemental Torches Esoterica", uri = "https://arcana.land/esoterica/elemental-torches.toml" }
]
```

### Standard Deck Structure

This spec is designed so that creating a custom deck should only require creating a minimal `deck.toml` file with your card
images placed in a reasonable directory structure.

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
