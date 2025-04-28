# Tarot Esoterica Specification v1.0

**Maintained by:** [Arcana Land](https://github.com/arcanaland)

## Overview

This specification defines a standard format for tarot esoterica - the interpretative and symbolic information associated with tarot decks. Unlike the [deck specification](https://github.com/arcanaland/deck-spec) which focuses on presentation, this specification focuses on meaning and correspondence systems.

The esoterica specification is built around these principles:

- **Accumulating Properties**: Meanings and properties combine across levels, building a rich interpretation
- **Separation of Inherent vs. Interpretive Properties**: Clear distinction between card attributes and interpretive frameworks
- **Modular Components**: Independent TOML files that can be mixed and matched
- **Compatibility**: Works with the existing deck specification's canonical IDs

## Schema Versioning

- This specification uses a **schema version number** to ensure compatibility and future extensibility.
- The schema version is specified within each esoterica file under the top-level section as `schema_version`.

## File Structure

Esoterica files are standalone TOML files that can be stored anywhere. The recommended location follows XDG standards:

```
$XDG_DATA_HOME/tarot/esoterica/meanings/base.toml
$XDG_DATA_HOME/tarot/esoterica/correspondences/numerology.toml
$XDG_DATA_HOME/tarot/esoterica/contexts/love.toml
...
```

Which typically resolves to:

```
~/.local/share/tarot/esoterica/meanings/base.toml
~/.local/share/tarot/esoterica/correspondences/numerology.toml
~/.local/share/tarot/esoterica/contexts/love.toml
...
```


## Inherent vs. Interpretive Properties

This specification makes a clear distinction between two types of properties:

### Inherent Properties
These are intrinsic to the card itself and are based on the card's position in the tarot system:
- Card type (major arcana vs. minor arcana)
- Suit (for minor arcana)
- Number (both for major arcana and numbered minor arcana)
- Court status (court card vs. non-court card)
- Specific card identity

### Interpretive Properties
These are external frameworks applied to the cards and can vary based on tradition, context, or use:
- Reading context (love, career, spiritual, etc.)
- Tradition/system (Golden Dawn, Thoth, RWS, etc.)
- Cultural framework
- Historical period
- Personal interpretation framework

## Property Accumulation

Properties accumulate across both inherent and interpretive dimensions:

### Inherent Property Cascade
1. **Global**: Properties affecting all cards
2. **Card Type**: Properties affecting major or minor arcana
3. **Suit**: Properties affecting a specific suit (wands, cups, etc.)
4. **Number**: Properties affecting all cards of a specific number
5. **Court Type**: Properties affecting all court cards (page, knight, queen, king)
6. **Non-Court Type**: Properties affecting all non-court minor arcana cards (ace through ten)
7. **Card**: Properties affecting an individual card

### Interpretive Property Dimensions
- **Context**: Properties specific to a reading context (love, career, etc.)
- **Tradition**: Properties specific to a tarot tradition or system
- **Cultural**: Properties specific to a cultural interpretation
- **Historical**: Properties from a specific historical period

Properties accumulate independently along each dimension, allowing for rich, multi-layered interpretations.

## Layer Schema

Each esoterica layer is a single TOML file with this basic structure:

```toml
# File metadata
[meta]
schema_version = "1.0"
id = "golden-dawn-astrology"
name = "Golden Dawn Astrological Correspondences"
type = "correspondences"  # meanings, correspondences, context, tradition
version = "1.0"
author = "ledif"
license = "CC-BY-SA-4.0"
description = "Traditional Golden Dawn astrological correspondences for tarot"

# Inherent property levels
[global]
some_property = "value for all cards"

[major_arcana]
some_property = "value for all major arcana cards"

[minor_arcana]
some_property = "value for all minor arcana cards"

[minor_arcana.wands]
some_property = "value for all wands cards"

[numbers.four]
some_property = "value for all cards with number 4"

[non_court_cards]
some_property = "value for all non-court minor arcana"

[court_cards]
some_property = "value for all court cards"

# Individual card properties
[cards.major_arcana.00]
property = "value specific to The Fool"

[cards.minor_arcana.wands.four]
property = "value specific to Four of Wands"

# Interpretive frameworks (separate from inherent properties)
[contexts.love.cards.minor_arcana.wands.four]
upright = "Meaning in love context"
reversed = "Reversed meaning in love context"

[traditions.golden_dawn.cards.minor_arcana.wands.four]
decan = "third decan of aries"
planetary_ruler = "venus"
```

## Collection Files

A collection file can reference multiple layer files:

```toml
[meta]
schema_version = "1.0"
id = "standard-tarot-collection"
name = "Standard Tarot Collection"
type = "collection"
version = "1.0"
author = "ledif"

# Referenced layers (using relative or absolute paths)
[layers]
files = [
  "meanings/base.toml",
  "correspondences/astrology.toml",
  "contexts/love.toml"
]

# Card mappings for non-standard decks
[card_mappings]
"minor_arcana.torches.prince" = "minor_arcana.wands.knight"
"minor_arcana.disks.princess" = "minor_arcana.pentacles.page"
"major_arcana.adjustment" = "major_arcana.11"

# Custom cards not in standard decks
[custom_cards.major_arcana.happy_squirrel]
name = "The Happy Squirrel"
keywords = ["surprise", "playfulness", "hidden resources"]
upright_meaning = "Unexpected resources appearing when needed"
reversed_meaning = "Missing opportunities due to lack of awareness"
```

## Layer Types

### Base Meanings

```toml
# ~/.local/share/tarot/esoterica/meanings/base.toml
[meta]
schema_version = "1.0"
id = "base-meanings"
name = "Base Tarot Meanings"
type = "meanings"
version = "1.0"

[global]
reading_approach = "Intuitive, focusing on personal resonance"

[major_arcana]
theme = "Major life lessons and archetypal energies"

[minor_arcana]
theme = "Day-to-day experiences and practical matters"

[minor_arcana.wands]
element = "Fire"
theme = "Energy, passion, creativity, and action"
keywords = ["creativity", "energy", "passion", "inspiration"]

[non_court_cards]
type = "Situations and events"

[court_cards]
type = "People, personalities, or aspects of self"

[numbers.four]
meaning = "Stability, structure, and foundation"
keywords = ["stability", "structure", "foundation", "security"]

[cards.minor_arcana.wands.four]
name = "Four of Wands"
keywords = ["celebration", "harmony", "homecoming", "community"]
upright_meaning = "Celebration, harmony, marriage, home, and community"
reversed_meaning = "Lack of support, transience, home conflicts"
```

### Context Interpretations

```toml
# ~/.local/share/tarot/esoterica/contexts/love.toml
[meta]
schema_version = "1.0"
id = "love-context"
name = "Love Reading Interpretations"
type = "context"
version = "1.0"

[global]
context = "Love and relationships"
focus = "Romantic connections and partnerships"

# Context-specific interpretations use a separate path
[contexts.love.cards.minor_arcana.wands.four]
upright = "Celebration of relationship milestones, harmony in home life"
reversed = "Relationship instability, lack of shared celebration"
advice = "Create moments of joy and celebration in your relationship"
singles = "Social gatherings may lead to romantic opportunities"
couples = "Celebrate your achievements as a couple, build your future home"
```

### Tradition-Specific Interpretations

```toml
# ~/.local/share/tarot/esoterica/traditions/golden_dawn.toml
[meta]
schema_version = "1.0"
id = "golden-dawn"
name = "Golden Dawn System"
type = "tradition"
version = "1.0"

[global]
system = "Hermetic Order of the Golden Dawn"
key_texts = ["Liber T", "The Book of Thoth"]

# Tradition-specific correspondences use a separate path
[traditions.golden_dawn.cards.minor_arcana.wands.four]
decan = "third decan of aries"
planetary_ruler = "venus"
hebrew_letter = "none"
title = "Completion"
esoteric_meaning = "The establishment of a matter completed and perfected"
```

## Implementation Guidelines

### Property Accumulation Behavior

Properties accumulate across inherent levels and interpretive dimensions:

1. **List Properties**: Keywords, associations, etc. accumulate across all applicable levels and dimensions
   ```toml
   [minor_arcana.wands]
   keywords = ["passion", "creativity"]
   
   [numbers.four]
   keywords = ["stability", "structure"]
   
   [cards.minor_arcana.wands.four]
   keywords = ["celebration", "homecoming"]
   
   [contexts.love.cards.minor_arcana.wands.four]
   keywords = ["relationship milestone", "domestic harmony"]
   
   # When resolved for a love reading, the Four of Wands keywords would include all of these
   ```

2. **Text Properties**: Applications may either:
   - Concatenate with clear separation
   - Present as separate entries with sources labeled
   - Present the most specific while making others available on request

3. **Special Cases**: Some properties like "name" should take the most specific value rather than combining

### Creating Compatible Layers

1. **Respect Property Types**: Understand the distinction between inherent and interpretive properties.
2. **Design for Accumulation**: Create properties that work well when combined with others.
3. **Use Canonical IDs**: Be consistent with the deck specification's canonical IDs.
4. **Include Metadata**: Always include complete metadata in each file.

### Application Integration

Applications should:

1. **Support Multi-dimensional Access**: Allow users to select both inherent properties and interpretive frameworks.
2. **Combine Compatible Layers**: Allow mixing of layers from different sources.
3. **Present Layered Information**: Design interfaces that reveal the accumulated layers of meaning.
4. **Maintain Clear Sources**: Indicate where specific interpretations come from.

### Example: Multi-dimensional Access to the 4 of Wands

An application might present the 4 of Wands with these flexible viewing options:

```
4 OF WANDS
---------------
Base Meaning: Celebration, harmony, marriage, home, and community

View by Context:
- [Love] Celebration of relationship milestones, harmony in home life
- [Career] Recognition for team efforts, workplace harmony
- [Spiritual] Creating sacred space, establishing spiritual foundations

View by Tradition:
- [RWS] Completion of a phase, celebration, homecoming
- [Golden Dawn] Third decan of Aries, ruled by Venus, "Completion"
- [Thoth] "Completion," the establishment of harmony through effort

Element: Fire
Number Significance: Stability, structure, foundation
```

This approach allows users to explore the card from multiple perspectives while maintaining a clear distinction between inherent properties and various interpretive frameworks.

## Full 4 of Wands Example

This demonstrates how properties from multiple files would accumulate for the 4 of Wands:

### Base Meanings File
```toml
[meta]
schema_version = "1.0"
id = "base-meanings"
type = "meanings"

[global]
reading_approach = "Intuitive"

[minor_arcana]
theme = "Day-to-day experiences"
keywords = ["practical", "everyday"]

[minor_arcana.wands]
element = "fire"
energy = "passionate, creative, active"
keywords = ["action", "passion", "creativity"]

[numbers.four]
energy = "stable, structured"
keywords = ["stability", "structure", "foundation"]

[cards.minor_arcana.wands.four]
name = "Four of Wands"
keywords = ["celebration", "homecoming", "community"]
upright_meaning = "Celebration of milestones, harmony in the home"
reversed_meaning = "Lack of harmony, transience, instability"
```

### Love Context File
```toml
[meta]
schema_version = "1.0"
id = "love-context"
type = "context"

[contexts.love.global]
focus = "Romantic connections and partnerships"

[contexts.love.cards.minor_arcana.wands.four]
upright = "Happy home life, celebration of relationship milestones"
reversed = "Domestic tension, unstable relationship foundations"
keywords = ["relationship milestone", "domestic harmony"]
```

### Golden Dawn Tradition File
```toml
[meta]
schema_version = "1.0"
id = "golden-dawn"
type = "tradition"

[traditions.golden_dawn.global]
system = "Hermetic Order of the Golden Dawn"

[traditions.golden_dawn.cards.minor_arcana.wands.four]
decan = "third decan of aries"
planetary_ruler = "venus"
title = "Completion"
keywords = ["established strength", "perfection", "completion"]
```

When resolved for the 4 of Wands in a love reading using Golden Dawn tradition, this would accumulate into:

- **Core Properties** (from inherent card attributes):
  - **name**: "Four of Wands"
  - **element**: "fire"
  - **inherent keywords**: ["practical", "everyday", "action", "passion", "creativity", "stability", "structure", "foundation", "celebration", "homecoming", "community"]
  - **upright_meaning**: "Celebration of milestones, harmony in the home"
  - **reversed_meaning**: "Lack of harmony, transience, instability"

- **Love Context Layer** (from interpretive context):
  - **context**: "Love and relationships"
  - **context keywords**: ["relationship milestone", "domestic harmony"]
  - **context upright**: "Happy home life, celebration of relationship milestones"
  - **context reversed**: "Domestic tension, unstable relationship foundations"

- **Golden Dawn Layer** (from interpretive tradition):
  - **tradition**: "Hermetic Order of the Golden Dawn"
  - **tradition keywords**: ["established strength", "perfection", "completion"]
  - **decan**: "third decan of aries"
  - **planetary_ruler**: "venus"
  - **title**: "Completion"

## Validation Rules

Components should be validated for:

1. **Required Metadata**: Ensure each file contains complete metadata.
2. **Schema Compliance**: Verify files follow the correct structure with proper separation between inherent and interpretive properties.
3. **Canonical IDs**: Check that all card references use canonical IDs.
4. **Compatible Versions**: Verify that the schema version is supported.

## Future Considerations

1. **Spread Definitions**: Standard and custom spread layouts.
2. **Non-Western Systems**: Support for different cultural traditions.
3. **Interactive Elements**: Affirmations, journaling prompts, etc.
4. **Media References**: Literary, film, and cultural references.
5. **Historical Evolution**: Development of meanings over time.

## Conclusion

This specification provides a structured, modular system for tarot interpretations. By clearly separating inherent card properties from interpretive frameworks, it allows for a more organized and flexible approach to tarot esoterica. The accumulation of properties across multiple dimensions enables rich, layered meanings while maintaining clarity about the source and nature of each interpretation.