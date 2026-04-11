# lyrics-frameworks-skill

A library of reusable structural frameworks extracted from Chinese-language pop songs, used with the [lyrics-framework](https://github.com/chen-da-pang/lyrics-framework) Claude Code skill to generate new lyrics.

## What's in here

Each framework captures the structural skeleton of a song — segment roles, line counts, rhyme logic, and fill constraints — without storing the original lyrics. The framework can then be used to write entirely new songs with different themes.

This library works as the data layer for the `lyrics-framework` skill. The skill handles analysis and generation; this repo stores the results.

## Framework library

| Framework ID | Segments | Lines | Structure highlights |
|---|---|---|---|
| wenwendexingfu | 11 | — | Standard 3-round baseline, pre-chorus + dual chorus |
| xiaoxinyuan (圆) | 7 | 43 | Cantonese, dual-verse symmetry, dual-Hook alternating |
| congbuzhudongshiruo | 9 | 66 | Dual chorus, bridge, 转折词 pre-chorus signal |
| lianren | 9 | 43 | Pre-chorus before chorus (non-standard), stutter pre-chorus |
| huanxiangzhongdexingxing | 9 | 36 | Cantonese, R2 skips pre-chorus, single chorus |
| yuguohoudefengjing | 15 | 41 | Post-chorus, dual-Hook + title-line remote rhyme |
| yibanyiban | 8 | 88 | Rap structure, R2 has no chorus, stacked-syllable chorus |
| womeizhuanshen | 9 | 86 | Inverted structure (chorus opens), English Hook, call-back ending |

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
