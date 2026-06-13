# Surokkha AI — MVP Plan

---

## MVP Goal

> **একটা Android app যেটা বাংলাদেশি নারীদের WhatsApp + SMS-এ bKash/Nagad financial scam আর romance/বিয়ে স্ক্যাম real-time-এ ধরবে এবং নিজেই সতর্ক করবে।**

**MVP scope: শুধু দুইটা scam type, শুধু Android, শুধু Bangla + Banglish।**

Everything else is Phase 2.

---

## Why These Two Scam Types First

| scam type | কারণ MVP-তে |
|---|---|
| **bKash/Nagad financial scam** | সবচেয়ে concrete rules আছে, highest accuracy সম্ভব (rule-based), bKash নিজে pattern document করেছে, immediate tangible harm |
| **Romance/বিয়ে স্ক্যাম** | বাংলাদেশে সবচেয়ে emotionally damaging, AIVA narrative-এর জন্য perfect, academic dataset আছে (Suarez-Tangil, FTC scripts), CID cases documented |

**MVP-তে নেই:** grooming (বেশি complex, data কম), cyberbullying, deepfake — Phase 2।

---

## Timeline: 12 Weeks to Working Beta

### Phase 0 — Week 1–2: Foundation & Data

**Goal: Development environment ready + initial dataset**

#### Week 1
- [ ] Android project setup (Kotlin + Jetpack Compose)
- [ ] `NotificationListenerService` proof of concept
  - WhatsApp notification থেকে sender + message text extract করা
  - SMS notification test
  - Messenger test
- [ ] Basic onboarding flow (Notification Access permission grant)

#### Week 2
- [ ] Dataset collection শুরু:
  - Download: BD-SHS, Bangalabarta, BanTH, TB-OLID
  - Download: scamdiggerprofiles (romance scam)
  - Collect: bKash scam patterns (bKash official page + news articles manually)
  - Label: ২০০টা manual Bangla/Banglish example (financial + romance)
- [ ] Hard rules engine প্রথম version (Kotlin):
  - OTP/PIN request patterns (Bangla + English)
  - Non-shortcode transaction SMS detection
  - Basic lottery patterns

**Deliverable:** Notification থেকে message read হচ্ছে + rules fire হচ্ছে locally।

---

### Phase 1 — Week 3–6: ML Model

**Goal: On-device Bangla scam classifier**

#### Week 3–4: Data preparation
- [ ] Dataset merge + clean:
  - Financial scam: Bangalabarta + bKash patterns + UCI SMS (English)
  - Romance scam: scamdigger profiles (text only) + FTC 7 scripts (synthetic Bangla)
  - Safe: BD-SHS non-toxic subset + normal chat samples
- [ ] Labeling scheme:
  ```
  0 = safe
  1 = financial_scam (bKash/OTP/lottery)
  2 = romance_scam (marriage promise/love-bombing/off-platform push)
  ```
- [ ] Train/val/test split (80/10/10), stratified

#### Week 5: Model training
- [ ] Fine-tune BanglaBERT (csebuetnlp/banglabert) on merged dataset
  - Google Colab Pro / Kaggle (free GPU)
  - Target: ~88%+ accuracy on test set
- [ ] Fine-tune MuRIL (google/muril-base-cased) on Banglish subset (BanTH + TB-OLID)
  - Target: ~80%+ on Banglish
- [ ] Evaluate: precision/recall/F1 per class, confusion matrix

#### Week 6: On-device conversion
- [ ] Convert to TFLite:
  ```python
  converter = tf.lite.TFLiteConverter.from_saved_model(saved_model_path)
  converter.optimizations = [tf.lite.Optimize.DEFAULT]  # INT8 quantization
  tflite_model = converter.convert()
  ```
- [ ] Integrate TFLite into Android project
- [ ] Script detection (Bangla Unicode vs Latin) → model routing
- [ ] Benchmark: inference time on test device (target < 200ms)

**Deliverable:** On-device model classifying Bangla + Banglish messages with confidence score।

---

### Phase 2 — Week 7–9: Alert UI + Full Integration

**Goal: Complete end-to-end flow, user-facing**

#### Week 7: Alert system
- [ ] 3-tier alert logic:
  ```
  confidence < 0.50 → no alert
  0.50–0.75 → soft yellow warning card
  > 0.75 → red alert with explanation
  ```
- [ ] Explanation builder:
  - Top 2 reasons surface করা
  - Bangla text: "কারণ: OTP চাওয়া হয়েছে + অচেনা নম্বর"
- [ ] Alert UI (Jetpack Compose):
  - Overlay notification (non-intrusive)
  - Bottom sheet with detail + actions
  - [এটা ঠিক আছে] [scam report করুন] buttons

#### Week 8: Conversation context
- [ ] In-memory conversation buffer (last 20 messages per thread)
- [ ] Basic temporal scoring:
  - Love-bombing pattern (fast relationship talk)
  - Money request after trust-building
  - Off-platform push ("Telegram-এ আসো")
- [ ] Sender reputation (local SQLite):
  - User-reported senders store করা
  - Future: server sync

#### Week 9: Polish + edge cases
- [ ] Handle notification collapse ("5 messages" → request expansion)
- [ ] WhatsApp notification preview off → graceful degradation
- [ ] App icon + persistent notification (Play policy compliance)
- [ ] Settings screen: on/off per app, sensitivity level
- [ ] Bangla UI throughout

**Deliverable:** Full working app — notification আসে, model চলে, alert দেখায়, explanation বলে।

---

### Phase 3 — Week 10–11: Testing & Feedback

**Goal: Real users, real messages, real feedback**

#### Week 10: Closed beta
- [ ] ২০–৩০ জন beta user recruit করো:
  - বয়স ১৮–৩৫, smartphone user, WhatsApp active
  - Mix: students, working women, housewives
  - Dhaka + other districts
- [ ] Instrumentation:
  - Alert fire হলে user কী করল (dismiss/report/learn more) — analytics
  - False positive: "ঠিক আছে" press → log করো
- [ ] Feedback form (Google Form, Bangla):
  - "কোনো scam miss হয়েছে?" (false negative)
  - "ভুলভাবে flag হয়েছে?" (false positive)
  - "কতটা useful মনে হয়েছে?" (1–5)

#### Week 11: Iterate
- [ ] Top false positives analyze + fix rules/threshold
- [ ] Miss করা scam patterns → dataset-এ add → retrain
- [ ] Performance issues fix (battery, memory)
- [ ] Crash reports fix

**Target metrics after beta:**
- False positive rate < 15% (goal < 10%)
- User satisfaction > 3.5/5
- < 5% daily battery drain

---

### Phase 4 — Week 12: Launch Prep

**Goal: Play Store-ready + AIVA demo**

- [ ] Play Store listing prepare:
  - App name: সুরক্ষা AI
  - Description (Bangla + English)
  - Screenshots
  - Privacy policy (mandatory — explains notification access, no data upload)
- [ ] Play Console: Notification Listener permission declaration submit
- [ ] AIVA demo script prepare
- [ ] Press kit: 1-pager, demo video (screen recording)
- [ ] Launch: soft launch (invite-only), না public — তোমার decision

---

## MVP Feature Scope (Clear Boundaries)

### ✅ IN MVP

| Feature | Detail |
|---|---|
| WhatsApp notification scan | message text + sender name |
| SMS scan | incoming SMS body |
| Messenger scan | notification text |
| Financial scam detection | bKash/Nagad OTP, lottery, fake transaction |
| Romance scam detection | marriage promise, love-bombing, off-platform push |
| Hard rules engine | instant, rule-based detection |
| AI classifier | BanglaBERT + MuRIL, on-device |
| Bangla + Banglish | both script types |
| 3-tier alert | silent / soft warn / strong alert |
| Explanation | top 2 reasons shown to user |
| Manual report | user can mark as scam/not scam |
| Local sender reputation | reported senders remembered |
| Offline mode | works without internet |
| Persistent notification | Play policy compliance |

### ❌ NOT IN MVP (Phase 2+)

| Feature | Why deferred |
|---|---|
| Instagram DM scan | Instagram blocks 3rd-party, complex |
| imo / Telegram scan | nice-to-have, not highest impact |
| Image/deepfake detection | different tech stack, scope too big |
| Voice call analysis | requires microphone, very complex |
| iOS app | different platform, different architecture |
| Cloud sync / family alerts | privacy complexity, scope too big |
| Community report server | needs backend, moderation |
| Grooming detection | needs more data, more nuance |

---

## Resources Needed

### Development
- 1–2 Android developer (Kotlin) + 1 ML engineer (Python/TFLite)
- Or: 1 full-stack person with both skills (ambitious but doable for MVP)

### Compute (Budget-friendly)
- Kaggle / Google Colab Pro: **free–$10/month** — model training
- AWS t3.micro: **~$10/month** — lightweight backend (Phase 1: not needed)
- No GPU server needed — training once, inference on-device

### Data (All free/open)
- BD-SHS: GitHub (free)
- BanTH, TB-OLID: arXiv/GitHub (free)
- Bangalabarta: Mendeley (free)
- scamdiggerprofiles: GitHub (free)
- Manual labeling: ~৪০ ঘণ্টা human time

### Timeline: 12 weeks solo or 8 weeks with a co-founder

---

## Success Metrics for MVP

| Metric | Target |
|---|---|
| Detection accuracy (Bangla) | ≥ 85% |
| Detection accuracy (Banglish) | ≥ 78% |
| False positive rate | ≤ 15% |
| App size | ≤ 80 MB |
| Battery drain | ≤ 2%/day |
| Beta users | ≥ 20 |
| User satisfaction | ≥ 3.5/5 |
| Play Store: published | ✅ |

---

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Play Store rejects app for notification access | Medium | High | Submit permission declaration early; frame as user-consent tool, not surveillance |
| WhatsApp collapses notifications (no message body) | High | Medium | Still catch sender name + basic pattern; educate user in onboarding |
| Banglish accuracy too low (< 75%) | Medium | Medium | Supplement with more rules; be transparent about limitations |
| User doesn't trust AI — ignores alerts | Medium | High | Explanation ("কেন flag করলাম") + education flow in onboarding |
| AI safety filters for LLM-generated scams | High | Medium | Rule-based layer catches structural patterns regardless of phrasing quality |
| Privacy concern — "who reads my chat?" | High | High | On-device emphasis everywhere; no server upload of message content |

---

## Post-MVP Roadmap (3–6 months)

```
Month 1–3 (MVP):
└── bKash/romance scam, WhatsApp + SMS, Bangla + Banglish

Month 3–6 (Growth):
├── Community report server (crowdsourced reputation)
├── More scam types: investment fraud, pig-butchering
├── imo + Telegram support
├── Family alert (user-controlled: opt-in, sends to trusted contact)
└── 10,000 active users target

Month 6–12 (Expansion):
├── B2B: NGO pilot, bKash/Nagad API integration
├── Image analysis (fake profile photo detection)
├── Federated learning (improve model from corrections)
└── South Asian diaspora marketing (UK Bangladeshi community)
```

---

## First 3 Things To Do Tomorrow

1. **Create Android project** — NotificationListenerService দিয়ে WhatsApp notification থেকে message read করার proof of concept বানাও (৩–৪ ঘণ্টা)।

2. **Download the datasets** — BD-SHS, Bangalabarta, BanTH তিনটা GitHub/Mendeley থেকে নামাও, একটা folder-এ রাখো (১ ঘণ্টা)।

3. **Read the bKash fraud page** — [bkash.com/en/help/avoid-fraud](https://www.bkash.com/en/help/avoid-fraud) — ওখান থেকে ১০টা specific pattern manually list করো। এগুলোই তোমার first hard rules।
