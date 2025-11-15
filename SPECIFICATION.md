# SXF Lyrics Format Specification
**Version: 1.0**

This document provides a human-readable specification for the Sonoxus Lyrics Format (SXF). It is intended for developers who wish to create parsers, generators, or editors that support the `.sxf` format.

This specification is a companion to the formal [JSON Schema](/schemas/sxf.v1.schema.json).

## Guiding Principles

1.  **JSON Native:** An SXF file must be a valid JSON object.
2.  **UTF-8 Encoding:** All text must be encoded in UTF-8.
3.  **Time Units:** All time-related fields (`start`, `end`) must be expressed as **integers in milliseconds**.
4.  **Extensibility:** Parsers must ignore any properties they do not recognize. This allows for future non-breaking additions to the spec.

## 1. Top-Level Object

The root of an SXF file is a JSON object with two top-level properties.

| Property | Type | Required | Description | Example |
| :--- | :--- | :--- | :--- | :--- |
| `version` | `string` | No | The version of the SXF specification being used. Currently only `v1.0` is supported. If not specified, the parser will try to determine it. | `"1.0"` |
| `meta` | [Meta Object](#2-the-meta-object) | **Yes** | Contains all metadata about the song (title, artist, etc.). | See [Meta Object](#2-the-meta-object) |
| `lyrics` | Array of [lyricLine Objects](#3-the-lyricline-object) | No | An array containing all lines of the song's lyrics. | See [lyricLine Objects](#3-the-lyricline-object) |
| `structure` | Array of [structure Objects](#8-the-structure-object) | No | An array of objects defining the song's structure (e.g. verses, choruses) by grouping lines from the `lyrics` array. | See [Structure Objects](#8-the-structure-object) |

---

## 2. The `meta` Object

The `meta` object contains descriptive information about the track.

| Property | Type | Required | Description | Example |
| :--- | :--- | :--- | :--- | :--- |
| `title` | `string` | **Yes** | The title of the song. | `"A Whole New World"`
| `artist` | `string` OR `Array<string>` | No | The primary artist(s). Can be one string or an array for collaborations. | `"Aladdin & Jasmine"` or `["Aladdin", "Jasmine"]` |
| `album` | `string` | No | The name of the album this song belongs to. | `"Aladdin (Original Motion Picture Soundtrack)"` |
| `artwork` | `string (uri)` OR [embeddedArtwork Object](#4-the-embeddedartwork-object) | No | Cover art for the song. Can be a URL or an embedded Base64 object. | `"https://example.com/artwork.png"` |
| `lang` | `string` | No | The original language of the song in the [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag) language code. | `"en"` |
| `isrc` | `string` | No | The ISRC code of the song. | `"US-ABC-21-12345"` |
| `singers` | Array of [Singer Objects](#5-the-singer-object) | No | An array defining the different vocalists for duets, backing vocals, etc. | See [Singer Object](#5-the-singer-object) |

---

## 3. The `lyricLine` Object

The `lyrics` array is composed of `lyricLine` objects. Each object represents a single line of text and its associated timing and metadata.

| Property | Type | Required | Description | Example |
| :--- | :--- | :--- | :--- | :--- |
| `text` | `string` | **Yes** | The full, plain text of the line. This is used as a fallback if rich `segments` are not supported by the parser. | `"I can show you the world"` |
| `start` | `integer` | No | The start time of the line in **milliseconds**. | `20100` |
| `end` | `integer` | No | The end time of the line in **milliseconds**. | `22500` |
| `singer` | `string` OR `Array<string>` | No | The `id` (or an array of `id`s) of the singer(s) from `meta.singers` performing this line. | `aladdin` or `["aladdin", "jasmine"]`|
| `translations` | Array of [translation Objects](#6-the-translation-object) | No | An array of line-by-line translations. | See [Translation Object](#6-the-translation-object) |
| `segments` | Array of [segment Objects](#7-the-segment-object) | No | An array of "rich" segments (words/syllables) for precise synchronization. | See [Segment Objects](#7-the-segment-object) |

**Note on Synchronization:**
* **Unsynced:** The `lyricLine` object only contains `text`.
* **Line-Synced:** The object contains `text`, `start`, and `end`.
* **Rich-Synced:** The object contains `text` (as fallback) and `segments`. The `start` and `end` fields may be omitted, as they can be inferred from the first and last segment.

---

## 4. The `embeddedArtwork` Object

Used inside `meta.artwork` to embed an image file directly into the SXF file.

| Property | Type | Required | Description | Example |
| :--- | :--- | :--- | :--- | :--- |
| `format` | `string` | **Yes** | The encoding format. Currently, only `"base64"` is supported. | `"base64"` |
| `mimeType` | `string` | **Yes** | The MIME type of the image (e.g. `image/jpeg`, `image/png`). | `"image/png"` |
| `data` | `string` | **Yes** | The Base64-encoded image data as a string. | `"iVBORw0KGgo..."` |

---

## 5. The `singer` Object

Used inside `meta.singers` to define a vocalist or role.

| Property | Type | Required | Description | Example |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `string` | **Yes** | A unique identifier for the singer (e.g. `elsa`, `vocal1`). This `id` is referenced by `lyricLine.singer`. | `"aladdin"` |
| `name` | `string` | No | A human-readable display name (e.g. "Elsa", "Backing Vocals"). | `"Aladdin"` |
| `role` | `string` | No | Role of the singer for rendering purposes. Can be either `"main"` (default) or `"background"` | `"main"` |

**Note on Roles:**

To handle cases where one performer sings multiple parts (e.g. main vocals and their own background echo), define a separate singer object in `meta.singers` for each part. These objects can share the same name but must have unique ids and distinct role properties. Example:
```json
[
  {
    "id": "elsa",
    "name": "Elsa",
    "role": "main"
  },
  {
    "id": "elsa_bg",
    "name": "Elsa",
    "role": "background"
  }
]
```

---

## 6. The `translation` Object

Used inside `lyricLine.translations` to provide a translated version of the line.

| Property | Type | Required | Description | Example |
| :--- | :--- | :--- | :--- | :--- |
| `lang` | `string` | **Yes** | The [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag) language code (e.g. `en`, `ru`, `es-419`). | `"es"` |
| `text` | `string` | **Yes** | The translated text for the line. | `"Yo te puedo mostrar el mundo"` |

---

## 7. The `segment` Object

Used inside `lyricLine.segments` to define a single synchronized word or syllable.

| Property | Type | Required | Description | Example |
| :--- | :--- | :--- | :--- | :--- |
| `text` | `string` | **Yes** | The text content of this specific segment. | `"Hello"` |
| `start` | `integer` | **Yes** | The start time of the segment in **milliseconds**. | `20100` |
| `end` | `integer` | **Yes** | The end time of the segment in **milliseconds**. | `20300` |
| `emphasis` | `string` | No | Specifies emphasis for the segment. Supported values are `italic` (rendered as italic) or `bold` (rendered as bold). If omitted, emphasis is `normal`. | `"bold"` |

---

## 8. The `structure` Object

Used as a top-level property to separate the song in segments (verses).

| Property | Type | Required | Description | Example |
| :--- | :--- | :--- | :--- | :--- |
| `type` | `string` | **Yes** | The standardized, machine-readable type of the section. Available types are `intro`, `verse`, `pre_chorus`, `chorus`, `post_chorus`, `bridge`, `hook`, `solo`, `interlude`, `spoken`, `outro`, `other`. | `"chorus"` |
| `label` | `string` | No | A human-readable label. | `"Verse 1"` |
| `startIndex` | `integer` | **Yes** | The 0-based index of the first lyricLine in this section. | `0` |
| `endIndex` | `integer` | **Yes** | The 0-based index of the last lyricLine in this section (inclusive). | `3` |

---

## Full "Kitchen Sink" Example

Here is an example `.sxf` file demonstrating all features of the specification.

```json
{
  "version": "1.0",
  "meta": {
    "title": "A Whole New World",
    "artist": "Aladdin & Jasmine",
    "singers": [
      {
        "id": "aladdin",
        "name": "Aladdin",
        "role": "main"
      },
      {
        "id": "jasmine",
        "name": "Jasmine",
        "role": "main"
      }
    ],
    "lang": "en",
    "isrc": null,
    "album": null,
    "artwork": null
  },
  "lyrics": [
    {
      "start": 20100,
      "end": 22500,
      "text": "I can show you the world",
      "singer": "aladdin",
      "segments": [
        {
          "text": "I ",
          "start": 20100,
          "end": 20300
        },
        {
          "text": "can ",
          "start": 20300,
          "end": 20600
        },
        {
          "text": "show ",
          "start": 20600,
          "end": 20900
        },
        {
          "text": "you ",
          "start": 20900,
          "end": 21200
        },
        {
          "text": "the ",
          "start": 21200,
          "end": 21400
        },
        {
          "text": "world",
          "emphasis": "bold",
          "start": 21400,
          "end": 22500
        }
      ]
    },
    {
      "start": 23000,
      "end": 25800,
      "text": "Shining, shimmering, splendid",
      "singer": "jasmine",
      "segments": [
        {
          "text": "Shi",
          "start": 23000,
          "end": 23300
        },
        {
          "text": "ning, ",
          "start": 23300,
          "end": 23800
        },
        {
          "text": "shim",
          "start": 23800,
          "end": 24200
        },
        {
          "text": "mering, ",
          "start": 24200,
          "end": 24900
        },
        {
          "text": "splen",
          "start": 24900,
          "end": 25400
        },
        {
          "text": "did",
          "start": 25400,
          "end": 25800
        }
      ]
    }
  ],
  "structure": [
    {
      "type": "verse",
      "label": "Verse 1",
      "startIndex": 0,
      "endIndex": 1
    }
  ]
}
```