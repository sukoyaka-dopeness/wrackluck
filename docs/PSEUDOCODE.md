# WrackLuck — Pseudocode / 擬似コード

> *This document describes the logic of WrackLuck in plain language and code-like notation.*  
> *本書はWrackLuckのロジックを平易な言語とコード風表記で記述する。*
>
> *This is not implementation code. It is a specification of intent.*  
> *これは実装コードではない。意図の仕様書である。*
> 
---

## 1. The Lifecycle of a Bottle / ボトルのライフサイクル

```
WHEN user sends a bottle:

  1. CREATE bottle with full metadata schema
     (null fields reserved for future phases)

  2. ASSIGN origin_zone
     from sender's geographic coordinates

  3. ASSIGN initial_smell
     from user selection OR random

  4. SET is_drift_complete = false
  SET current_degradation = 0.0

  5. BEGIN drift process (async)

  6. NOTIFY sender: "your bottle has been thrown into the sea"
```

---

## 2. Drift Process / 漂流プロセス

```
FUNCTION drift(bottle):

  # --- Step 1: Time Lock ---
  lock_hours = random_float(3.0, 6.0)
  WAIT until now >= bottle.timestamp_origin + lock_hours

  # --- Step 2: Animal Event ---
  roll = random_float(0.0, 1.0)

  IF roll < 0.02:
    APPLY sinking(bottle)
    RETURN  # bottle exits normal drift

  ELIF roll < 0.07:
    APPLY animal(bottle, "penguin")   # express: reduce remaining lock
    lock_hours = lock_hours * 0.4

  ELIF roll < 0.13:
    APPLY animal(bottle, "cat")       # delay: extend lock
    lock_hours = lock_hours * 1.8

  ELIF roll < 0.20:
    APPLY animal(bottle, "goat")      # degrade: accelerate degradation

  # --- Step 3: Zone Drift ---
  IF random_float(0.0, 1.0) < 0.15:
    bottle.current_zone = random_zone(exclude=bottle.current_zone)
    LOG event: "current_shift"

  # --- Step 4: Degradation ---
  bottle.current_degradation = calculate_degradation(bottle, elapsed_hours)

  # --- Step 5: Mark Complete ---
  bottle.is_drift_complete = true

  # --- Step 6: Select Receiver ---
  receiver = select_receiver(bottle)
  DELIVER bottle TO receiver
  LOG event: "landfall"
```

---

## 3. Animal Events / 動物イベント

```
FUNCTION apply_animal(bottle, animal_type):

  IF animal_type == "penguin":
    LOG event: "animal_touch", detail: "penguin_speedup"
    # lock reduction handled in drift()

  ELIF animal_type == "cat":
    LOG event: "animal_touch", detail: "cat_delayed"
    # lock extension handled in drift()

  ELIF animal_type == "goat":
    bottle.current_degradation += random_float(0.1, 0.25)
    bottle.current_degradation = min(bottle.current_degradation, 0.8)
    LOG event: "animal_touch", detail: "goat_nibbled"


FUNCTION apply_sinking(bottle):

  bottle.status = "sunken"
  LOG event: "sunken"

  # bottle waits in the deep
  # degradation continues to accumulate
  # deep sea fish may salvage it later (see Section 7)
```

---

## 4. Degradation / テキスト劣化

```
FUNCTION calculate_degradation(bottle, elapsed_hours):

  base_rate = 0.03 per hour
  rate = base_rate

  IF bottle had goat event:
    rate = rate * 2.0

  new_degradation = bottle.current_degradation + (rate * elapsed_hours)
  RETURN min(new_degradation, 0.8)   # hard cap at 80%


FUNCTION degrade_text(text, degradation_rate):

  result = []

  FOR each character IN text:

    roll = random_float(0.0, 1.0)

    IF roll < degradation_rate * 0.3:
      result.APPEND("_")                          # loss / 欠損

    ELIF roll < degradation_rate * 0.5:
      result.APPEND("*")                          # corruption / 文字化け

    ELIF roll < degradation_rate * 0.6
         AND character is kanji:
      result.APPEND(refract_kanji(character))     # meaning refraction / 意味の屈折

    ELSE:
      result.APPEND(character)                    # original / 原文維持

  RETURN join(result)


FUNCTION refract_kanji(character):
  # Returns a kanji with similar shape or adjacent meaning
  # e.g. 海 → 毎, 愛 → 哀, 明 → 盲
  # Pool defined per character during implementation
  RETURN similar_kanji_pool[character].random()
```

---

## 5. Receiver Selection / 受信者選択

```
FUNCTION select_receiver(bottle):

  candidates = get_active_users(
    exclude = bottle.sender_id
  )

  scored = []

  FOR each user IN candidates:

    score = 0.0

    # Primary: scent affinity
    score += scent_affinity(bottle.current_smell, user.smell_preference)

    # Secondary: re-arrival weight (prior connection)
    IF has_prior_contact(bottle.sender_id, user.id):
      score += bottle.re_arrival_weight     # max 0.08

    # Tertiary: cultural zone match (weak)
    IF bottle.current_zone == user.origin_zone:
      score += 0.02

    # Noise: the sea (philosophical core)
    score += random_float(0.0, 0.15)

    scored.APPEND((user, score))

  # Weighted random selection
  # Higher score = more likely, but not guaranteed
  RETURN weighted_random_select(scored)


NOTE:
  The noise range random_float(0.0, 0.15) is a philosophical parameter.
  Wider range = wilder sea = more strangers.
  Narrower range = stronger bonds = less surprise.
  Tune with intention, not just for performance.

  ノイズ幅は哲学的パラメータである。
  広いほど海は荒れ、見知らぬ人に届く。
  狭いほど縁が強くなり、驚きが減る。
  性能ではなく、意図をもってチューニングすること。
```

---

## 6. Scent Affinity / 匂い相性

```
FUNCTION scent_affinity(bottle_smells, user_preferences):

  # Both are arrays of scent tags
  # e.g. bottle_smells = ["melancholy", "quiet"]
  #      user_preferences = ["quiet", "absurd"]

  matches = intersection(bottle_smells, user_preferences)
  total   = union(bottle_smells, user_preferences)

  IF total is empty:
    RETURN 0.0

  # Jaccard similarity — shared / total
  RETURN length(matches) / length(total)

  # Result range: 0.0 (no match) to 1.0 (perfect match)
  # In practice, most scores will be 0.0–0.5
```

---

## 7. Sunken Bottle Salvage / 沈没ボトルのサルベージ

```
# Run periodically (e.g. every few hours, server-side)

FUNCTION salvage_check():

  sunken_bottles = get_all_sunken_bottles()

  FOR each bottle IN sunken_bottles:

    roll = random_float(0.0, 1.0)

    IF roll < 0.01:   # ~1% chance per check — extremely rare

      bottle.status = "drifting"
      LOG event: "animal_touch", detail: "deep_fish_salvage"

      # Continue degradation accumulation
      # Receiver selection proceeds normally
      receiver = select_receiver(bottle)
      DELIVER bottle TO receiver
      LOG event: "landfall"
```

---

## 8. Re-arrival Weight / 再漂着補正

```
FUNCTION update_re_arrival_weight(sender_id, receiver_id, event):

  bond = get_bond(sender_id, receiver_id)

  IF event == "first_landfall":
    bond.weight = 0.05
    bond.last_updated = now

  ELIF event == "reply_success":
    bond.weight = min(bond.weight + 0.03, 0.08)
    bond.last_updated = now


# Run periodically (e.g. daily)

FUNCTION decay_re_arrival_weights():

  FOR each bond IN all_bonds:

    days_elapsed = (now - bond.last_updated).days

    IF days_elapsed >= 60:
      DELETE bond

    ELIF days_elapsed >= 30:
      bond.weight = bond.weight * 0.5
```

---

## 9. Navigation Score / 航海スコア

```
FUNCTION calculate_navigation_score(user_id):

  stats = get_user_stats(user_id)

  score =
      stats.total_sends    * 1.0
    + stats.total_receives * 1.0
    + stats.total_replies  * 2.0
    + stats.total_sinkings * 5.0
    + stats.days_active    * 0.1

  RETURN score


FUNCTION check_deep_archive_unlock(user_id):

  score = calculate_navigation_score(user_id)

  THRESHOLD = [to be tuned during implementation]

  IF score >= THRESHOLD
     AND user.deep_archive_unlocked == false:

    user.deep_archive_unlocked = true

    # No notification. No explanation.
    # The door simply appears.
    # 理由は伝えない。ある日、静かに扉が現れる。
```

---

## 10. AI Seabird Dispatch / AI海鳥の送信

```
# Run periodically to maintain AI:Human ratio of 2:1

FUNCTION dispatch_ai_seabirds():

  human_sends_recent = count_human_sends(last_hours=1)
  ai_sends_needed    = human_sends_recent * 2

  FOR i IN range(ai_sends_needed):

    bottle = create_ai_bottle(
      sender_id   = ARCADIA_ID,
      origin_zone = random_zone(),      # all 4 zones equally
      content     = generate_ai_letter(),
      smell       = random_smell_tags()
    )

    BEGIN drift(bottle)


FUNCTION generate_ai_letter():

  roll = random_float(0.0, 1.0)

  IF roll < 0.8:
    RETURN fill_template(random_template())   # 80% template-based

  ELSE:
    RETURN generate_variable_letter()         # 20% rule-based variable

  # Letter styles:
  # - Strange apology letters / 変な謝罪文
  # - Hollow business emails / 間の抜けたビジネスメール
  # - Incoherent poems / 意味不明な詩
  # - Routine sea condition reports / 日常的な海況報告
```

---

## Appendix: Tunable Parameters / チューニング可能なパラメータ

The following values are not fixed. They should be tuned based on user experience data.  
以下の値は固定ではない。ユーザー体験データに基づいてチューニングすること。

| Parameter | MVP Default | Notes |
|---|---|---|
| Time lock | 3–6 hours | Core to the "slow" experience |
| Noise range | 0.0–0.15 | Philosophical parameter — tune with care |
| Sinking probability | ~2% | Rare enough to feel special |
| Salvage probability | ~1% per check | Extremely rare |
| Degradation rate | 0.03/hour | Adjust per language in future phases |
| AI:Human ratio | 2:1 | Reduce as user base grows |
| Re-arrival max weight | 0.08 | Keep low to preserve randomness |
| Navigation score threshold | TBD | Set after observing real usage patterns |
