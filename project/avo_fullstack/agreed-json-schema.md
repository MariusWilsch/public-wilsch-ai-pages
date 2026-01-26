---
publish: true
---

# AVO Recipe Schema - Agreed Specification
[[client-avo]]

Schema agreed in Jan 15, 2026 meeting with Mattis (AVO), Thomas Erhard, Heiko Schmitz.

## Part 1: JSON with Semantic Comments

```json
// FORMAT: JSONL (one recipe per line, no wrapper array)
{
  "article_number": "RZ6001",           // Recipe ID - unique identifier for this sauce

  "materials": [                         // Array of exploded raw materials
                                         // RULE: sum(percentage) = 100%
                                         // RULE: Only raw materials, never Vormischungen
    {
      "article_number": "M-001",         // Raw material ID
      "percentage": 25.0,                // Cumulative % of this material in final recipe

      "Ebenen": {                        // Provenance breakdown (OPTIONAL)
                                         // RULE: sum(Ebenen percentages) = parent percentage
        "01": {                          // Level key (2-digit string)
          "article_number": "V-001",     // Source: which Vormischung contributed this
          "percentage": 10               // How much came from V-001
        },
        "03": {
          "article_number": "V-002",
          "percentage": 15               // 10 + 15 = 25 ✓
        }
      }
    },
    {
      "article_number": "M-002",
      "percentage": 60.0                 // No Ebenen = added directly (level 1)
    },
    {
      "article_number": "M-003",
      "percentage": 15.0
    }
    // Total: 25 + 60 + 15 = 100% ✓
  ]
}
```

## Path Reference

```
article_number                          → string   Recipe ID (unique)
materials                               → array    Exploded raw materials list
materials[].article_number              → string   Raw material ID (never V)
materials[].percentage                  → number   Cumulative % (all sources combined)
materials[].Ebenen                      → object   Provenance breakdown (optional)
materials[].Ebenen["01"]                → object   Level entry
materials[].Ebenen["01"].article_number → string   Vormischung source ID
materials[].Ebenen["01"].percentage     → number   % contributed from this V
```

## Part 2: JSON Schema (Machine-Readable)

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "AVO Recipe",
  "description": "JSONL format - one recipe per line",
  "type": "object",
  "required": ["article_number", "materials"],
  "properties": {
    "article_number": {
      "type": "string",
      "description": "Unique recipe identifier"
    },
    "materials": {
      "type": "array",
      "minItems": 1,
      "items": {
        "type": "object",
        "required": ["article_number", "percentage"],
        "properties": {
          "article_number": { "type": "string" },
          "percentage": { "type": "number", "minimum": 0, "maximum": 100 },
          "Ebenen": {
            "type": "object",
            "additionalProperties": {
              "type": "object",
              "required": ["article_number", "percentage"],
              "properties": {
                "article_number": { "type": "string" },
                "percentage": { "type": "number", "minimum": 0, "maximum": 100 }
              }
            }
          }
        }
      }
    }
  }
}
```

## Meta Level Rules

| Rule | Type | Description |
|------|------|-------------|
| **M1** | Format | JSONL: one recipe per line, no wrapper array |
| **M2** | Exploded | All Vormischungen broken down to raw materials |
| **M3** | Sum 100% | `sum(materials[].percentage) = 100` |
| **M4** | Raw only | `materials[].article_number` = raw materials only |
| **M5** | Provenance | `Ebenen` shows WHERE each % came from |
| **M6** | Level sum | `sum(Ebenen[].percentage) = parent percentage` |
| **M7** | Optional | `Ebenen` omitted when material added directly |
| **M8** | Source | `Ebenen[].article_number` = Vormischung ID (V-xxx) |

## Visualization

![Schema visualization](agreed-schema-visualization.png)

*Generated with [JSON Viewer Tools](https://jsonviewer.tools/editor)*

## Source

- Meeting: Jan 15, 2026 with Mattis (AVO)
- Transcript: [Fireflies](https://app.fireflies.ai/view/01KEXRBAF5495NKTGZ5N6FHFVE)
- Screenshot evidence: Mattis screen share showing Ebenen structure
- Visualization tool: [jsonviewer.tools/editor](https://jsonviewer.tools/editor)
