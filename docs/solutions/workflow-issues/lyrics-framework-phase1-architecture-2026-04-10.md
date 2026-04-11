---
title: lyrics-framework Skill Phase 1 — Architecture and Workflow Decisions
date: 2026-04-10
category: docs/solutions/workflow-issues
module: lyrics-framework
problem_type: workflow_issue
component: development_workflow
severity: high
applies_when:
  - Building multi-model AI workflows with parallel execution
  - Implementing review systems to avoid model self-preference bias
  - Packaging Python skills for Claude Code
  - Extracting structural frameworks from Chinese pop songs
  - Running parallel AI fill workflows with Codex/Gemini/Qwen
tags:
  - lyrics-framework
  - skill-development
  - workflow
  - review-process
  - github
  - packaging
  - parallel-ai
  - bias-mitigation
  - codex
  - segmentation
---

# lyrics-framework Skill Phase 1 — Architecture and Workflow Decisions

## Context

Building the lyrics-framework Claude Code skill required establishing two complete sub-workflows from scratch: one that extracts structural frameworks from existing Chinese pop songs, and one that fills new lyrics into those frameworks using multiple AI models in parallel. Phase 1 surfaced several non-obvious problems — a misidentified song requiring framework rename and segmentation correction, a systematic self-preference bias in AI review, and a Codex sandbox restriction that silently blocked file writes when invoked through an agent wrapper. Each required a deliberate architectural decision.

**Phase 1 status**: Complete as of 2026-04-10.
**GitHub repos**: [lyrics-framework](https://github.com/chen-da-pang/lyrics-framework) (skill) · [lyrics-frameworks-skill](https://github.com/chen-da-pang/lyrics-frameworks-skill) (framework library)

## Guidance

### Sub-workflow A: Lyrics → Framework

The extraction pipeline runs in sequence:

1. Segment the lyrics into structural units (verse, pre-chorus, chorus, bridge, outro)
2. Annotate each line: char count, semantic role, rhyme 辙, Hook position
3. Rhyme analysis: six-dimension system (see `references/rhyme-taxonomy.md`)
4. Generate `framework.yaml` (machine-readable) and `framework-fillable.md` (fill template)
5. Commit and push to framework library:

```bash
cd /Users/wycm/lycris_skill
git add frameworks/{song_id}/ frameworks/index.yaml
git commit -m "Add framework: {song_name}"
git push
```

**Critical segmentation rule**: Text-based rules are necessary but not sufficient. Pre-chorus and chorus that share the same melodic unit must be treated as a single segment even if they appear as separate lyric blocks. This requires musical knowledge — pure text analysis will misidentify the boundary.

The `lianren` framework is the canonical corrected example. Originally named `aishangshiyichangxiaoyu` (misidentified song), it was renamed and its segmentation corrected from 12 segments to 9: the pre-chorus (S3) and chorus (S4+S5) share one melodic unit and must be one segment.

### Sub-workflow B: Framework → Lyrics

1. Build fill prompt from `framework-fillable.md` + `framework.yaml` (extract original main rhyme to exclude)
2. Run four models in parallel: Claude (self), Codex, Gemini, Qwen
3. Apply two-layer review
4. Output best version in Suno format

**Parallel model invocation:**

| Model | Invocation | Notes |
|-------|-----------|-------|
| Claude | Direct in session | Always runs |
| Codex | `node codex-companion.mjs task --fresh "..."` via Bash | Do NOT use Agent wrapper — sandbox blocks file writes |
| Gemini | `mcp__gemini-cli__ask-gemini` | Style note: "偏意象化、诗意风格" |
| Qwen | `qwen-code` skill | Requires periodic re-auth |

If any model is unavailable: skip and note, continue with remaining models.

### Codex Execution Pattern

**Wrong** (agent sandbox silently blocks file writes):
```
# Do NOT do this
Skill("codex:rescue", args="write output to /path/file.md ...")
```

**Right** (Bash direct, file writes succeed):
```bash
node ~/.claude/plugins/cache/openai-codex/codex/1.0.3/scripts/codex-companion.mjs \
  task --fresh "$(cat /tmp/prompt.txt)"
```

For long prompts, write to a temp file first to avoid shell escaping failures:
```bash
cat > /tmp/review_prompt.txt << 'EOF'
[full prompt here]
EOF
node .../codex-companion.mjs task --fresh "$(cat /tmp/review_prompt.txt)"
```

Read output (strip log lines):
```bash
grep -v "^\[codex\]" /private/tmp/claude-501/.../tasks/{id}.output
```

### Two-Layer Review System

**The problem**: Codex systematically favors its own output when scoring framework compliance — it naturally follows structural rules when writing, so it scores itself highly. Using Codex as both fill model and sole reviewer produces biased results.

**The solution**: Two independent layers.

**Layer 1 — Framework compliance (Codex):**
- Character count per line matches framework spec
- Rhyme correctness ([押主韵] lines end on declared primary rhyme)
- Hook句 appears identically at all annotated positions
- Return quality: S5/S6 compress and echo S1/S2

**Layer 2 — Content quality (anonymous parallel subagents):**
- Label versions A/B/C — hide AI source to prevent self-preference bias
- Launch one subagent per version simultaneously
- Score on: 语感流畅度, 意象统一性, 情绪感染力, 原创性, 主题契合度

Subagent prompt pattern:
```
你是一位专业的中文流行歌词评审，请对以下歌词做内容质量审核。
不要关注字数是否符合框架，只评估内容质量。

【版本 A】
[lyrics — source hidden]

【版本 B】
[lyrics — source hidden]
...
```

Combine both layers to determine the recommended version.

### Fill Prompt Hard Constraints

Every fill prompt must include these constraints:

```
- 不能使用[原曲主韵]作为主韵，自选其他韵
- 严格遵守每句字数，不能多也不能少
- Hook句在全曲所有标注「← Hook句」的位置完全重复，一字不差
- [押主韵]的句子句尾必须押选定的主韵
- [掺韵]的句子故意不押主韵
- S5/S6必须是S1/S2的压缩回返，不能重新创作新内容
- 只输出歌词，每句标注行号，不要输出其他内容
```

### Skill Packaging Decisions

- Python package `lyrics_framework_extraction` bundled into `scripts/` inside skill repo
- Prerequisites (tool setup) in `references/prerequisites.md` — NOT in `SKILL.md` body (avoids context window cost)
- `SKILL.md` body target: under 175 lines (hard limit 500)
- Exclude `__pycache__` via `.gitignore`
- Framework library README: no song names or artist names (copyright risk)

## Why This Matters

**Two-layer review**: Without it, Codex wins not because its lyrics are better but because it follows structural rules more mechanically. Separating compliance review from content quality review, and anonymizing the content review, produces results that are both structurally valid and independently verified for quality.

**Codex sandbox restriction**: When Codex is invoked through an Agent subagent, file writes silently fail — the task appears to complete but no output file is written. This is not documented anywhere and must be discovered empirically. Always invoke via Bash directly.

**Segmentation + musical knowledge**: A model that has never heard the song will apply segmentation rules correctly and still produce a wrong framework. Musical knowledge — specifically, whether a pre-chorus and chorus share a melodic unit — is required for accurate segmentation. This is a fundamental limitation of text-only analysis.

## When to Apply

- **New framework extraction**: Verify segmentation against the actual song before committing. If the song is unfamiliar, flag for human review.
- **Parallel fill**: Always invoke Codex via Bash directly, never via Agent subagent.
- **Fill prompts**: Include all hard constraints. Missing any one produces structurally invalid output that fails Layer 1 review.
- **Layer 2 review**: Always anonymize versions before passing to subagents.
- **Skill packaging**: Keep SKILL.md under 175 lines. Move prerequisites to `references/`. Exclude `__pycache__`.
- **New framework commit**: Only commit `frameworks/{id}/` and `frameworks/index.yaml`.

## Examples

### Segmentation correction (lianren)

Wrong (3 segments — text-based rule applied without musical knowledge):
```
S3: pre-chorus (4 lines)   ← wrong boundary
S4: chorus A (4 lines)
S5: chorus B (3 lines)
```

Correct (2 segments — pre-chorus and chorus share one melodic unit):
```
S3: pre-chorus (4 lines)   ← same melodic unit as S4
S4: chorus (7 lines, includes叠字 build + Hook宣言)
```

### Anonymous content review (Layer 2)

```
版本A:
L01 爱像旧胶卷潮湿
L02 咔嚓咔嚓响着
...

版本B:
L01 爱像是一段旧记忆
L02 咔嚓咔嚓地响
...

请从以下维度评分（1-10）：
1. 语感流畅度
2. 意象统一性
3. 情绪感染力
4. 原创性
5. 主题契合度
```

The subagent does not know version A is Codex output. This prevents systematic self-preference bias.

## Related

- `docs/solutions/workflow-issues/segmentation-rules-non-standard-structures-2026-04-08.md` — segmentation rule updates that preceded this phase
- `docs/solutions/integration-issues/codex-cli-mcp-replaced-by-codex-rescue-skill-2026-04-09.md` — Codex MCP 503 fix that led to current invocation pattern
- `docs/superpowers/specs/2026-04-07-lyrics-framework-extraction-design.md` — authoritative design spec for framework extraction
- `docs/superpowers/specs/2026-04-03-lyrics-segmentation-logic-design.md` — segmentation method design
- `docs/superpowers/plans/2026-04-07-lyrics-framework-extraction-implementation.md` — Phase 1 execution roadmap
- `~/.claude/skills/lyrics-framework/references/prerequisites.md` — tool setup instructions
- `frameworks/lianren/` — canonical corrected framework example
