# AndroidAPS string context

Where each AndroidAPS string appears in the app and what it means, for translators. Context
Translator imports these files and shows them next to each string and in its AI prompts.

## Layout

One JSON file per strings file of AndroidAPS, at the same path with `.json` for `.xml`:

```
context/app/src/main/res/values/strings.json
context/plugins/smoothing/src/androidMain/res/values/strings.json
```

## Format

Schema: `ctx-scan schema` prints it (`crates/core/schemas/string-context.schema.json` in
Context Translator).

```json
{
  "schema_version": 1,
  "source_path": "app/src/main/res/values/strings.xml",
  "repo_commit": "AndroidAPS commit the entries were written against",
  "generated_by": "claude-code skill aaps-string-context",
  "entries": [
    {
      "key": "bolus_delivered",
      "ui_location": "Overview > bolus progress dialog",
      "kind": "dialog_text",
      "description": "Shown when the pump confirms the bolus finished delivering.",
      "related_keys": ["bolus_delivering"],
      "confidence": 0.8,
      "usages_hash": "from ctx-scan",
      "usages": [{ "path": "…/BolusProgressDialog.kt", "line": 120 }],
      "manual_note": "Written by translators in the app"
    }
  ]
}
```

- `usages_hash` fingerprints where the key was used when the entry was written. When the code
  moves on, the app marks the entry stale and the skill lists it again.
- `manual_note` is written by translators in the app, one commit per change with an
  `Edited-by:` line. Tools that regenerate an entry keep it.

## Updating

Run the `aaps-string-context` skill in Claude Code from the Context Translator repo, one module
at a time. It is resumable: entries whose usages did not change are skipped.

## Documentation context

What each page of the AndroidAPS manual covers, its sections, and the app strings each section
names. One JSON file per Markdown page of AndroidAPSdocs, under `docs/` at the page's path with
`.json` for `.md`:

```
docs/docs/EN/Configuration/Config-Builder.json
```

Schema: `ctx-scan docs schema` (`crates/core/schemas/doc-context.schema.json` in Context
Translator).

```json
{
  "schema_version": 1,
  "doc_path": "docs/EN/Configuration/Config-Builder.md",
  "repo_commit": "AndroidAPSdocs commit",
  "app_commit": "AndroidAPS commit the keys were resolved against",
  "generated_by": "claude-code skill aaps-doc-context",
  "summary": "What the reader does on this page.",
  "features": ["Configuration"],
  "screens": [{ "ui_location": "Main menu > Configuration", "file": "…/strings.xml", "key": "nav_configuration" }],
  "sections": [
    {
      "heading": "Pump",
      "anchor": "pump",
      "source_hash": "from ctx-scan",
      "summary": "Choosing the pump driver.",
      "dialogs": ["Configuration > Pump"],
      "mentions": [{ "file": "core/ui/src/androidMain/res/values/strings.xml", "key": "configbuilder_pump", "as_written": "Pump", "confidence": 0.9 }]
    }
  ],
  "manual_note": null
}
```

- `source_hash` fingerprints a section's English when it was described; when the page changes,
  the section is listed again.
- The documentation projects of the app import these files after each context sync and show
  them next to each segment.

Write them with the `aaps-doc-context` skill and recheck them with `aaps-doc-recheck`.
`docs-index.json` and `usage-index.json` are work files: do not commit them.
