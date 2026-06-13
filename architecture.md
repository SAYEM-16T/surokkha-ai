# Surokkha AI — Technical Architecture

---

## Overview: System Design Philosophy

**3 টা মূল নীতি যা সব সিদ্ধান্ত drive করে:**

1. **On-device first** — user-এর chat কোনো server-এ যায় না
2. **Consent-visible** — app সবসময় দেখা যায়, notification থাকে
3. **Layered detection** — fast rules আগে, heavy AI পরে

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     USER'S ANDROID PHONE                    │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐  │
│  │  WhatsApp    │  │  Messenger   │  │  SMS / imo / etc │  │
│  └──────┬───────┘  └──────┬───────┘  └────────┬─────────┘  │
│         │                 │                    │            │
│         └────────────┬────┘────────────────────┘            │
│                      ↓                                      │
│         ┌────────────────────────┐                          │
│         │  NotificationListener  │  ← Android API           │
│         │     Service            │    (user grants once)    │
│         └────────────┬───────────┘                          │
│                      ↓                                      │
│         ┌────────────────────────┐                          │
│         │   Message Preprocessor │                          │
│         │  - script detection    │                          │
│         │    (Bangla / Banglish) │                          │
│         │  - sender metadata     │                          │
│         │  - link extraction     │                          │
│         └────────────┬───────────┘                          │
│                      ↓                                      │
│    ┌─────────────────────────────────────┐                  │
│    │         DETECTION ENGINE            │                  │
│    │                                     │                  │
│    │  Layer 1: Hard Rules (< 1ms)        │                  │
│    │  ├─ OTP/PIN request pattern         │                  │
│    │  ├─ Non-official shortcode check    │                  │
│    │  ├─ Lottery/prize keywords          │                  │
│    │  └─ Known scam sender list          │                  │
│    │                                     │                  │
│    │  Layer 2: AI Classifier (< 200ms)   │                  │
│    │  ├─ Script router                   │                  │
│    │  │   ├─ Bangla → BanglaBERT-tiny    │                  │
│    │  │   └─ Banglish → MuRIL-quantized  │                  │
│    │  ├─ Scam type classifier            │                  │
│    │  │   ├─ financial_scam              │                  │
│    │  │   ├─ romance_scam                │                  │
│    │  │   ├─ grooming                    │                  │
│    │  │   └─ safe                        │                  │
│    │  └─ Confidence score (0.0 – 1.0)   │                  │
│    │                                     │                  │
│    │  Layer 3: Conversation Context      │                  │
│    │  ├─ Conversation history buffer     │                  │
│    │  │   (last N messages, in-memory)   │                  │
│    │  ├─ Temporal pattern scoring        │                  │
│    │  │   (love-bombing over time)       │                  │
│    │  └─ Sender reputation (local DB)   │                  │
│    └─────────────────────────────────────┘                  │
│                      ↓                                      │
│         ┌────────────────────────┐                          │
│         │    Alert Decision      │                          │
│         │  confidence < 0.5  → 🔕 silent                    │
│         │  0.5 – 0.75        → 💛 soft warn                 │
│         │  > 0.75            → 🔴 strong alert              │
│         └────────────┬───────────┘                          │
│                      ↓                                      │
│         ┌────────────────────────┐                          │
│         │   Alert UI + Explain   │                          │
│         │  "কেন flag করলাম:"    │                          │
│         │  • OTP চেয়েছে         │                          │
│         │  • অচেনা নম্বর        │                          │
│         │  [এটা scam] [ঠিক আছে]│                          │
│         └────────────────────────┘                          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
                          │
                          │ (optional, anonymized,
                          │  user-consented only)
                          ↓
            ┌─────────────────────────┐
            │   Surokkha Cloud        │
            │  ┌───────────────────┐  │
            │  │ Sender reputation │  │
            │  │ database          │  │
            │  │ (crowdsourced)    │  │
            │  └───────────────────┘  │
            │  ┌───────────────────┐  │
            │  │ URL safety check  │  │
            │  │ (links only,      │  │
            │  │  no message text) │  │
            │  └───────────────────┘  │
            │  ┌───────────────────┐  │
            │  │ Model update OTA  │  │
            │  │ (weights only)    │  │
            │  └───────────────────┘  │
            └─────────────────────────┘
```

---

## Android Layer: Message Capture

### Primary: NotificationListenerService

```kotlin
// AndroidManifest.xml
<service
    android:name=".SurokkhaNotificationListener"
    android:permission="android.permission.BIND_NOTIFICATION_LISTENER_SERVICE"
    android:exported="true">
    <intent-filter>
        <action android:name=
            "android.service.notification.NotificationListenerService"/>
    </intent-filter>
</service>
```

**কী পাওয়া যায়:**
- `extras.getString(Notification.EXTRA_TITLE)` → sender name
- `extras.getString(Notification.EXTRA_TEXT)` → message text
- `extras.getParcelableArrayList(Notification.EXTRA_MESSAGES)` → MessagingStyle full thread
- `sbn.packageName` → কোন app (com.whatsapp, com.facebook.orca, etc.)

**Limitations (জানতে হবে):**
- User notification preview off থাকলে message body আসে না
- WhatsApp-এ multiple unread হলে collapse হয়: "5 messages"
- iOS-এ এই API নেই → iOS version আলাদা approach লাগবে

### Fallback: AccessibilityService
শুধু তখনই ব্যবহার করব যখন user actively chat open করে পড়ছে — background-এ না।

---

## Detection Layer 1: Hard Rules Engine

```python
# Rule examples (Python pseudocode — actual implementation Kotlin/Java on device)

BKASH_OTP_PATTERNS = [
    r"(OTP|পিন|PIN|security code).{0,30}(দিন|share|send|পাঠান)",
    r"(bKash|বিকাশ|Nagad|নগদ).{0,50}(verify|confirm|নিশ্চিত)",
]

LOTTERY_PATTERNS = [
    r"(জিতেছেন|winner|prize|পুরস্কার).{0,30}(টাকা|BDT|Tk|account|number)",
    r"(account number|একাউন্ট নম্বর).{0,20}(দিন|পাঠান|send)",
]

FAKE_TRANSACTION_RULE = {
    # bKash official only sends from shortcode 16247
    # Any "transaction" SMS from a regular number = suspicious
    "trigger": "transaction SMS from non-shortcode sender",
    "confidence": 0.95
}

MARRIAGE_SCAM_PATTERNS = [
    r"(বিয়ে|marry|marriage).{0,100}(বিদেশ|abroad|UK|USA|Canada|Dubai)",
    r"(ভালোবাসি|love you|পছন্দ).{0,50}(প্রথম.{0,10}দেখা|first.{0,10}time)",
]
```

**Rule hit → confidence = 0.85–0.98, skip AI layer (save battery)**

---

## Detection Layer 2: AI Classifier

### Model Selection Strategy

```
Input message
     ↓
Script Detection (fast regex)
     ├─ Bengali Unicode (U+0980–U+09FF) majority → Route to BanglaBERT-tiny
     └─ Latin chars majority → Route to MuRIL-quantized (handles Banglish)
```

### Model 1: BanglaBERT-tiny (Bangla script)

| Property | Value |
|---|---|
| Base model | csebuetnlp/banglabert (110M) → distilled to ~22M |
| Quantization | INT8 (TFLite) |
| Size on device | ~45 MB |
| Inference time | ~120ms (mid-range Android) |
| Training data | BD-SHS + Bangla smishing dataset + custom scam corpus |
| Labels | `financial_scam`, `romance_scam`, `grooming`, `safe` |
| Expected accuracy | ~88–91% (Bangla script) |

### Model 2: MuRIL-quantized (Banglish + mixed)

| Property | Value |
|---|---|
| Base model | google/muril-base-cased (236M) → quantized |
| Size on device | ~70 MB |
| Inference time | ~180ms |
| Training data | BanTH (37k) + TB-OLID + OffMix-3L + custom |
| Expected accuracy | ~80–85% (Banglish — inherently harder) |

### Training Data Sources

```
Financial Scam:
├── Bangla smishing dataset (IEEE DataPort, 2,287 samples)
├── Bangalabarta dataset (2,772 samples, Mendeley)
├── bKash scam pattern corpus (custom-collected)
└── UCI SMS Spam Collection (English baseline)

Romance/Grooming:
├── PAN-2012 dataset (English, ~66k conversations)
├── scamdiggerprofiles (GitHub, dating fraud profiles)
├── FTC scam scripts (7 canonical patterns, labeled)
└── Custom BD matrimonial scam corpus (to be collected)

Bangla Hate/Abuse (for grooming context):
├── BD-SHS (50,200+ samples, GitHub naurosromim)
├── Bengali Hate Speech Dataset (rezacsedu, 6,400+ samples)
└── BanTH (37,300 transliterated samples)
```

---

## Detection Layer 3: Conversation Context

```
Conversation Buffer (in-memory, never persisted to disk):
├── Last 20 messages per conversation
├── Timestamps
└── Per-message confidence scores

Temporal Scoring:
├── Love-bombing detector:
│   "প্রিয়" / "ভালোবাসি" / "marry" in first 3 messages → +0.3
├── Off-platform push:
│   "WhatsApp-এ আসো" / "Telegram-এ বলো" → +0.25
├── Money request escalation:
│   Any money mention after trust-building pattern → +0.4
└── Urgency injection:
    "এখনই" / "আজকেই" / "urgent" + money/OTP → +0.35

Sender Reputation (local SQLite):
├── User manually reported: confidence_boost = +0.5
├── Community reported (from server): confidence_boost = +0.3
└── Known legitimate (saved contact): confidence_penalty = -0.4
```

---

## Alert System

### 3-tier Alert Logic

```kotlin
fun decideAlert(confidence: Float, scamType: ScamType): Alert {
    return when {
        confidence < 0.50 -> Alert.NONE
        confidence < 0.75 -> Alert.SOFT_WARN(
            message = "এই message-টা একটু দেখুন",
            explanation = buildExplanation(scamType),
            actions = [DISMISS, REPORT_SCAM]
        )
        else -> Alert.STRONG_ALERT(
            message = "⚠️ এটা ${scamType.banglaLabel} হতে পারে!",
            explanation = buildExplanation(scamType),
            actions = [DISMISS, REPORT_SCAM, LEARN_MORE]
        )
    }
}
```

### Explanation Builder (XAI)

```kotlin
fun buildExplanation(scamType: ScamType): String {
    // Show TOP 2 reasons only — don't overwhelm user
    return when (scamType) {
        FINANCIAL_SCAM -> "কারণ: OTP/PIN চাওয়া হয়েছে + অচেনা নম্বর"
        ROMANCE_SCAM   -> "কারণ: খুব দ্রুত বিয়ে/ভালোবাসার কথা + টাকার উল্লেখ"
        GROOMING       -> "কারণ: অপরিচিত ব্যক্তি ক্রমাগত personal কথা জানতে চাইছে"
        else           -> ""
    }
}
```

---

## Privacy Architecture

```
What stays ON DEVICE (always):
✅ Full message text
✅ Conversation history buffer
✅ AI model inference
✅ User's report history
✅ Sender reputation (local)

What goes to SERVER (only with user consent):
📤 Anonymized scam report: {scam_type, message_hash, sender_hash}
   → NO actual message text, NO phone numbers in plaintext
📤 URL only (for malicious link check) — no message context
📤 Model weight updates (download only, not upload)

What NEVER goes to server:
❌ Message content
❌ Phone numbers / contacts
❌ Conversation history
❌ User identity
```

---

## Tech Stack

```
Android App:
├── Language: Kotlin
├── Min SDK: API 26 (Android 8.0) — covers ~90% BD market
├── ML runtime: TensorFlow Lite 2.x
├── Local DB: Room (SQLite) — sender reputation, settings
├── Notification: NotificationListenerService
└── UI: Jetpack Compose

ML Pipeline (training, cloud):
├── Training: Python, HuggingFace Transformers
├── Fine-tuning: BanglaBERT + MuRIL on scam datasets
├── Conversion: TFLite converter + INT8 post-training quantization
└── Hosting: Hugging Face model hub (weights) + custom CDN (OTA update)

Backend (minimal):
├── Language: Python (FastAPI)
├── DB: PostgreSQL (sender reputation, anonymized reports)
├── URL safety: Google Safe Browsing API integration
└── Hosting: AWS/GCP lightweight instance
```

---

## Performance Targets

| Metric | Target | Notes |
|---|---|---|
| Notification → alert latency | < 500ms | Including AI inference |
| Battery impact | < 2% daily | Benchmark on mid-range device |
| App size | < 80 MB | Including both ML models |
| RAM footprint | < 50 MB | Background service |
| Accuracy (Bangla) | ~88–91% | Fine-tuned BanglaBERT |
| Accuracy (Banglish) | ~80–85% | MuRIL, harder problem |
| False positive rate | < 10% | Tune for precision |
| Offline functionality | Full | No network needed for detection |

---

## Security Considerations

- **Model obfuscation**: Scammers could theoretically probe the model to craft bypass messages — mitigate with regular model updates + rule layer that's harder to reverse-engineer
- **Play Store compliance**: NotificationListener requires explicit user grant in Settings; must show persistent notification icon; must NOT transmit message content
- **Data breach surface**: Server stores only hashes, not plaintext — even if breached, no message content exposed
- **Abuse prevention**: Community report system needs anti-brigading (Truecaller pattern: multiple independent reporters needed to flag)

---

## Phase 2 Additions (Post-MVP)

- **Image/video analysis**: Deepfake profile photo detection via reverse-image hash check
- **Voice call protection**: Real-time call analysis (harder — requires microphone + speaker separation)
- **iOS version**: Different approach needed — Screen Time API + Share Extension
- **Federated learning**: Improve model from user corrections without exposing data
