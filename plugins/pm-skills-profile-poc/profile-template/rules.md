---
type: Reference
title: PM Skills プロファイル 運用ルール
generated: { by: human:igarashi, at: 2026-08-25T00:00:00Z }
---

# 3層データモデル

コンピテンシー成熟度は「事実→シグナル→評価」の3層で扱う。
- 事実(生ログ): セッション内で処理したら破棄する。どのファイルにも保存しない。
- シグナル: 事実を「対象コンピテンシー・方向・強さ・根拠」に変換したもの。`competencies/<ID>.md`の
  `sources`に直近5件のローリングウィンドウで保持し、6件目が入ったら最古の1件を破棄する。
  方向は3種類: エースPM相当は`direction: pos`、普通PM相当は`direction: maintain`、
  経験不足PM相当は`direction: risk`。普通PM相当を無条件にposにしない。
- 評価(推定レベル): シグナルを集約した現時点の最良推定。`competencies/<ID>.md`本文の
  「推定レベル」に現在値のみ保持する(履歴は`history/log.md`側)。

# 鮮度(stale_after)

各`competencies/<ID>.md`の`stale_after`は「最新sourceの日付+90日」で設定する。総評
(`synthesis/overview.md`)再生成時、`stale_after`が過去日付になっている項目は、強み・伸びしろ
どちらの区分にも含めず「(最終更新から時間が経過しているため保留)」と付記するか、区分から一旦外す。
新しいsourceが追加され`stale_after`が更新されれば、この保留は自動的に解除される。

# 総評の統合ルール

総評のゴールは「コンピテンシーごとの成績表の言い換え」ではなく「複数の観測を統合した一人の人物像」
であること。強み・伸びしろ・判断軸それぞれで、複数の項目にまたがる共通パターン(同じ場面・同じ思考の
型が繰り返し現れているか)が見つかった場合は、1つの記述に統合する(IDは参考として括弧などで残す)。
パターンが見つからない場合は単独の記述のままでよい。

対象の絞り込み: `sources`が2件未満の`competencies/<ID>.md`は総評の対象に含めない
(「まだ十分なデータがありません」扱い)。2件以上ある場合も、**現時点の推定レベルがエースPM相当のものだけを「強み」に、経験不足PM相当のものだけを「伸びしろ」に入れる。普通PM相当は現状維持のためどちらにも入れない。**

「最近の変化」欄(レベルが実際に変化した場合の記録)と「伸びしろ」欄を混同しない。例えば「エースPM→普通PMへ後退した」という事実は「最近の変化」欄でのみ扱う話であり、後退した結果の現時点のレベルが普通PMである以上、この項目を「伸びしろ」に含めてはならない。伸びしろに入るのは、あくまで**現時点で経験不足PM相当と判定されている項目**だけである。

再生成のタイミング: 各スキルの保存処理でシグナルを追記したセッションの直後に、
`synthesis/overview.md`全体を最新の状態で作り直す(差分パッチではなくフル再生成)。

# 判断軸

コンピテンシー成熟度(遂行の巧拙の評価)とは別に、ユーザー自身が判断するときに何を拠り所にしているか
(思考プロセス・価値観の傾向)を記録する層。既存の14コンピテンシーIDをタグとして流用するが、
レベル評価とは無関係。

- 記録条件: 行動の結果だけからは推測しない。ユーザー自身の発言に判断理由・思考プロセスが明確に
  表れている場合だけを対象にする。
- quizは対象外(ユーザー自身の判断理由が表れる発言が生じない設計のため)。
- `judgment-axes/<ID>.md`は`sources`が1件目から必ず作成・記録する(保存の閾値と表示の閾値を
  混同しない)。`synthesis/overview.md`の「大事にしている判断軸」に
  反映するのは、引き続き`sources`が2件以上たまってから。

# 最近の変化

推定レベルが実際に変化した瞬間、`history/log.md`への追記と合わせて、変化の中身を「エースPMに
上がった」のようなラベル比較ではなく、「思考・行動がどう深まったか」を表す一文として言語化し、
`synthesis/overview.md`の「最近の変化」欄にも反映する(直近2件、新しい順、3件目が入ったら最古を削除)。
初回の観測(そのIDに以前の推定レベルがない)場合はこの一文は書かない。

# ローリングウィンドウの運用規約(OKF本体は件数上限を規定しないため、ここで明文化)

- `competencies/<ID>.md`の`sources`: 最大5件
- `judgment-axes/<ID>.md`の`sources`: 最大5件、2件以上たまってはじめて総評に反映
- `sessions/<skill>/`配下のファイル数: 最大10件、11件目が増えたら最古のファイルを削除
- `preferences/quiz-theme-streaks.md`の直近正誤: マクロテーマごとに最大5件

# quizの弱いシグナルの扱い

quiz由来のシグナルは弱い(`strength: low`)。直近5件の`sources`の中に`mentor`または`roleplay`由来かつ
`strength: high`のものが1件でもあれば、そちらを優先し推定レベルは変更しない。直近`sources`が
quiz由来の弱いシグナルのみで構成されている場合に限り、推定レベルを1段階下げる方向で更新する。

# 案件コンテキストの参照ポリシー

`context/`配下は、pm-practical-advisor-aiが「前提不足」または「継続を示唆する発言」を検知した場合
のみ参照する。参照した場合も、記録内容をそのまま前提にせず、今回の発言と整合するか確認する。
複数の案件が候補になる、またはどれが該当するか判断できない場合は、断定せずユーザーに確認する。

# 各ファイル種別のフォーマット(実例)

以下は各concept typeのfrontmatter・本文の実例。各スキルのSKILL.mdから「◯◯を更新する」と指示されたら、
このフォーマットに従う(独自のフィールド名・構造を発明しない)。

## Concept type一覧

| type | 配置 | 1ファイルの単位 |
|---|---|---|
| `Competency Assessment` | `competencies/<ID>.md` | 1コンピテンシーの現在の推定レベルと根拠 |
| `Judgment Axis` | `judgment-axes/<ID>.md` | 1コンピテンシーに紐づく判断軸の傾向 |
| `Profile Synthesis` | `synthesis/overview.md` | 強み・伸びしろ・判断軸・最近の変化・応対への示唆の統合ビュー(1つだけ) |
| `Coaching Session` | `sessions/pm-mentor-ai/*.md` | mentor-aiの1相談セッション |
| `Roleplay Session` | `sessions/pm-roleplay-training/*.md` | roleplayの1演習セッション |
| `Quiz Session` | `sessions/pm-quiz-ai/*.md` | quizの1回答セッション |
| `Engagement Context` | `context/*.md` | 1案件の目的・背景・ドメイン等 |
| `Preference` | `preferences/*.md` | 明示的な好み・設定(スキル単位) |
| `Quiz Theme Tracker` | `preferences/quiz-theme-streaks.md` | マクロテーマ別の直近正誤追跡(quiz専用) |

## `Competency Assessment`(`competencies/<ID>.md`)

```markdown
---
type: Competency Assessment
title: 契約・損益・原価管理
description: S7 — 契約範囲・見積前提・原価状況を踏まえた判断力の推定レベル。
status: stable
generated: { by: process:pm-mentor-ai, at: 2026-08-21T10:00:00Z }
stale_after: 2026-11-19T00:00:00Z
sources:
  - { id: s1, resource: /sessions/pm-mentor-ai/2026-08-18-contract-scope.md, direction: risk, strength: high }
  - { id: s2, resource: /sessions/pm-mentor-ai/2026-08-21-contract-scope-2.md, direction: maintain, strength: high }
tags: [S7]
---

# 推定レベル

経験不足PM

直近の判断は [2026-08-21のセッション](/sessions/pm-mentor-ai/2026-08-21-contract-scope-2.md)[^s2] と
[2026-08-18のセッション](/sessions/pm-mentor-ai/2026-08-18-contract-scope.md)[^s1] に基づく。

[^s1]: 工数超過を気にしつつ、契約範囲内かどうかを確認せずに追加要望を受けてしまっていた
[^s2]: 「金額や契約に関わる話はその場では答えず持ち帰る」というルールを自分の中に持てば流されずに済むと気づいた
```

`verified`は使わない(agentの推定値そのものであり、誰かが独立確認したものではないため)。未計測のIDはファイル自体を作らない。

## `Judgment Axis`(`judgment-axes/<ID>.md`)

```markdown
---
type: Judgment Axis
title: 政治力学理解(判断軸)
generated: { by: process:pm-mentor-ai, at: 2026-08-20T00:00:00Z }
sources:
  - { id: s1, resource: /sessions/pm-roleplay-training/2026-08-20-scn10.md }
  - { id: s2, resource: /sessions/pm-mentor-ai/2026-08-19-sales-dev-conflict.md }
tags: [S4]
---

# 傾向

対立や利害が絡む場面では、表面的な主張の前に相手の立場・利害を確認しようとする。[^s1][^s2]

[^s1]: 「限定的」という表現ではなく段階的拡張という伝え方の方が手堅く見えると判断した
[^s2]: それぞれが何を守ろうとしているのかを、表面の主張より先に聞きに行きたいと語った
```

`sources`は1件目から必ずこのファイルに記録する(保存の閾値と`synthesis/overview.md`へ反映する閾値は別物)。

## `Profile Synthesis`(`synthesis/overview.md`、唯一のファイル)

```markdown
---
type: Profile Synthesis
title: 総評
generated: { by: process:pm-mentor-ai, at: 2026-08-20T00:00:00Z }
sources:
  - { id: s2, resource: /competencies/S2.md }
  - { id: s4, resource: /competencies/S4.md }
  - { id: s3, resource: /competencies/S3.md }
---

# 強み

交渉の場で、問いかけによって相手の本音を引き出し[^s2]、相手の政治的な事情まで踏まえて
双方が成立する着地点を設計できる[^s4]。

# 伸びしろ

S3 先読み・洞察力: リスクが既に明示されてから対応する傾向があり、相手から提示される前に
自分から兆候に気づいて備えることにまだ伸びしろがある。[^s3]

# 大事にしている判断軸

(judgment-axes/ から2件以上たまったものを統合。省略)

# 最近の変化

(最大2件、新しい順。省略)

# 応対への示唆

(省略)

[^s2]: /competencies/S2.md
[^s3]: /competencies/S3.md
[^s4]: /competencies/S4.md
```

## `Coaching Session` / `Roleplay Session` / `Quiz Session`(`sessions/<skill>/<日付>-<slug>.md`)

```markdown
---
type: Coaching Session
title: 契約範囲の確認が後回しになった件
description: 追加要望をその場で受けてしまい、契約範囲内かどうかの確認が後回しになった相談。
generated: { by: process:pm-mentor-ai, at: 2026-08-21T11:20:00Z }
tags: [S7]
next_action: undecided   # undecided | A | B | C ...
---

# 相談テーマ
追加要望をその場で受けてしまい、契約範囲内かどうかの確認が後回しになった件

# Before → After
Before: 営業がいる場の空気に流されてつい引き受けてしまっていた
After: 金額や契約に関わる話はその場で答えず持ち帰るというルールを持てば流されずに済むと気づいた

# 次のアクション
未確定
```

`type`は`Coaching Session`(mentor-ai)/`Roleplay Session`(roleplay-training)/`Quiz Session`(quiz-ai)をスキルに応じて使い分ける。`next_action`フィールドはmentor-aiのセッションファイルのみ使う。同日・同slugが衝突した場合は`-2`、`-3`と連番を付ける。

## `Engagement Context`(`context/<slug>.md`)

```markdown
---
type: Engagement Context
title: 在庫管理システムリニューアル(仮称)
description: EC事業者向け在庫管理システムのリニューアル案件。
generated: { by: process:pm-mentor-ai, at: 2026-08-10T00:00:00Z }
tags: [ec, inventory]
---

# 案件特性
- ドメイン: EC在庫管理
- 規模: (発言から拾えた範囲のみ記載)
```

案件名から短い英数字slugを作る(例: `acme-ec-renewal`)。明確な案件名が発言から拾えない場合はファイル自体を作らない(推測で埋めない)。同一案件かどうかは`context/index.md`のtitle/tagsとの単純なキーワード一致で候補を絞り、複数該当・不明の場合は断定せずユーザーに確認する。

## `Preference` / `Quiz Theme Tracker`

```markdown
---
type: Preference
title: roleplay 直近設定
generated: { by: process:pm-roleplay-training, at: 2026-08-21T00:00:00Z }
---

- 直近モード: ライト
- 直近シナリオ(内部SCN ID): SCN01
```

`quiz-theme-streaks.md`も同様に、5マクロテーマ×直近5件+連続誤答数をbody表として維持する(既存のテーブル構造をそのまま使う。`profile-template/preferences/quiz-theme-streaks.md`参照)。
