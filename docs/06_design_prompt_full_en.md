# Claude Design Prompt — Pattern A: Full 8-Screen Demo

> **Purpose**: Paste these prompts into Claude (Artifacts / design mode) to build a **scripted, click-through presentation prototype** for「家庭内裁判所」(Domestic Court). UI copy is **Japanese**. Instructions are **English**.
>
> **Recommended workflow**: Use **Staged Prompts (Step 0→5)** in the same conversation. Fall back to **One-Shot Master Prompt** only if time is tight.

---

## How to Use This Document

1. Start a new Claude Artifacts conversation (sidebar → Artifacts).
2. **Pin** the Master Spec (below) at the top of the chat.
3. Paste **Step 0**, wait for Claude to confirm, then paste **Step 1 → 5** sequentially in the **same thread**.
4. When judge illustrations are ready, paste image URLs or data URIs into `JUDGE_IMAGES` (Step 4).
5. Use **Appendix: Iteration Tips** for refinements — never ask for a full rewrite unless broken.

---

## SHARED BLOCKS (Pin These First)

### Master Spec (pin at top of chat)

```
Build a React single-file component artifact: a scripted, click-through demo of「家庭内裁判所」— an entertainment app where couples resolve household disputes through AI-mediated "court" proceedings (intake → hearing → trial → verdict). One device, two people take turns. No LLM/API calls; all dialogue is hardcoded from the scenario below. Mobile-first (max-width ~430px), Japanese UI copy, pop/entertaining visual tone. State via useState/useReducer only (no localStorage). Libraries: Tailwind CSS, lucide-react only. External images via JUDGE_IMAGES map (URL or data URI). Disclaimer visible on every screen: 「本サービスはエンターテインメントであり、法的助言・カウンセリングではありません。」
```

### Product Context

**Problem**: Small household conflicts (chores, money, parenting) lack a neutral third party. Friends aren't neutral; counseling feels too heavy.

**Value**:
- **Neutrality**: AI agents have no stake; attorneys advocate for each party; judges apply social norms
- **Articulation**: Attorney agents help users verbalize facts, feelings, and requests
- **Entertainment**: "Court" metaphor + characterful judges turns conflict into a playful ritual
- **Closure**: Always ends with a verdict — no ambiguous endings

**Key design philosophy**:
- Judges are **randomly assigned** with intentional personality bias (±15pt max) — this is a feature, not a bug. Losing parties can blame "that judge's personality."
- Attorneys are always **on the client's side** during hearings — never preach or judge the user
- Privacy: raw hearing logs are hidden from the other party until trial (only approved summaries go to court)

### Visual Direction

**Tone**: Pop, bright, approachable — like a mobile game / character-driven chat app. Court gravitas meets playful energy.

**Benchmark references** (mix these):
- Duolingo-style friendly cards and rounded buttons
- Character chat bubbles (visual novel / LINE-style)
- Gacha/roulette reveal animation for judge selection
- Stripe-style clean card layout for verdict summary

**Role colors** (match CLI prototype):
| Role | Color | Usage |
|------|-------|-------|
| Clerk (書記官) | Gray / neutral | System messages |
| A-side Attorney | Cyan (`#06b6d4` area) | Misaki's agent |
| B-side Attorney | Magenta (`#d946ef` area) | Kenta's agent |
| Judge | Amber (`#f59e0b` area) | All judge speech |

**Typography**: Clean sans-serif. Case titles (事件名) in bold, slightly ceremonial styling.

**Do NOT**: Generate judge portrait illustrations. Use `JUDGE_IMAGES` slots or placeholder initials only.

### Artifact Sandbox Constraints (MUST follow)

| Rule | Detail |
|------|--------|
| Artifact type | React single-file component |
| State | `useState` / `useReducer` only — **no** `localStorage` / `sessionStorage` |
| Network | **No** external API calls, fetch, or LLM |
| Libraries | Tailwind CSS, lucide-react — **avoid** jQuery, axios, lodash (sandbox may fail silently) |
| Images | **No local file paths**. Use `JUDGE_IMAGES` map at top of code (URL or data URI). Fallback to colored circle + initial if `null` |
| TOC | Add a comment block at top of file listing sections + rough line ranges |
| Edge cases | Handle: image not set → placeholder; empty states; long Japanese text wraps properly |

### Judges Pool (roulette + script judge)

| id | name | title | catchphrase | tone hint |
|----|------|-------|-------------|-----------|
| `judge_ooka` | 大岡 忠相 | 令和の大岡裁き | これにて、一件落着！ | 時代劇調 |
| `judge_logica` | ロジカ | 感情を採点しないAI裁判官 | 判定を確定します。異議は受理しません | 無機質・簡潔 |
| `judge_mitsuko` | 観音寺 みつこ | 涙もろい調停おばちゃん | はい、よう言えました。えらい！ | 関西弁 |
| `judge_iwaya` | 巌谷 鉄五郎 | 昭和から来た男 | 次にわしの前に来るときは、夫婦喧嘩の土産話にせい | ぶっきらぼう・昭和親父 |
| `judge_luna` | ジャッジ☆ルナ | 法廷のカリスマ | 以上、閉廷〜！おつかれ〜 | ギャル語 |

**Script judge for this demo**: `judge_iwaya` (巌谷 鉄五郎). Roulette cycles through all 5, then **stops on Iwaya**.

### Character Image Assets

```javascript
// Place at TOP of artifact code — user supplies URLs or data URIs later
const JUDGE_IMAGES = {
  judge_ooka: null,      // 大岡 忠相 — portrait, 1:1, min 256×256
  judge_logica: null,    // ロジカ
  judge_mitsuko: null,   // 観音寺 みつこ
  judge_iwaya: null,     // 巌谷 鉄五郎 — REQUIRED for this demo script
  judge_luna: null,      // ジャッジ☆ルナ
};
// Fallback component: colored circle + first kanji of name when null
// DO NOT generate AI portraits. Clerk/attorneys: lucide icons only.
```

**Placement**: Roulette cell, judge intro card, trial speaker avatar, verdict header.

### Scripted Scenario Data

```javascript
const SCENARIO = {
  parties: { a: "美咲", b: "健太" },
  caseTitle: "令和7年（家）第1号 夫婦合算家計における自作キーボード無断購入事件",
  selectedJudgeId: "judge_iwaya",

  intake: {
    clerkGreeting: "美咲さん、何があったかをお聞かせください。",
    plaintiffStatement: "先週の金曜、夫の健太が私に何の相談もなく10万円の自作キーボードを買いました。我が家は家計を合算でやりくりしているのに、勝手に大きな買い物をされて納得がいきません。",
    filingMessage: "受理しました。事件名を起案しています…",
  },

  judgeOpening: `わしが本件の担当、巌谷 鉄五郎じゃ。見ての通り、昭和の風が吹いておるが、裁きは公平にやる。美咲さん、健太さん、よく来たな。

夫婦の揉め事をわしの前に持ってくるとは、それだけ本気ということじゃろう。ならばわしも本気で向き合う。

これより「令和7年（家）第1号　夫婦合算家計における自作キーボード無断購入事件」を開廷する。

これからそれぞれの代理人によるヒアリングが行われる。言い訳ではなく、事実をきちんと話すよう、両名に伝えておけ。

**次にわしの前に来るときは、夫婦喧嘩の土産話にせい。**`,

  hearingA: {
    intro: "代理人にあなたの言い分を話してください。聞き取りが十分になると、サマリーが提示されます。",
    turns: [
      { role: "attorney_a", text: "はじめまして、美咲さん。本日担当させていただく弁護士です。どうぞ安心してお話しください。\n\n今回のこと、家計を一緒に管理されているなかでの出来事だったのですね。それは戸惑われたことと思います。\n\nまず最初に、事件の経緯を整理させてください――先週金曜日にキーボードを購入されたとのことですが、美咲さんはそれをいつ・どのようにして知りましたか？" },
      { role: "user_a", text: "買ったのは先週金曜です。金額は10万2千円で、家計用の共通口座のカードで支払われていました。" },
      { role: "attorney_a", text: "そうでしたか。共通口座のカードで、しかも10万円を超える金額が事前相談なく使われていたのですね。それは驚かれたことと思います。\n\n確認させてください――美咲さんはその購入をどのように知りましたか？健太さんから報告があったのか、それとも口座の明細などで気づかれたのでしょうか？" },
      { role: "user_a", text: "明確なルールはないですが、結婚してから2年間、1万円を超える買い物はお互いLINEで一言相談してきました。私も冬のコートを買うときは相談しました。" },
      { role: "attorney_a", text: "明確なルールはないけれど、2年間の実績として自然と守られてきた慣行があったのですね。美咲さんご自身もそれを誠実に守ってこられた。それは大切なことだと思います。\n\nちなみに、健太さんから事前の相談はあったのか、それとも購入後の報告もなかったのか――どちらでしたでしょうか？" },
      { role: "user_a", text: "一番嫌だったのは、金額よりも、事後報告すらなくカードの明細で気づいたことです。隠されていた感じがして悲しかったです。" },
      { role: "attorney_a", text: "金額の問題ではなく、「隠されていた」と感じた、その悲しさが一番大きかったのですね。2年間積み上げてきた信頼が揺らいだような気持ちになられたのかもしれません。\n\nその気持ち、しっかり受け止めました。最後にもう一点だけ聞かせてください――今回の件で、美咲さんは健太さんにどうしてほしいと思っていますか？謝ってほしい、ルールを作り直したい、返金してほしい……どんなことでも構いません。" },
      { role: "user_a", text: "キーボードを返品してほしいわけではありません。今後は大きな買い物の前に一言相談してほしい。それと、今回のことについてちゃんと謝ってほしいです。" },
    ],
    summary: `**【美咲さんの言い分サマリー】**

**■ 事実**
- 先週金曜日、夫・健太さんが自作キーボードを102,000円で購入した
- 支払いは夫婦共通口座のカードで行われた
- 美咲さんは事前の相談を受けておらず、事後報告もなく、カードの明細によって購入を知った

**■ 経緯**
- 婚姻後2年間、1万円を超える買い物の際はLINEで事前に相談し合うという慣行が夫婦間に存在した
- 明文化されたルールではないが、美咲さん自身も冬のコート購入時に相談するなど、双方がこの慣行を守ってきた実績がある

**■ 感情**
- 金額の大きさよりも、事後報告すらなくカードの明細で発覚したことに強い悲しみを覚えた
- 隠されていたように感じ、これまで築いてきた信頼関係が傷ついたと感じている

**■ 要望**
- キーボードの返品は求めない
- 今後は1万円を超える買い物の前に、必ず一言相談するルールを明確にしてほしい
- 今回の件について、健太さんから誠意ある謝罪がほしい`,
    approvalPrompt: "この内容でよろしいですか？",
    submittedMessage: "美咲さんの主張サマリーが法廷に提出されました。",
  },

  handoff: {
    privacyNote: "ここまでの美咲さんの聞き取り内容は健太さんには表示されません（法廷にはサマリーのみ提出されます）。",
    instruction: "端末を健太さんに渡してください",
    buttonLabel: "準備OK",
  },

  hearingB: {
    intro: "代理人にあなたの言い分を話してください。聞き取りが十分になると、サマリーが提示されます。",
    turns: [
      { role: "attorney_b", text: "はじめまして、健太さん。わたくし、家庭内裁判所の代理人弁護士を務めさせていただきます。今日はお時間をいただき、ありがとうございます。\n\nさて、今回の件ですが、美咲さん側からは「事前相談なしの10万円超えの購入があった」という申し立てが出ています。健太さんにも、ぜひご自身の言葉でお話しいただきたいと思っています。\n\nまず、今回のキーボード購入について、健太さんなりの「経緯」を聞かせていただけますか？" },
      { role: "user_b", text: "買ったのは事実です。ただ、あれは3ヶ月前から再販を待っていた限定品で、通知が来てから30分以内に決めないと買えない状況でした。" },
      { role: "attorney_b", text: "なるほど、3ヶ月間ずっと再販を待ち続けていた限定品だったんですね。それは確かに、30分という時間制限の中では相談する余裕がなかったかもしれません。\n\nそのことは、美咲さんにも伝えていましたか？" },
      { role: "user_b", text: "相談しようと思いましたが、美咲は仕事の会議中で連絡がつきませんでした。買った後に言おうと思って、言いそびれてしまったのは反省しています。" },
      { role: "attorney_b", text: "そうだったんですね。会議中で連絡がつかない、でも30分以内に決断しなければならない——それは確かに難しい状況でしたね。言いそびれてしまったことも、正直に話してくださってありがとうございます。\n\n3ヶ月前からこのキーボードを狙っていたこと自体は、美咲さんはご存知でしたか？" },
      { role: "user_b", text: "お金は自分の趣味用に貯めていたつもりでしたが、口座を分けていなかったのは確かです。共通口座から出たのはまずかったと思っています。" },
      { role: "attorney_b", text: "正直にお話しくださって、ありがとうございます。「趣味用に貯めていたつもり」という気持ちと、「口座が分かれていなかった」という現実の間で、健太さん自身もモヤモヤされているところがあるんですね。\n\n今回の件で健太さんが「一番伝えたいこと」や「わかってほしいこと」は何でしょうか？" },
      { role: "user_b", text: "隠すつもりはなかったです。金額が大きいものは事前相談するルールを作るのは賛成です。ただ、趣味のお金をある程度自由に使える仕組みも作ってほしいです。" },
    ],
    summary: `**【健太さんの言い分サマリー】**

**■ 事実**
- 購入したキーボード（102,000円）は、3ヶ月前から再販を待っていた限定品であった
- 再販通知が届いてから30分以内に購入を決定しなければならない状況であった
- 購入時、美咲さんは仕事の会議中であり、事前に連絡を取ることができなかった
- 購入後に報告しようと思っていたが、言いそびれてしまった

**■ 経緯**
- 健太さんは当該購入費用を趣味のために積み立ててきたつもりであったが、夫婦の共通口座と明確に分離されていなかった
- 共通口座のカードで支払いが行われたことは、適切ではなかったと健太さん自身も認識している

**■ 感情**
- 購入を隠す意図は一切なかった
- 言いそびれてしまったことについては反省している

**■ 要望**
- 1万円を超える買い物について事前相談のルールを設けることには賛成である
- 一方で、各自が趣味に使えるお金をある程度自由に管理できる仕組みも、併せて整えてほしい`,
    approvalPrompt: "この内容でよろしいですか？",
    submittedMessage: "健太さんの主張サマリーが法廷に提出されました。",
  },

  trial: {
    spectatorNote: "ここからは観戦です",
    speeches: [
      { role: "judge", title: "争点の整理", text: "（開廷宣言・3争点の整理 — 慣行の拘束力 / 緊急性の免責 / 共通口座の設計。全文はSCENARIOデータ参照）" },
      { role: "attorney_a", title: "主張陳述", text: "（A側代理人弁論 — 争点①②③について美咲さんの主張）" },
      { role: "attorney_b", title: "反論", text: "（B側代理人弁論 — 事情の酌量・ルール化賛成・趣味費の要望）" },
      { role: "attorney_a", title: "再反論", text: "（沈黙の重さ・関係の修復を強調）" },
      { role: "attorney_b", title: "再々反論", text: "（意図と結果は別物・謝罪は本人の言葉で）" },
    ],
    // Embed full trial texts from CLI log lines 207-335 in implementation
  },

  verdict: {
    ratioA: 75,
    ratioB: 25,
    mainText: "（巌谷裁判官の判決全文 — 主文75:25、理由3争点、提言3つ、双方への説諭、閉廷）",
    proposals: [
      "趣味費専用口座を今月中に開設し、共通口座から毎月一定額を自動移管する仕組みを整備する",
      "「1万円超の買い物は事前にLINEで相談する」ルールをLINEまたはメモアプリで文書化し、双方が確認できる形で保存する",
      "健太さんが自身の言葉で、購入後に沈黙した経緯と心情を美咲さんに直接説明する場を設ける",
      "共通口座の用途（家計専用）と個人趣味費の使途区分を明確に定め、双方が合意した運用ルールとして共有する",
      "大きな買い物をした際は、事後であっても購入品を嬉しそうに相手へ見せ・共有することを習慣とする",
    ],
    appealButtonLabel: "もう一度別の裁判官で裁いてもらう",
    appealDisabled: true,
  },

  escalation: {
    title: "裁判を中断しました",
    message: "本サービスはエンターテインメントであり、法的助言・医療的助言・カウンセリングではありません。",
    resources: [
      { label: "配偶者暴力相談支援センター", url: "https://www.gender.go.jp/policy/danjo/violence/support/" },
      { label: "こころの健康相談統一ダイヤル", url: "https://www.mhlw.go.jp/" },
      { label: "法テラス", url: "https://www.houterasu.or.jp/" },
    ],
  },
};

const DISCLAIMER = "※ 本サービスはエンターテインメントであり、法的助言・カウンセリングではありません。";
```

> **Note to implementer**: Embed **full verbatim text** for `trial.speeches` and `verdict.mainText` from the CLI log (`docs/05_cli_prototype_log.txt` lines 207–460). The abbreviated placeholders above are for structure only — the artifact must show the complete Japanese dialogue.

---

## STAGED PROMPTS (Main — paste in order, same conversation)

### Step 0: Kickoff

```
I want to build a presentation prototype for「家庭内裁判所」using the Master Spec and Shared Blocks I pinned above.

Before you write any code:
1. Confirm you understand this is a **scripted React artifact** (no API, no localStorage).
2. List the **8 screens** you will implement and the phase state machine.
3. Ask me **at most 2 clarifying questions** if anything is ambiguous.

Do NOT generate code yet. Wait for my "go" before Step 1.
```

### Step 1: Skeleton (layout + navigation only)

```
Step 1 — Build the skeleton only.

Create a React single-file artifact with:
- Phase state machine: TOP → INTAKE → JUDGE_SELECT → HEARING_A → HANDOFF → HEARING_B → TRIAL → VERDICT → CLOSING (+ ESCALATION accessible from a dev/preview menu)
- Mobile-first layout (max ~430px centered), persistent header with case title + phase indicator
- Footer disclaimer: 「※ 本サービスはエンターテインメントであり、法的助言・カウンセリングではありません。」
- Design tokens: pop colors, role colors (clerk gray, A=cyan, B=magenta, judge=amber)
- Each screen shows placeholder content + a primary「次へ」button to advance
- TOC comment at top of file

**User interactions (behavior, not just layout):**
- TOP: user taps「提訴する」→ goes to INTAKE
- INTAKE: user taps through scripted chat bubbles → event name reveal → JUDGE_SELECT
- HEARING_A/B: user taps「次へ」to reveal next bubble; ends with summary approval tap
- HANDOFF: user taps「準備OK」→ HEARING_B
- TRIAL: user taps「次へ」or auto-advance to reveal each speech
- VERDICT: typewriter effect on verdict text → verdict card
- CLOSING: verdict summary card + grayed-out appeal button

No scenario text yet — placeholders only. Keep everything in one file. Use useState only.
```

### Step 2: Content (wire scenario data)

```
Step 2 — Wire all scripted content.

**Keep** the skeleton, state machine, layout, and design tokens from Step 1.

**Add** the full SCENARIO data object (parties 美咲/健太, case title, all hearing turns, summaries, trial speeches, verdict text verbatim from the scenario).

**Screen behaviors:**
1. **TOP**: App name「家庭内裁判所」, tagline, disclaimer,「提訴する」button
2. **INTAKE**: Clerk greeting → show plaintiff statement as chat bubble →「受理しました。事件名を起案しています…」→ reveal case title dramatically
3. **JUDGE_SELECT**: Roulette animation cycling 5 judges → stop on 巌谷 鉄五郎 → show intro card + opening speech
4. **HEARING_A**: Replay Misaki's hearing turns sequentially; progress indicator (e.g. 3/5); show summary card;「この内容でよろしいですか？」→ approve
5. **HANDOFF**: Privacy explanation +「準備OK」
6. **HEARING_B**: Same pattern for Kenta
7. **TRIAL**: Spectator mode — speeches appear one by one with speaker name + role color + avatar slot
8. **VERDICT/CLOSING**: Full verdict text → summary card (ratio 75:25 bar, proposals list, judge name)
9. **ESCALATION**: Reference screen (link from footer menu「エスカレーション画面を見る」for demo)

Use exact Japanese copy from scenario. No LLM simulation — pure playback.
```

### Step 3: Polish (animations & presentation flair)

```
Step 3 — Add presentation animations. **Keep all content and navigation unchanged.**

- **JUDGE_SELECT**: Slot-machine / roulette spin through 5 judge cards (1.5–2s), decelerate, land on 巌谷 鉄五郎 with highlight + catchphrase
- **HEARING**: Chat bubbles fade/slide in; typing indicator between attorney messages (0.5s)
- **TRIAL**: Each speech slides in from speaker's side; subtle courtroom background pattern
- **VERDICT**: Typewriter reveal for verdict body (adjustable speed or skip button); ratio bar animates 0→75% / 0→25%; proposal cards stagger in
- **Case title**: Ceremonial reveal animation on INTAKE and verdict card
- Add「スキップ」button during long text sequences for live demo safety

Pop, game-like feel. No new libraries beyond Tailwind + lucide-react.
```

### Step 4: Image slots

```
Step 4 — Wire JUDGE_IMAGES. **Do not change layout or scenario text.**

At top of file:
const JUDGE_IMAGES = { judge_ooka: null, judge_logica: null, judge_mitsuko: null, judge_iwaya: null, judge_luna: null };

Create <JudgeAvatar id={...} size={...} /> component:
- If JUDGE_IMAGES[id] is set → show <img>
- If null → colored circle + first kanji (大/ロ/観/巌/ル)

Use in: roulette cells, judge intro card, trial speaker row, verdict header.

**Do NOT** draw or generate portrait illustrations. I will paste URLs/data URIs later.

Show me exactly which line to edit when I have images ready.
```

### Step 5: QA & handoff

```
Step 5 — Final QA pass.

Verify against checklist:
□ TOP → CLOSING completable without dead ends
□ All Japanese copy matches scenario (no truncation)
□ Role colors correct per speaker
□ Disclaimer on every screen
□ Mobile 375px width — no horizontal scroll
□ JUDGE_IMAGES slots work with null + with URL
□ No localStorage / no fetch / no external API
□ Appeal button grayed out with label「もう一度別の裁判官で裁いてもらう」
□ Escalation screen reachable from demo menu
□ Skip button works on long verdict

Fix any failures with **minimal diffs**. Add brief「デモ操作ガイド」comment at top: recommended tap order for 5-min live presentation.
```

---

## ONE-SHOT MASTER PROMPT (Fallback)

Use only if staged prompts are not feasible. Paste as a single message after pinning Master Spec + Shared Blocks.

```
Build a complete React single-file artifact for「家庭内裁判所」presentation demo.

**Constraints**: Scripted only, no API/localStorage, Tailwind+lucide-react, mobile-first, Japanese UI, pop visual tone, JUDGE_IMAGES map with null fallback (no AI-generated portraits).

**8 screens**: TOP → INTAKE → JUDGE_SELECT (roulette→巌谷) → HEARING_A (美咲) → HANDOFF → HEARING_B (健太) → TRIAL (spectator) → VERDICT+CLOSING. ESCALATION as preview menu item.

**Scenario**: Keyboard purchase dispute — 美咲 vs 健太, case「令和7年（家）第1号 夫婦合算家計における自作キーボード無断購入事件」, judge 巌谷 鉄五郎, verdict 75:25. Use full dialogue from pinned SCENARIO data verbatim.

**Interactions**: Tap「次へ」/「提訴する」/「準備OK」to advance. Hearing/trial = sequential bubble replay. Verdict = typewriter + animated ratio bar. Roulette lands on Iwaya. Grayed appeal button.

**Role colors**: A attorney cyan, B attorney magenta, judge amber. Disclaimer on all screens.

Think carefully about state machine structure before coding (effort: high). TOC comment at top.
```

---

## APPENDIX: Iteration Tips

### Incremental refinement (same conversation)

| Do | Don't |
|----|-------|
| "Keep the verdict card layout. Change ratio bar colors to amber gradient." | "Rebuild the whole app with a new design." |
| "Fix only the HANDOFF screen — button should be full-width." | "Start over from scratch." |
| "Trial speeches: increase delay to 1.2s between messages." | Repeat entire spec every message |

### Error report template

```
The artifact throws [error type] at approximately line [N] in [function/component name].
Fix ONLY that issue. Leave all other code, styling, and scenario text identical.
Error message: [paste exact error]
```

### When to use extended thinking

For Step 1 (state machine) or if trial/verdict rendering breaks, add:

```
Use adaptive thinking with high effort for the state machine and sequential playback logic.
```

### Forking

To try an alternate visual style, use Claude's "Edit message" on Step 1 to branch — keep the working version in the main thread.

---

## ACCEPTANCE CHECKLIST

- [ ] Complete flow TOP → CLOSING without dead ends (~5 min live demo)
- [ ] Scenario text matches CLI log (no invented dialogue)
- [ ] Role color coding per speaker
- [ ] Disclaimer visible on every screen
- [ ] Works at 375px mobile width
- [ ] `JUDGE_IMAGES` accepts URL/data URI injection; null shows placeholder
- [ ] No localStorage, no external network calls
- [ ] Appeal button present but disabled (grayed out)
- [ ] Escalation screen accessible for presentation reference
- [ ] Skip button on long verdict sequence
