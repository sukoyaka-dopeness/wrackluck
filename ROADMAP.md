# WrackLuck — Roadmap / 実装ロードマップ

> *This document defines what gets built, when, and why.*  
> *本書は何を、いつ、なぜ作るかを定義する。*

---

## Phase 0 — Foundation / 基盤構築

**Goal:** The sea exists. A letter can be thrown and received.  
**目標:** 海が存在する。手紙を投げて、受け取ることができる。

### Infrastructure / インフラ
- [ ] User registration and authentication (anonymous-first)  
  ユーザー登録・認証（匿名優先）
- [ ] Bottle data model with full metadata schema (null fields for future phases)  
  ボトルデータモデル＋フルメタデータスキーマ（将来フィールドはnull）
- [ ] Basic drift queue (server-side time lock: 3–6 hours)  
  基本的な漂流キュー（サーバーサイド時刻ロック：3〜6時間）
- [ ] Receiver selection algorithm (scent affinity + noise)  
  受信者選択アルゴリズム（匂い相性＋ノイズ）

### Core Features / コア機能
- [ ] Write and send a bottle (text only, JP/EN)  
  手紙を書いて投函する（テキストのみ、日・英）
- [ ] Receive a bottle  
  手紙を受け取る
- [ ] Reply via bottle (not DM)  
  ボトルで返信する（DM不可）
- [ ] Scent tag assignment (3–5 fixed categories, manual or random)  
  匂いタグ付与（3〜5種の固定カテゴリ、手動またはランダム）
- [ ] Sea zone assignment from sender's geographic location (4 zones)  
  送信者の緯度経度から海域タグを決定（4海域）
- [ ] current_zone drift (zone changes during voyage)  
  current_zoneの漂流（航海中に海域が変化する）

### AI Seabirds / AI海鳥
- [ ] Arcadia (アルカディア) as sender identity  
  送信者名としてのアルカディア
- [ ] Template-based letter generation (80% template, 20% variable)  
  テンプレートベースの手紙生成（80%テンプレ、20%変数）
- [ ] AI:Human send ratio 2:1  
  AI:人間の送信比率 2:1
- [ ] AI covers all 4 sea zones from launch  
  AIが全4海域をサービス開始時からカバー

### Animal Events / 動物イベント
- [ ] Penguin — express delivery (lock shortened)  
  ペンギン——速達（ロック短縮）
- [ ] Cat — delay (lock extended)  
  猫——遅延（ロック延長）
- [ ] Goat — degradation accelerated  
  ヤギ——劣化加速
- [ ] Sunken bottle (rare event, ~2% probability)  
  沈没ボトル（レアイベント、約2%確率）

### Text Degradation / テキスト劣化
- [ ] Character loss (replaced with `_`)  
  文字欠損（`_`に置換）
- [ ] Character corruption (replaced with `*`)  
  文字化け（`*`に置換）
- [ ] Meaning refraction — kanji substitution with similar-shaped or semantically adjacent characters  
  意味の屈折——形・意味が近い別の漢字への置換
- [ ] Degradation cap at 80%  
  劣化上限80%

### UI / UX
- [ ] No scores, no numerical values in UI  
  スコア・数値をUIに出さない
- [ ] Ambient sound per sea zone (lightweight MP3 loop)  
  海域別環境音（軽量MP3ループ）
- [ ] Haptic feedback on send and receive  
  投函・受取時のハプティクス
- [ ] PWA (Progressive Web App) baseline  
  PWAベースライン

---

## Phase 1 — The Sea Fills / 海が満ちる

**Goal:** The sea feels alive. Voyaging becomes habitual.  
**目標:** 海が生きていると感じられる。航海が習慣になる。

- [ ] Navigation score (hidden internal metric)  
  航海スコア（隠し内部指標）
- [ ] Re-arrival weight tuning based on real usage data  
  実データに基づく再漂着補正値のチューニング
- [ ] AI ratio adjustment as user base grows  
  ユーザー増加に応じたAI比率の調整
- [ ] Expanded AI seabird template library  
  AI海鳥テンプレートライブラリの拡充
- [ ] Multilingual bottle reception (letters in any language arrive as-is)  
  多言語ボトルの受信（どの言語の手紙も原文のまま届く）
- [ ] Deep sea salvage by deep sea fish (rare, high rarity)  
  深海魚によるサルベージ（レア、高レアリティ）

---

## Phase 2 — The Deep Opens / 深海が開く

**Goal:** Long-term voyagers discover what lies beneath.  
**目標:** 長期航海者が、海の底にあるものを発見する。

- [ ] Deep sea archive unlocked by navigation score  
  航海スコアによる深海展示室の解放
- [ ] Silent exhibition of sunken letters (heavily degraded, anonymous)  
  沈んだ手紙たちの無言の展示（劣化済み・匿名）
- [ ] Imperfect translation (opt-in, AI-powered, intentionally incomplete)  
  不完全な翻訳（オプトイン、AI駆動、意図的に不完全）
- [ ] Additional creatures: whale (long haul), migratory bird (cross-border)  
  追加生物：クジラ（長距離輸送）、渡り鳥（国境越え）
- [ ] Sea zone fauna differentiation (each zone has unique animal events)  
  海域ごとの生物の差別化（各海域に固有の動物イベント）

---

## Phase 3 — The World Expands / 世界が広がる

**Goal:** The sea becomes truly global. Languages multiply. The ocean deepens.  
**目標:** 海が本当にグローバルになる。言語が増える。海が深くなる。

- [ ] Multilingual send support (degradation logic per language)  
  多言語送信対応（言語ごとの劣化ロジック）
- [ ] AI scent detection (automatic tag assignment from message content)  
  AI匂い推定（メッセージ内容からタグを自動付与）
- [ ] Real ocean current integration  
  リアル海流データ連携
- [ ] Legendary bottle designation (degradation ≥ 80% + long voyage)  
  レジェンダリーボトルの認定（劣化率80%以上＋長旅）
- [ ] Journey title system (auto-generated from history logs)  
  航海称号システム（履歴ログから自動生成）
- [ ] NFT preservation for legendary bottles  
  レジェンダリーボトルのNFT化

---

## Phase 4 — The Ocean Has No Owner / 海に所有者はいない

**Goal:** Anyone can run their own sea.  
**目標:** 誰でも自分の海を立てられる。

- [ ] Federated architecture design  
  フェデレーション型アーキテクチャの設計
- [ ] Inter-server bottle drift protocol  
  サーバー間ボトル漂流プロトコル
- [ ] ActivityPub compatibility investigation  
  ActivityPub互換性の調査
- [ ] Fork documentation and self-hosting guide  
  フォーク・セルフホストガイドの整備

---

## Locked Principles / 変更不可原則

Regardless of phase, the following must never change.  
フェーズに関わらず、以下は変更不可である。

- **1 sent, 1 received.** / 1送信・1受信
- **No DM.** / DM禁止
- **No scores in UI.** / UIに数値を出さない
- **No likes or reactions.** / いいね・リアクション禁止
- **Minimum drift time: 3–6 hours.** / 最短漂流時間：3〜6時間
- **Degradation cap: 80%.** / 劣化上限：80%
