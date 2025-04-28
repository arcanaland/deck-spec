## Tarot Deck Specification v1.0

### Overview
This specification defines a standard format for tarot decks used by tarot applications. The format is designed to:

- Separate presentation (images, names) from interpretation (meanings, readings).
- Support internationalization (i18n) and localization (l10n).
- Allow flexibility for suit and court card renaming.
- Support extensions for decks with extra or missing cards.

Decks must be delivered as a directory with a mandatory `deck.toml` file at the root.

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

### Required Fields

```toml
[deck]
name = "Thoth Tarot"
author = "Aleister Crowley, Lady Frieda Harris"
version = "1.0"
aspect_ratio = 0.5789 # Optional; default 11:19
```

### Optional Aliases

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

### Optional Custom Cards

```toml
[custom_cards.wild]
id = "wild"
name = "The Wild Card"
image = "scalable/major_arcana/wild.svg"
```

---

## Aspect Ratio

- Standard assumed aspect ratio is **11:19** (~0.5789).
- Raster folders are named `h<height>/`, e.g., `h512/`, `h1024/`.
- Applications must preserve the aspect ratio when scaling images.

---


## Canonical IDs

Cards are referenced internally using these IDs:

- Major Arcana: `major_arcana.00` to `major_arcana.21`
- Minor Arcana: `minor_arcana.<suit>.<rank>` where:
  - `<suit>`: `wands`, `cups`, `swords`, `pentacles`
  - `<rank>`: `ace`, `two`, ..., `ten`, `page`, `knight`, `queen`, `king`

Applications should **never** depend on filenames or user-visible names for behavior.

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

---

# Examples

## Rider Waite Smith
```toml
[deck]
id = "rider-waite-smith"
name = "Rider-Waite-Smith"
version = "1.0"
author = "Pamela Colman Smith"
license = "Public Domain"
description = "The classic Rider-Waite-Smith tarot deck, first published in 1909."
default_resolution = "scalable"
icon = "deck-icon.png" # Optional: deck preview image

[cards.major_arcana]
the_fool = { id = "00", image = "scalable/major-arcana/00.svg" }
the_magician = { id = "01", image = "scalable/major-arcana/01.svg" }
# etc.

[cards.minor_arcana.wands]
ace = { id = "01", image = "scalable/minor-arcana/wands/01.svg" }
two = { id = "02", image = "scalable/minor-arcana/wands/02.svg" }
# etc.

[cards.minor_arcana.cups]
ace = { id = "01", image = "scalable/minor-arcana/cups/01.svg" }
```


## Thoth Tarot

```toml
[deck]
name = "Thoth Tarot"
author = "Aleister Crowley, Lady Frieda Harris"
version = "1.0"
aspect_ratio = 0.5789

[aliases.suits]
wands = "Staves"
pentacles = "Disks"

[aliases.courts]
page = "Princess"
knight = "Prince"
queen = "Queen"
king = "Knight"

[remap_major_arcana]
08 = "adjustment"  # Thoth's "Adjustment" is Justice (8)
11 = "lust"        # Thoth's "Lust" is Strength (11)

[custom_cards]
# Thoth has no standard extra cards, so this section would be empty here.
```

And `names/en.toml` for Thoth might look like:

```toml
[major_arcana]
00 = "The Fool"
01 = "The Magus"
# ...

[minor_arcana.wands]
ace = "Ace of Staves"
two = "Two of Staves"
# ...
```
