# WrackLuck — Specification / 仕様書

> *This document defines what WrackLuck is made of.*  
> *本書はWrackLuckが何で作られているかを定義する。*

---

## 1. Core Model / コアモデル

### 1.1 The Bottle / ボトル

A bottle is the fundamental unit of WrackLuck.  
ボトルはWrackLuckの基本単位である。

- One sender → one receiver. Always. / 1送信→1受信。常に。
- A bottle cannot be recalled once sent. / 投函後のボトルは取り消せない。
- A bottle can receive one reply — also a bottle. / ボトルへの返信は1通のみ、返信もボトルである。
- A bottle is never duplicated or broadcast. / ボトルは複製・配信されない。

### 1.2 The Journey / 旅

Every bottle undergoes a journey before arriving.  
すべてのボトルは、届く前に旅をする。

1. **Time lock** — minimum 3–6 hours after sending before the bottle enters the drift queue. / 時刻ロック——送信後3〜6時間は漂流キューに入らない。
2. **Animal event** — probabilistic intervention during drift. / 動物イベント——漂流中の確率的介入。
3. **Degradation** — text changes during the voyage. / 劣化——航海中にテキストが変化する。
4. **Receiver selection** — a weighted random process determines who receives the bottle. / 受信者選択——重み付きランダムによって受信者が決まる。

---

## 2. Metadata Schema / メタデータスキーマ

Every bottle carries the following data structure from the moment it is created.  
すべてのボトルは、生成された瞬間から以下のデータ構造を持つ。

```json
{
  "bottle_id": "UUID",
  "sender_id": "hashed (non-identifiable)",
  "timestamp_origin": "ISO 8601",
  "origin_zone": "mediterranean | polynesian | south_american | african",
  "current_zone": "mediterranean | polynesian | south_american | african",
  "initial_smell": ["tag1", "tag2"],
  "current_smell": ["tag1", "tag2"],
  "current_degradation": 0.0,
  "is_drift_complete": false,
  "re_arrival_weight": 0.05,
  "history_logs": [
    {
      "event_type": "animal_touch | current_shift | landfall | sunken | salvage",
      "event_detail": "goat_nibbled | penguin_speedup | cat_delayed | deep_fish_salvage",
      "timestamp": "ISO 8601",
      "zone": "mediterranean | polynesian | south_american | african"
    }
  ],
  "total_distance_km": null,
  "elapsed_days": null,
  "language_origin": null,
  "translation_state": null,
  "rarity_score": null,
  "title": null,
  "nft_hash": null
}
```

**Notes / 注記:**
- `null` fields are reserved for future phases. Do not remove them. / `null`フィールドは将来フェーズのために確保する。削除しないこと。
- `history_logs` is append-only. Never overwrite. / `history_logs`は追記のみ。上書き禁止。
- No raw values are ever shown in the UI. All values are expressed as sensation. / 生数値はUIに出さない。すべて感触として表現する。

---

## 3. Sea Zones / 海域

### 3.1 Assignment / 割り当て

`origin_zone` is derived from the sender's geographic location at the time of sending.  
`origin_zone`は送信時の送信者の地理的位置から決定される。

| Region / 地域 | Zone |
|---|---|
| East Asia, Pacific / 東アジア・太平洋 | `polynesian` |
| Europe, North Africa, Middle East / 欧州・北アフリカ・中東 | `mediterranean` |
| North and South America / 南北アメリカ | `south_american` |
| Africa, South Asia / アフリカ・南アジア | `african` |

### 3.2 Drift / 漂流

`current_zone` changes during the voyage based on animal events and current shifts.  
`current_zone`は動物イベントや海流変化に応じて航海中に変化する。

A bottle sent from Japan may arrive via the African zone.  
日本から出たボトルがアフリカ海域を経由して届く可能性がある。

### 3.3 AI Coverage / AIカバレッジ

AI seabirds send from all 4 zones from day one.  
AI海鳥はサービス開始初日から全4海域から送信する。

This ensures the sea feels global even when the user base is concentrated in one region.  
これにより、ユーザーが一地域に集中していても、海はグローバルに感じられる。

---

## 4. Scent System / 匂いシステム

### 4.1 MVP Tags (fixed) / MVPタグ（固定）

Scent tags are assigned at the time of sending — manually selected or randomly assigned.  
匂いタグは送信時に付与される——手動選択またはランダム付与。

MVP uses 3–5 fixed categories. Exact categories to be determined during implementation.  
MVPでは3〜5種の固定カテゴリを使用する。具体的なカテゴリは実装時に決定する。

Example categories: `melancholy`, `humour`, `longing`, `absurd`, `quiet`  
カテゴリ例：`melancholy`, `humour`, `longing`, `absurd`, `quiet`

### 4.2 UI Expression / UI表現

Scent is never shown as a label or score.  
匂いはラベルやスコアとして表示しない。

Expressed as sensation only — e.g. *「潮の香りが強くなってきた」*  
感触としてのみ表現する——例：*「潮の香りが強くなってきた」*

### 4.3 Future / 将来

Post-MVP: AI analyses message content and assigns scent tags automatically.  
Post-MVP：AIがメッセージ内容を解析し、匂いタグを自動付与する。

---

## 5. Receiver Selection / 受信者選択

Selection is a weighted random process — a tilted roulette, not a filter.  
受信者選択は重み付きランダム——フィルタではなく、傾いたルーレット。

```
score = 0.0
score += scent_affinity(bottle.current_smell, user.smell_preference)  // primary
score += re_arrival_weight  // if prior contact exists (max 0.08)
score += 0.02  // if cultural zone matches
score += random(0.0, 0.15)  // noise — this is the sea
```

The noise value `random(0.0, 0.15)` is the philosophical heart of the algorithm.  
ノイズ値`random(0.0, 0.15)`がアルゴリズムの哲学的心臓部である。

A larger noise range means a wilder sea. A smaller range means stronger bonds.  
ノイズ幅が大きいほど海は荒れる。小さいほど縁が強くなる。

This value is a tunable philosophical parameter, not merely a technical one.  
この値は技術的なパラメータではなく、哲学的なパラメータとしてチューニングする。

---

## 6. Text Degradation / テキスト劣化

Degradation is applied character by character during the voyage.  
劣化は航海中、一文字ずつ適用される。

### 6.1 Types / 種類

| Type | Expression | Trigger probability |
|---|---|---|
| Loss / 欠損 | `_` | `degradation × 0.3` |
| Corruption / 文字化け | `*` | `degradation × 0.5` |
| Refraction / 意味の屈折 | similar kanji / 意味・形が近い別の漢字 | `degradation × 0.6` (kanji only) |

### 6.2 Cap / 上限

Maximum degradation: **80%**.  
最大劣化率：**80%**。

A letter that is completely unreadable is no longer an experience.  
完全に読めなくなった手紙は体験にならない。

### 6.3 Legendary Threshold / レジェンダリー閾値

Degradation ≥ 80% + long voyage = Legendary designation (Post-MVP).  
劣化率80%以上＋長旅 = レジェンダリー認定（Post-MVP）。

---

## 7. Animal Events / 動物イベント

### 7.1 MVP Animals / MVP生物

| Animal | Effect | Probability |
|---|---|---|
| 🐧 Penguin | Express — time lock shortened / 速達——ロック短縮 | ~5% |
| 🐱 Cat | Delay — time lock extended / 遅延——ロック延長 | ~6% |
| 🐐 Goat | Degradation accelerated / 劣化加速 | ~7% |
| 🌊 Sinking | Bottle enters sunken state / 沈没状態に入る | ~2% |

### 7.2 Sunken Bottles / 沈没ボトル

A sunken bottle is not lost. It waits in the deep.  
沈没したボトルは失われない。深海で待つ。

- Degradation continues to accumulate while sunken. / 沈没中も劣化は蓄積し続ける。
- Randomly salvaged by deep sea fish and delivered to a random receiver. / ランダムに深海魚によってサルベージされ、ランダムな受信者に届く。
- Extremely high rarity. / 極めて高いレアリティ。

### 7.3 Future Animals / 将来の生物（Post-MVP）

Each sea zone will have its own fauna. Examples:  
各海域に固有の生物が存在する。例：

| Zone | Creature | Effect |
|---|---|---|
| Mediterranean | Octopus / タコ | Ink burst — extra degradation / インク噴射——追加劣化 |
| Polynesian | Sea turtle / ウミガメ | Slow drift — extended voyage / ゆっくり運ぶ——航海延長 |
| African | Pelican / ペリカン | Misdelivery — wrong receiver / 誤配——別の受信者へ |
| South American | Dolphin / イルカ | Playful redirect — zone change / 気まぐれ転送——海域変更 |

---

## 8. AI Seabirds / AI海鳥

### 8.1 Role / 役割

**Flow補填:** During early phases, AI sends at 2:1 ratio to fill the sea.  
**流量補填:** 初期フェーズ、AIが2:1の比率で送信し海を満たす。

**Flotsam generation:** AI letters are noise, foam, and debris — not human imitation.  
**漂流物生成:** AI手紙はノイズ・泡沫・漂流物——人間の模倣ではない。

### 8.2 Identity / アイデンティティ

Sender name: **Arcadia / アルカディア**  
Personality: weak. No strong narrative.  
人格：弱い。強い物語は持たない。

Letter style examples:  
手紙スタイル例：
- Strange apology letters / 変な謝罪文
- Hollow business emails / 間の抜けたビジネスメール
- Incoherent poems / 意味不明な詩
- Routine sea condition reports / 日常的な海況報告

### 8.3 Generation / 生成方式

MVP: 80% template + 20% variable (rule-based).  
MVP：80%テンプレート＋20%変数（ルールベース）。

No live API generation in MVP — cost control and quality stability.  
MVPではAPIリアルタイム生成は行わない——コスト削減・品質安定のため。

---

## 9. Re-arrival Weight / 再漂着補正

A hidden metric that tilts the sea slightly toward prior connections.  
過去の縁に向けて海をわずかに傾ける隠し指標。

| Event | Weight change |
|---|---|
| First arrival | +5% |
| Successful reply | +3% (max total: 8%) |
| After ~30 days | Halved / 半減 |
| After ~60 days | Gone / 消滅 |

Permanent connections are not formed.  
永続的な縁は生まれない。

---

## 10. Navigation Score / 航海スコア

A hidden internal metric. Never shown to the user.  
隠された内部指標。ユーザーには絶対に見せない。

```
navigation_score =
    sends      × 1.0
  + receives   × 1.0
  + replies    × 2.0   // a bond was formed
  + sinkings   × 5.0   // a rare experience
  + days       × 0.1   // time spent at sea
```

Used to unlock the Deep Sea Archive.  
深海展示室の解放条件として使用する。

The reason for unlocking is never communicated to the user.  
解放理由はユーザーに伝えない。

One day, the door simply appears.  
ある日、静かに扉が現れる。

---

## 11. Translation Policy / 翻訳ポリシー

Foreign letters arrive untranslated by default.  
異国の手紙はデフォルトで翻訳されずに届く。

No translation UI in MVP.  
MVPでは翻訳UIは存在しない。

Post-MVP: opt-in, AI-powered, intentionally imperfect translation.  
Post-MVP：オプトイン、AI駆動、意図的に不完全な翻訳。（課金要素候補）

A fragment of the original language is always left untranslated.  
元の言語の響きが、必ず一部残される。

---

## 12. Prohibited Features / 禁止事項

The following must never be implemented, regardless of phase.  
以下はいかなるフェーズにおいても実装してはならない。

- ❌ Direct messaging (DM) / ダイレクトメッセージ
- ❌ Likes, reactions, or any acknowledgement button / いいね・リアクション・承認ボタン
- ❌ Rankings or leaderboards / ランキング・リーダーボード
- ❌ Scores or numerical values in UI / UIへの数値表示
- ❌ Broadcast (1 sender → multiple receivers) / 配信（1送信→複数受信）
- ❌ Bottle recall after sending / 送信後のボトル取り消し
- ❌ Full control over drift destination / 漂流先の完全制御
