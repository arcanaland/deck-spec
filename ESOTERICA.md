# Tarot Esoterica Specification v0.1

**Maintained by:** [Arcana Land](https://github.com/arcanaland)

## Overview

This specification defines a **faceted classification** system for tarot esoterica—interpretative and symbolic information associated with tarot decks—using a **modular ontology** approach. Unlike the [deck specification](https://github.com/arcanaland/deck-spec), which focuses on presentation, this specification focuses on meaning and correspondence systems.

The specification is built around the following principles:
- **Faceted Classification**: Interpretations and correspondences are treated as independent dimensions (or "facets") that can be combined to create rich, multi-layered meanings.
- **Modular Ontology**: Each layer of esoterica is stored in its own file, forming a modular and extensible structure. These layers can interoperate while maintaining independence.
- **Accumulating Properties**: Meanings and properties combine across levels, building a rich interpretation.
- **Separation of Inherent vs. Interpretive Properties**: A clear distinction is made between card attributes and interpretive frameworks.
- **Compatibility**: Designed to interoperate with the deck specification's canonical IDs.

---

## Key Concepts

### Faceted Classification
Each **facet** represents a conceptual category of tarot esoterica, such as inherent properties, interpretive contexts, or traditions. Facets are implemented as **layers** and identified through the `type` field in layer metadata.

For example:
- The **context** facet includes layers like `facet.context.love`, which define meanings for specific reading contexts.
- The **tradition** facet includes layers like `facet.tradition.golden_dawn`, which define properties tied to specific tarot traditions.

### Layers
A **layer** represents a self-contained file within the modular ontology. Each layer corresponds to a single interpretive or inherent facet of tarot esoterica, such as numerology, context-specific meanings, or a cultural tradition. Layers are:
- **Independent**: Each layer can function autonomously.
- **Composable**: Layers can be combined to form multi-dimensional interpretations.
- **Scoped**: Each layer is identified by a unique `type` field in its metadata (e.g., `facet.context.love`).

For example:
- A layer file with `type = "facet.context.love"` contains interpretations specific to love readings.
- A layer file with `type = "facet.tradition.golden_dawn"` contains properties specific to the Golden Dawn tradition.
---

## Schema Versioning

- This specification uses a **schema version number** to ensure compatibility and future extensibility.
- The schema version is specified within each esoterica file under the top-level section as `schema_version`.

## File Structure

Each **layer** is a standalone TOML file stored according to the XDG Base Directory Specification. Layers are identified by their **namespace**, which is derived from the `type` field in metadata. The recommended directory structure is:

```
$XDG_DATA_HOME/tarot/esoterica/{facet}/{layer}.toml
```

For example:
- `$XDG_DATA_HOME/tarot/esoterica/context/love.toml` for the "love" context layer.
- `$XDG_DATA_HOME/tarot/esoterica/tradition/golden_dawn.toml` for the Golden Dawn tradition layer.



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
- Tradition/system (Golden Dawn, Thoth, Rider-Waite-Smith, etc.)
- Cultural framework
- Historical period
- Personal interpretation framework

### Property Accumulation

Properties accumulate across both inherent and interpretive dimensions:

#### Inherent Property Cascade
1. **Global**: Properties affecting all cards
2. **Card Type**: Properties affecting major or minor arcana
3. **Suit**: Properties affecting a specific suit (wands, cups, etc.)
4. **Number**: Properties affecting all cards of a specific number
5. **Court Type**: Properties affecting all court cards (page, knight, queen, king)
6. **Non-Court Type**: Properties affecting all non-court minor arcana cards (ace through ten)
7. **Card**: Properties affecting an individual card

#### Interpretive Property Dimensions
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
default_language = "en"
id = "love-context"
name = "Love Context Interpretations"
type = "facet.context.love"
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


Each layer file must include the following metadata fields in a `[meta]` section:

| Field             | Description                                                                                 |
|--------------------|---------------------------------------------------------------------------------------------|
| `schema_version`   | Specifies the version of the esoterica schema being used (e.g., `1.0`).                    |
| `default_language` | The primary language for the file's content (default: `en`).                                |
| `id`               | A unique identifier for the layer (e.g., `love-context`).                                  |
| `name`             | A human-readable name for the layer (e.g., `Love Context Interpretations`).                |
| `type`             | The hierarchical namespace for the layer (e.g., `facet.context.love`, `facet.tradition.golden_dawn`). |
| `version`          | The version of the layer itself, for tracking updates (e.g., `1.0`).                       |
| `author`           | The creator or maintainer of the layer.                                                    |
| `license`          | The license governing the layer's content (e.g., `CC-BY-SA-4.0`).                          |
| `description`      | A brief explanation of the layer's purpose and scope.                                      |

Layers must include the `[meta]` section at the top of the file to ensure compatibility and clear identification.

The type field is dynamic and can include any category relevant to tarot correspondences. New categories should be descriptive and documented within the description field to ensure clarity and usability. Applications should handle unknown types gracefully by ignoring them or logging their presence for future updates. The `type` field in the metadata supports hierarchical namespaces to organize layers. For example, `context.love` specifies that the layer belongs to the `context` category and focuses on `love`. Collections can reference these namespaces directly, allowing dynamic resolution of files.


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

# Referenced layers 
[layers]
namespaces = [
  "context.love",
  "context.career",
  "tradition.golden_dawn",
  "astrology.zodiac"
]
```

Namespaces allow collections to reference categories of layers instead of individual files. Applications should resolve these namespaces to corresponding files dynamically.

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

### Astrology Correspondences

```toml
# ~/.local/share/tarot/esoterica/correspondences/astrology.toml
[meta]
schema_version = "1.0"
id = "astrology-correspondences"
name = "Astrological Correspondences"
type = "astrology"
version = "1.0"

[global]
system = "Traditional Western Astrology"

[cards.major_arcana.00]
zodiac = "Aquarius"
planetary_ruler = "Uranus"
house = "11th House"
```

---

### Numerology Correspondences

```toml
# ~/.local/share/tarot/esoterica/correspondences/numerology.toml
[meta]
schema_version = "1.0"
id = "numerology-correspondences"
name = "Numerology Correspondences"
type = "numerology"
version = "1.0"

[numbers.four]
meaning = "Foundation, stability, and structure"
life_path_number = 4

[cards.minor_arcana.wands.four]
numerological_significance = "Represents achieving stability through effort"
```

---

### Elemental Correspondences

```toml
# ~/.local/share/tarot/esoterica/correspondences/elements.toml
[meta]
schema_version = "1.0"
id = "elemental-correspondences"
name = "Elemental Correspondences"
type = "elements"
version = "1.0"

[global]
elements = ["Fire", "Water", "Earth", "Air"]

[minor_arcana.wands]
element = "Fire"
qualities = ["passion", "energy", "creativity"]
```

---

### Psychological Archetypes

```toml
# ~/.local/share/tarot/esoterica/correspondences/psychology.toml
[meta]
schema_version = "1.0"
id = "psychological-archetypes"
name = "Psychological Archetypes"
type = "psychology"
version = "1.0"

[major_arcana]
archetypes = [
  {"card": "The Fool", "archetype": "The Innocent", "shadow": "Naivety, recklessness"},
  {"card": "The Magician", "archetype": "The Creator", "shadow": "Manipulation, arrogance"}
]
```

---

### Mythology Layers

```toml
# ~/.local/share/tarot/esoterica/correspondences/mythology-greek.toml
[meta]
schema_version = "1.0"
id = "greek-mythology-correspondences"
name = "Greek Mythology Correspondences"
type = "mythology.greek"
version = "1.0"

[major_arcana]
system = "Greek Mythology"

[cards.major_arcana.00]
deity = "Dionysus"
myth = "The Fool's journey represents the divine madness of Dionysus"
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

Properties accumulate hierarchically within layers and across facets, allowing for rich, multi-dimensional interpretations.

#### Intra-Layer Accumulation
Within a single layer, properties cascade from general to specific levels:
1. **Global Properties**: Apply to all cards (e.g., `[global]`).
2. **Category Properties**: Apply to subsets of cards, such as `[major_arcana]` or `[minor_arcana.wands]`.
3. **Specific Card Properties**: Apply to individual cards (e.g., `[cards.minor_arcana.wands.four]`).

#### Inter-Layer Accumulation
Across layers, properties are combined from multiple facets:
- **Inherent Properties**: For example, `[numbers.four]` from a numerology layer.
- **Interpretive Properties**: For example, `[contexts.love.cards.minor_arcana.wands.four]` from a context layer.

The accumulation hierarchy ensures that properties from more specific layers (e.g., `[cards.minor_arcana.wands.four]`) override those from broader categories (e.g., `[numbers.four]`).

### Creating Compatible Layers

1. **Respect Property Types**: Understand the distinction between inherent and interpretive properties.
2. **Design for Accumulation**: Create properties that work well when combined with others.
3. **Use Canonical IDs**: Be consistent with the deck specification's canonical IDs.
4. **Include Metadata**: Always include complete metadata in each file.



When adding a new category (type), ensure it is descriptive, relevant to tarot correspondences, and documented within the description field in the meta section. Use namespaces (e.g., `mythology.greek`) for complex or multi-system categories. Applications should parse unknown type values without failing, either ignoring them or logging their presence for future updates.


### Conflict Resolution Rules

When properties overlap across layers, the following rules apply:

1. **Priority Order**:
   - **User-Defined Priority**: Applications should allow users to define the priority of layers (e.g., `facet.context.love > facet.tradition.golden_dawn`).
   - **Specific Overrides General**: Properties from specific layers (e.g., `[cards.minor_arcana.wands.four]`) override those from broader layers (e.g., `[numbers.four]`).

2. **Conflict Handling**:
   - Concatenate List Properties: Combine keywords, associations, or other list-based properties, and label their sources.
   - Preserve Most Specific Text Properties: For single-value fields like `name`, use the value from the most specific layer while retaining others for reference.

**Example**:
If the `Four of Wands` appears in both a numerology layer and a love context layer:
- **Keywords**: Combine (e.g., `["stability", "homecoming", "relationship milestone"]`).
- **Name**: Use the most specific value (e.g., `"Four of Wands"` from `[cards.minor_arcana.wands.four]`).

### Application Integration

Applications should:

1. **Support Multi-dimensional Access**: Allow users to select both inherent properties and interpretive frameworks.
2. **Combine Compatible Layers**: Allow mixing of layers from different sources.
3. **Present Layered Information**: Design interfaces that reveal the accumulated layers of meaning.
4. **Maintain Clear Sources**: Indicate where specific interpretations come from.

Applications should ignore unknown type values or categories gracefully. For example, if a file includes a type value of psychology that is not yet supported, the application should skip this layer without errors and log its presence for future compatibility. This ensures backward compatibility and allows the specification to evolve incrementally.

### Example: Multi-Dimensional Access to the 4 of Wands

An application might present the `Four of Wands` using accumulated properties from multiple layers:

```
4 OF WANDS
---------------
**Base Meaning**: Celebration, harmony, marriage, home, and community.

**Facets**:
- **Context: Love**: Celebration of relationship milestones, harmony in home life.
- **Context: Career**: Recognition for team efforts, workplace harmony.
- **Tradition: Golden Dawn**: Third decan of Aries, ruled by Venus, "Completion."
- **Element**: Fire.
- **Numerology**: Stability, structure, foundation.
```

Each facet represents a layer, and the accumulated properties provide a rich, multi-dimensional interpretation of the card.

## Validation Rules

Components should be validated for:

1. **Required Metadata**: Ensure each file contains complete metadata.
2. **Schema Compliance**: Verify files follow the correct structure with proper separation between inherent and interpretive properties.
3. **Canonical IDs**: Check that all card references use canonical IDs.
4. **Compatible Versions**: Verify that the schema version is supported.


## i18n Support

To future-proof the specification for internationalization (i18n) and localization (l10n), this version introduces non-intrusive i18n-ready features. English remains the default language, requiring no additional work for authors. Localization is optional and can be added incrementally.

Key i18n principles:
- **Default Language**: English (`en`) is assumed unless otherwise specified.
- **Lightweight Structure**: No additional complexity for single-language files.
- **Future-Proofing**: Supports translations and localized content without breaking compatibility.

To prepare for localization, properties can include language-specific variants. By default, properties are written in the `default_language`.

#### Example: Single-Language File (English Only)
```toml
[cards.major_arcana.00]
name = "The Fool"
description = "Represents new beginnings and spontaneity."
```

#### Example: Multilingual Extension
```toml
[cards.major_arcana.00]
name = "The Fool"
name.fr = "Le Fou"
description = "Represents new beginnings and spontaneity."
description.fr = "Représente de nouveaux départs et la spontanéité."
```

Applications should fall back to the `default_language` if a localized property is unavailable.


## Future Considerations

1. **Spread Definitions**: Standard and custom spread layouts.
2. **Non-Western Systems**: Support for different cultural traditions.
3. **Interactive Elements**: Affirmations, journaling prompts, etc.
4. **Media References**: Literary, film, and cultural references.
5. **Historical Evolution**: Development of meanings over time.


The specification is designed to support new categories dynamically. By allowing contributors to define new type values and documenting them through the description field, the specification can grow organically without requiring frequent version updates. This modular approach ensures it remains relevant to evolving interpretative and symbolic frameworks.

## Conclusion

This specification provides a structured, modular system for tarot interpretations. By clearly separating inherent card properties from interpretive frameworks, it allows for a more organized and flexible approach to tarot esoterica. The accumulation of properties across multiple dimensions enables rich, layered meanings while maintaining clarity about the source and nature of each interpretation.
