# Framework Notes: 月亮代表我的心 - 邓丽君

## Scope

- This directory is the formal collected framework entry for `月亮代表我的心`.
- This applies the updated `lyrics-framework` rule set: anything detected must be recorded, not only dominant rhyme structure.

## Source And Lineation Basis

- Working edition for this framework: Teresa Teng's classic Mandarin recording.
- Accepted lineation basis: Short lyric lines. Short clauses are treated as independent `display_line` entries rather than force-merged into longer compound lines.

## Numbered Lines Used For This Framework

```text
L01 你问我爱你有多深
L02 我爱你有几分
L03 我的情也真
L04 我的爱也真
L05 月亮代表我的心
L06 你问我爱你有多深
L07 我爱你有几分
L08 我的情不移
L09 我的爱不变
L10 月亮代表我的心
L11 轻轻的一个吻
L12 已经打动我的心
L13 深深的一段情
L14 教我思念到如今
L15 你问我爱你有多深
L16 我爱你有几分
L17 你去想一想
L18 你去看一看
L19 月亮代表我的心
```

## Framework Summary

- Segment judgment:
  - `S1 = L01-L05` 主歌 A (Verse 1)
  - `S2 = L06-L10` 主歌 B (Verse 2)
  - `S3 = L11-L14` 桥段 (Bridge)
  - `S4 = L15-L19` 主歌 C (Verse 3)
- Main rhyme route:
  - **人辰辙 (en/in/un)**
  - `S1`: en -> en -> en -> en -> in (All main rhyme)
  - `S2`: en -> en -> i -> ian -> in (Rhyme break at L08/L09, return at L10)
  - `S3`: un -> in -> ing -> in (Near-rhyme chain: un/in/ing)
  - `S4`: en -> en -> iang -> an -> in (Rhyme break at L17/L18, return at L19)

## Non-tail Techniques Inventory

- `T01` 对举：`L01 / L02` (and `L06/L07`, `L15/L16`)
  - form: 提问+追问的双句对举
  - description: `你问我爱你有多深 / 我爱你有几分`
- `T02` 对举：`L03 / L04`
  - form: `我的[名词]也真`
  - description: `我的情也真 / 我的爱也真`
- `T03` 对举：`L08 / L09`
  - form: `我的[名词]不[变动/形容词]`
  - description: `我的情不移 / 我的爱不变`
- `T04` 对举：`L11 / L13`
  - form: `[AA叠字]的[一个/一段][名词]`
  - description: `轻轻的一个吻 / 深深的一段情`
- `T05` 对举：`L17 / L18`
  - form: `你去[动词]一[动词]`
  - description: `你去想一想 / 你去看一看`
- `T06` 叠词：`L11` and `L13`
  - form: AA叠词开头
  - description: `轻轻` / `深深`
- `T07` 重复与动作指引：`L17` and `L18`
  - form: `A一A` 动作叠词
  - description: `想一想` / `看一看`

## Duiju / Parallelism Map

- `L01 ↔ L02`
  - shared character pattern: `7 / 6`
  - shared syntactic skeleton: 主谓宾 + 程度/数量提问
  - semantic axis: 设问与回响
  - fill constraint: 保留提问与回答的并列关系

- `L03 ↔ L04`
  - shared character pattern: `5 / 5`
  - shared syntactic skeleton: `我的[名词]也真`
  - semantic axis: 平行情怀宣言
  - fill constraint: 保留 `我的A也X / 我的B也X` 并列对举

- `L08 ↔ L09`
  - shared character pattern: `5 / 5`
  - shared syntactic skeleton: `我的[名词]不[动词/形容词]`
  - semantic axis: 忠诚度承诺
  - fill constraint: 保留 `我的A不X / 我的B不Y` 平行否定对仗

- `L11 ↔ L13`
  - shared character pattern: `6 / 6`
  - shared syntactic skeleton: `[AA叠字]的[数量]个/段[名词]`
  - semantic axis: 细节点滴回忆
  - fill constraint: 保留 `AA的一B` 叠字短语对偶

- `L17 ↔ L18`
  - shared character pattern: `5 / 5`
  - shared syntactic skeleton: `你去[动词]一[动词]`
  - semantic axis: 行动检验
  - fill constraint: 保留 `你去X一X` 动词重叠格式
