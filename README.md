# lyrics-frameworks-skill

A library of reusable structural frameworks extracted from Chinese-language pop songs, used with the [lyrics-framework](https://github.com/chen-da-pang/lyrics-framework) Claude Code skill to generate new lyrics.

## What's in here

Each framework captures the structural skeleton of a song — segment roles, line counts, rhyme logic, and fill constraints — without storing the original lyrics. The framework can then be used to write entirely new songs with different themes.

This library works as the data layer for the `lyrics-framework` skill. The skill handles analysis and generation; this repo stores the results.

## Framework library

8 frameworks covering standard 3-round pop, dual-chorus, rap structure, Cantonese pop, inverted structure, and more. Browse the `frameworks/` directory for details.

## File structure

Each framework directory contains:

```
frameworks/{song_id}/
├── framework.yaml          # Segment map, line annotations, rhyme analysis
├── framework-fillable.md   # Fill template with char counts and rhyme markers
└── lyrics-comparison.md    # (optional) Multi-model fill results and review
```

**`framework.yaml`** — machine-readable structure: segment roles, line char counts, rhyme 辙, Hook positions, round boundaries.

**`framework-fillable.md`** — human/AI fill template. Each line shows required char count and rhyme annotation (`[押主韵]` / `[散韵]` / `[掺韵]`). Compressed return mappings are explicit (e.g. `L19 ≈ L04`).

## Python package

`src/lyrics_framework_extraction/` — Python package for programmatic framework generation from annotated lyrics. Requires `PyYAML==6.0.2`.

```bash
pip install -r requirements.txt
```

## Adding a new framework

Use the `lyrics-framework` Claude Code skill (Sub-workflow A). After generation, commit and push:

```bash
git add frameworks/{song_id}/ frameworks/index.yaml
git commit -m "Add framework: {song_name}"
git push
```

## Related

- **Skill**: [lyrics-framework](https://github.com/chen-da-pang/lyrics-framework) — the Claude Code skill that uses this library

## License

MIT
