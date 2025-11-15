# Sonoxus Lyrics Format (SXF)

[![Specification](https://img.shields.io/badge/Specification-v1.0-blue.svg)](/SPECIFICATION.md)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](/LICENSE)

**SXF** is an open, JSON-based universal format for storing and synchronizing song lyrics. It's designed to be simple enough for user-generated content (UGC) and powerful enough for licensed, rich-lyrics APIs.

It elegantly scales from simple, unsynchronized text to complex, syllable-by-syllable synchronized lyrics for multiple vocalists.

## Why SXF?

Current lyrics formats are lacking:
* **.LRC** is widely supported but outdated. It only supports line-by-line timestamps and has no standard for translations or multiple singers.
* **Proprietary JSON formats** (from services like Musixmatch, Spotify, Apple Music) are powerful but are closed-source, complex, and not designed for interoperability or community content.

SXF aims to be the single, open standard that bridges this gap.

## Key Features

* **Three Levels of Sync:**
  1.  **Unsynced:** Simple plain text lines.
  2.  **Line-Synced:** Start and end timestamps for each line (Karaoke-style).
  3.  **Rich-Synced:** Timestamps for every word or syllable.
* **Song Structure:** Semantically define verses, choruses, bridges, and more.
* **Multiple Singers & Roles:** Assign lines to different vocalists (duets) and define their roles (e.g. `main`, `background`).
* **Semantic Details:** Supports `emphasis` (bold/italic) per-segment and `translations` per-line.
* **JSON Native:** Fully compatible with JSON, making it trivial to parse in any language (JavaScript, Python, Java, etc.).
* **Flexible & Robust:** Gracefully handles files with *only* metadata (e.g. for instrumental tracks) by making `lyrics` optional.

## Examples

### 1. Simple (Unsynced)

The most basic format.

```json
{
  "version": "1.0",
  "meta": {
    "title": "Twinkle Twinkle Little Star",
    "lang": "en"
  },
  "lyrics": [
    { "text": "Twinkle, twinkle, little star" },
    { "text": "How I wonder what you are" },
    { "text": "Up above the world so high" },
    { "text": "Like a diamond in the sky" }
  ]
}
```

### 2. Line-Synced (with Translation)

Ideal for standard karaoke-style display.

```json
{
  "version": "1.0",
  "meta": {
    "title": "Let It Go",
    "lang": "en"
  },
  "lyrics": [
    {
      "start": 10500,
      "end": 12800,
      "text": "The snow glows white on the mountain tonight",
      "translations": [
        { "lang": "ru", "text": "Снег блестит на горе сегодня ночью" }
      ]
    },
    {
      "start": 13100,
      "end": 15200,
      "text": "Not a footprint to be seen",
      "translations": [
        { "lang": "ru", "text": "Не видно ни следа" }
      ]
    }
  ]
}
```

### 3. Rich-Synced (Syllable-level & Singers)

The most powerful format, for "Apple Music" style animated lyrics and duets.

```json
{
  "version": "1.0",
  "meta": {
    "title": "A Whole New World",
    "artist": "Aladdin & Jasmine",
    "singers": [
      { "id": "aladdin", "name": "Aladdin", "role": "main" },
      { "id": "jasmine", "name": "Jasmine", "role": "main" }
    ],
    "lang": "en"
  },
  "lyrics": [
    {
      "start": 20100,
      "end": 22500,
      "text": "I can show you the world",
      "singer": "aladdin",
      "segments": [
        { "text": "I ", "start": 20100, "end": 20300 },
        { "text": "can ", "start": 20300, "end": 20600 },
        { "text": "show ", "start": 20600, "end": 20900 },
        { "text": "you ", "start": 20900, "end": 21200 },
        { "text": "the ", "start": 21200, "end": 21400 },
        {
          "text": "world",
          "start": 21400,
          "end": 22500,
          "emphasis": "bold"
        }
      ]
    },
    {
      "start": 23000,
      "end": 25800,
      "text": "Shining, shimmering, splendid",
      "singer": "jasmine"
    }
  ]
}
```

## 4. Song Structure (structure)

The structure key is a top-level array that "points" to the lyrics array by index to define sections.

```json
{
  "version": "1.0",
  "meta": {
    "title": "Twinkle Twinkle Little Star"
  },
  "lyrics": [
    { "text": "Twinkle, twinkle, little star" }, 
    { "text": "How I wonder what you are" }, 
    { "text": "Up above the world so high" }, 
    { "text": "Like a diamond in the sky" } 
  ],
  "structure": [
    {
      "type": "verse",
      "label": "Verse 1",
      "startIndex": 0,
      "endIndex": 1
    },
    {
      "type": "verse",
      "label": "Verse 2",
      "startIndex": 2,
      "endIndex": 3
    }
  ]
}
```

Even more examples can be found in the **[Examples](/examples)** folder.

## Specification & Validation

This format is formally defined using JSON Schema. This allows anyone to validate their `.sxf` files to ensure they are correct.

* **[Full Specification](/SPECIFICATION.md)**: A human-readable guide to every field.
* **[JSON Schema](/schemas/sxf.v1.schema.json)**: A machine-readable schema for validation.

## Ecosystem & Tools

A format is only as good as its tools. The immediate roadmap is focused on building a high-quality, open-source ecosystem for SXF.

* **SXF Editor** (In Development): A full-featured, open-source web (React) editor for creating and synchronizing `.sxf` files from scratch.
* **SXF Player** (In Development): A lightweight React component to easily parse and render SXF files with "Apple Music" style visualizations.
* **[LRC to SXF Converter]** (Planned): A simple online tool to migrate the huge library of existing `.lrc` files to the rich SXF format.
* **[SXF Validator]** (Planned): A schema-based validator, which will be available as an online tool.

## Contributing

This is an open project, and contributions are welcome! Whether you're helping to refine the specification, building a tool, or adding examples, your help is appreciated.

Please read our [CONTRIBUTING.md](/CONTRIBUTING.md) guide to get started.

## License

SXF is open-source and released under the **[Apache 2.0 License](/LICENSE)**.