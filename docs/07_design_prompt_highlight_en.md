# Claude Design Prompt — Pattern B: Highlight Reel (3 Key Moments)

> **Purpose**: Paste these prompts into Claude (Artifacts / design mode) to build a **short, high-impact presentation demo** focusing on the three wow moments: judge roulette, trial spectator view, and verdict card. UI copy is **Japanese**. Instructions are **English**.
>
> **Recommended workflow**: Use **Staged Prompts (Step 0→5)** in the same conversation. Fall back to **One-Shot Master Prompt** only if time is tight.

---

## How to Use This Document

1. Start a new Claude Artifacts conversation.
2. **Pin** the Master Spec below.
3. Paste **Step 0 → 5** sequentially in the **same thread**.
4. Attach judge images via `JUDGE_IMAGES` when ready (Step 4).
5. This version is optimized for a **3–5 minute live presentation** — skip hearing/handoff detail.

---

## SHARED BLOCKS (Pin These First)

### Master Spec (pin at top of chat)

```
Build a React single-file component artifact: a SHORT scripted highlight demo of「家庭内裁判所」— focusing on judge roulette reveal, courtroom spectator debate, and verdict card. Preceded by a minimal splash (case title only). No LLM/API; all dialogue hardcoded. Mobile-first, Japanese UI, pop/entertaining tone. useState only (no localStorage). Tailwind + lucide-react. Judge portraits via JUDGE_IMAGES map (URL/data URI). Disclaimer on every screen.
```

### Product Context

(Same as Pattern A — see `docs/06_design_prompt_full_en.md` Shared Blocks, or summarize:)

「家庭内裁判所」turns household disputes into an entertaining AI-mediated court ritual. Random characterful judges, attorney agents on each side, always ends with a verdict. This highlight demo shows the **emotional peaks** of that experience in under 5 minutes.

### Visual Direction

**Tone**: Maximum pop energy — gacha reveal, visual-novel chat, celebratory verdict card.

**Benchmark**: Gacha/roulette apps + character chat games + Duolingo-style cards.

**Role colors**: A attorney = cyan, B attorney = magenta, Judge = amber.

**Do NOT** generate judge portraits. Use `JUDGE_IMAGES` or placeholder initials.

### Artifact Sandbox Constraints

| Rule | Detail |
|------|--------|
| Type | React single-file component |
| State | useState/useReducer — no localStorage |
| Network | No API/fetch |
| Libraries | Tailwind, lucide-react only |
| Images | JUDGE_IMAGES map — no local paths, no AI-generated portraits |

### Judges Pool

| id | name | title | catchphrase |
|----|------|-------|-------------|
| `judge_ooka` | 大岡 忠相 | 令和の大岡裁き | これにて、一件落着！ |
| `judge_logica` | ロジカ | 感情を採点しないAI裁判官 | 判定を確定します。異議は受理しません |
| `judge_mitsuko` | 観音寺 みつこ | 涙もろい調停おばちゃん | はい、よう言えました。えらい！ |
| `judge_iwaya` | 巌谷 鉄五郎 | 昭和から来た男 | 次にわしの前に来るときは、夫婦喧嘩の土産話にせい |
| `judge_luna` | ジャッジ☆ルナ | 法廷のカリスマ | 以上、閉廷〜！おつかれ〜 |

**Script judge**: `judge_iwaya`. Roulette stops on Iwaya.

### Character Image Assets

```javascript
const JUDGE_IMAGES = {
  judge_ooka: null,
  judge_logica: null,
  judge_mitsuko: null,
  judge_iwaya: null,
  judge_luna: null,
};
// Fallback: colored circle + kanji initial. User supplies URL/data URI later.
```

### Scripted Scenario Data (highlight subset)

Use the **same case** as Pattern A:

- **Parties**: 美咲 (plaintiff) vs 健太 (defendant)
- **Case title**: 令和7年（家）第1号 夫婦合算家計における自作キーボード無断購入事件
- **One-line context** (splash): 「夫が相談なしに10万円のキーボードを購入した件」
- **Judge opening** (after roulette): 巌谷's opening speech (abbreviated OK on splash, full on intro card)
- **Trial speeches**: All 5 speeches from CLI log (judge 争点整理 → A弁論 → B弁論 → A再反論 → B再々反論) — **full verbatim text**
- **Verdict**: 美咲 75 : 健太 25, full verdict text + 5 proposals

Full text reference: `docs/05_cli_prototype_log.txt` lines 207–473.

```javascript
const HIGHLIGHT_SCENARIO = {
  caseTitle: "令和7年（家）第1号 夫婦合算家計における自作キーボード無断購入事件",
  parties: { a: "美咲", b: "健太" },
  contextLine: "夫が相談なしに10万円の自作キーボードを購入した件",
  selectedJudgeId: "judge_iwaya",
  ratioA: 75,
  ratioB: 25,
  proposals: [ /* 5 items from CLI log */ ],
  // trial.speeches[] and verdict.mainText: embed full Japanese from CLI log
};
```

### Screens in This Pattern (3 highlights + splash)

| # | Screen | Purpose |
|---|--------|---------|
| 0 | SPLASH | Case title + one-line context →「裁判を始める」 |
| 1 | JUDGE_SELECT | **Highlight 1** — Roulette through 5 judges → Iwaya reveal + opening |
| 2 | TRIAL | **Highlight 2** — Spectator debate, speeches auto-advance |
| 3 | VERDICT | **Highlight 3** — Typewriter verdict + animated ratio bar + proposal cards |

**Omitted** (intentionally): INTAKE detail, HEARING_A/B, HANDOFF, ESCALATION, appeal flow.

---

## STAGED PROMPTS (Main)

### Step 0: Kickoff

```
I want a SHORT highlight-reel prototype for「家庭内裁判所」— 3 wow moments only (roulette, trial, verdict) plus a minimal splash. See pinned Master Spec.

Before coding:
1. Confirm: scripted React artifact, no API/localStorage.
2. List the 4 screens (SPLASH → JUDGE_SELECT → TRIAL → VERDICT).
3. Ask at most 1 question if needed.

No code yet. Wait for my "go".
```

### Step 1: Skeleton

```
Step 1 — Skeleton for highlight reel.

React single-file artifact:
- Phases: SPLASH → JUDGE_SELECT → TRIAL → VERDICT
- Mobile-first (~430px), pop design tokens, role colors (A=cyan, B=magenta, judge=amber)
- Persistent disclaimer footer
- Each screen: placeholder +「次へ」or auto-advance hook
- TOC comment at top

**User flow:**
- SPLASH: tap「裁判を始める」→ JUDGE_SELECT
- JUDGE_SELECT: tap or auto「ルーレット開始」→ spin → TRIAL
- TRIAL: tap「次へ」or auto-reveal speeches → VERDICT
- VERDICT: typewriter + card, end state

Placeholders only. useState. No scenario text yet.
```

### Step 2: Content

```
Step 2 — Wire highlight content.

Keep skeleton. Add HIGHLIGHT_SCENARIO with full Japanese trial speeches and verdict text (verbatim from CLI log).

**SPLASH**: Dramatic case title card +「夫が相談なしに10万円の自作キーボードを購入した件」+ parties 美咲 vs 健太 +「裁判を始める」

**JUDGE_SELECT**: 5-judge roulette → stop 巌谷 鉄五郎 → large portrait slot + title「昭和から来た男」+ catchphrase + abbreviated opening speech

**TRIAL**: Header「代理戦争 — ここからは観戦です」. Sequential speeches with speaker avatar, name, role color. All 5 speeches full text.

**VERDICT**: Full 巌谷 verdict → summary card: ratio bar 75:25, judge name, 5 proposals as cards, catchphrase footer.

No hearing screens. No handoff. Pure playback.
```

### Step 3: Polish

```
Step 3 — Maximum presentation impact. Keep content unchanged.

- SPLASH: Title stamp animation (scale + fade), gavel icon bounce
- ROULETTE: 2s slot-machine spin, blur motion, confetti/sparkle on Iwaya land, catchphrase pop-in
- TRIAL: Auto-advance every 2.5s (with pause/play toggle); speeches slide from speaker side; subtle courtroom BG
- VERDICT: Typewriter (with skip), ratio bars animate from 0, proposal cards flip/stagger in, final「閉廷」stamp

Add「スキップ」on verdict. Pop, game-show energy.
```

### Step 4: Image slots

```
Step 4 — JUDGE_IMAGES wiring. Same as Pattern A.

<JudgeAvatar> with URL or kanji fallback. Prominent on roulette + verdict header. Do not generate portraits. Show me the edit line for pasting image URLs.
```

### Step 5: QA

```
Step 5 — QA for 3–5 min live demo.

□ SPLASH → VERDICT in under 20 taps (or 3 min with auto-advance)
□ Roulette always lands on 巌谷
□ All trial/verdict Japanese text complete
□ 75:25 ratio bar animates correctly
□ Disclaimer always visible
□ 375px mobile, no localStorage/fetch
□ JUDGE_IMAGES null + URL both work

Add「5分プレゼン操作ガイド」comment at top.
```

---

## ONE-SHOT MASTER PROMPT (Fallback)

```
Build a React single-file HIGHLIGHT demo for「家庭内裁判所」: 4 screens only (SPLASH → JUDGE_ROULETTE → TRIAL spectator → VERDICT card).

Scripted, no API/localStorage, Tailwind+lucide-react, mobile, Japanese, pop tone.

Case: 美咲 vs 健太, keyboard dispute, judge 巌谷 鉄五郎 lands on roulette, verdict 75:25. Full trial speeches + verdict text from CLI log verbatim.

Roulette: 2s spin through 5 judges. Trial: 5 speeches auto or tap advance. Verdict: typewriter + animated bars + proposal cards.

JUDGE_IMAGES map with null fallback — no AI portraits. Disclaimer all screens. Think carefully before coding (effort: high).
```

---

## APPENDIX: Iteration Tips

(Same as Pattern A — use incremental diffs, error template, same conversation.)

**Highlight-specific tip**: If trial feels too long for live demo, ask: "Add a「早送り」toggle that advances trial speeches every 1s instead of 2.5s — keep all text."

---

## ACCEPTANCE CHECKLIST

- [ ] SPLASH → VERDICT completable in ≤5 minutes
- [ ] Roulette lands on 巌谷 with strong visual impact
- [ ] Trial shows all 5 speeches with correct role colors
- [ ] Verdict 75:25 with animated bar + 5 proposals
- [ ] Full Japanese text from CLI log (trial + verdict)
- [ ] Disclaimer on all screens
- [ ] Mobile 375px
- [ ] JUDGE_IMAGES injectable
- [ ] No localStorage / no network
