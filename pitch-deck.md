# Surokkha AI — AIVA Pitch Deck Outline

---

## Slide 1: Cover

**Surokkha AI**
*"আপনার নিজের সুরক্ষা, আপনার হাতে"*

> Your personal AI shield against scams, manipulation & fraud — in Bangla.

- Team: [নাম]
- Cohort: AIVA Inaugural Cohort
- Contact: [email]

---

## Slide 2: The Problem (Hook with real story)

**প্রতি বছর বাংলাদেশে হাজার হাজার নারী প্রতারিত হচ্ছেন — WhatsApp, Messenger, Facebook-এ।**

3 types of harm, 1 common thread:

| ঘটনা | ক্ষতি |
|---|---|
| "বিয়ে করব, বিদেশ নেব" — তারপর টাকা নিয়ে গায়েব | আর্থিক + মানসিক |
| "bKash-এ ভুল করে পাঠিয়েছি, OTP দিন" | আর্থিক (গড় Tk ৫,০০০–১৫,০০০) |
| মাসের পর মাস trust তৈরি করে ব্ল্যাকমেইল | যৌন + আর্থিক |

> **CID Bangladesh:** ভুয়া matrimonial page "BCCB Matrimonial" থেকে Tk ১.৫ কোটি প্রতারণা।
> **FTC 2024:** Romance scam-এ শুধু US-এ $1.3B লোকসান — ভুক্তভোগীর সংখ্যাগরিষ্ঠ নারী।

**এখন সমস্যাটা আরও বড় হচ্ছে:** AI দিয়ে তৈরি perfect Bangla message, ভুয়া ছবি, ভুয়া voice — মানুষ ধরতেই পারছে না।

---

## Slide 3: Insight — কেন এখনো সমাধান নেই?

**বিদ্যমান tools তিনটা কারণে কাজ করে না:**

1. **ভাষার সমস্যা** — Norton, McAfee, Bark সব English-first। Bangla + Banglish বোঝে না।
2. **Always-on না** — Norton Genie-তে নিজে copy-paste করতে হয়। Real-time protection নেই।
3. **Wrong model** — Bark, Canopy শিশুদের জন্য, অভিভাবক নজর রাখে। **Adult নারী নিজে নিজেকে রক্ষা করার tool নেই।**

> "Google Gemini Nano on-device scam detection করে — কিন্তু শুধু নতুন Pixel/Samsung, শুধু English।"

**Gap: Bangla-speaking adult women protecting THEMSELVES. কেউ এটা করছে না।**

---

## Slide 4: Solution — Surokkha AI

**আপনার ফোনে, আপনার ভাষায়, সম্পূর্ণ private — একটা AI shield।**

```
আপনার WhatsApp / Messenger / SMS
          ↓
    Surokkha AI (on-device)
          ↓
   ⚠️ "এই message-টা সন্দেহজনক"
   কারণ: OTP চেয়েছে + অচেনা নম্বর
          ↓
    আপনি সিদ্ধান্ত নেন
```

**মূল নীতি:**
- 🔒 আপনার chat কখনো server-এ যায় না — সব processing ফোনেই
- 👁️ App সবসময় দেখা যায় — কোনো লুকানো নেই
- ✋ শুধু warn করে — block বা share করে না
- 🗣️ বাংলা + Banglish দুটোই বোঝে

---

## Slide 5: How It Works (Simple)

**3-layer detection:**

**Layer 1 — Hard Rules (instant, 100% reliable)**
- bKash/Nagad থেকে OTP চাওয়া = scam
- Official shortcode (16247) ছাড়া transaction SMS = সন্দেহজনক
- "লটারি জিতেছেন + account number দিন" pattern = scam

**Layer 2 — AI Model (Bangla + Banglish)**
- BanglaBERT / MuRIL fine-tuned on Bangladesh-specific scam data
- Romance scam linguistic markers (দ্রুত বিয়ের কথা, "বিদেশে নেব", "invest শেখাব")
- Grooming pattern detection (সময়ের সাথে trust তৈরির ধারা)

**Layer 3 — Community Intelligence**
- User-রা "এটা scam" report করলে সেই sender-এর reputation score কমে
- Truecaller model — অনেক মানুষ report করলে auto-flag

---

## Slide 6: Market Size

**Bangladesh + South Asian diaspora:**

| Segment | Size |
|---|---|
| Bangladesh mobile internet users | ১২+ কোটি |
| bKash/Nagad active users | ১৪.৪ কোটি+ |
| Facebook users (Bangladesh) | ৪.৫+ কোটি |
| South Asian diaspora (US/UK/Canada) | ৫০+ লাখ |

**Problem scale:**
- BFIU: ২০২৪ সালে ৪৮,৫৮৬ MFS account suspend
- Romance scam: globally $1.3B/year, victim-দের ৬০%+ নারী
- "Buy Bitcoin first, then we marry" scam এখন BD matrimonial sites-এ

**Target beachhead:** ১৮–৪০ বছর বয়সী Bangladeshi নারী, smartphone user, social media active → **~২ কোটি potential users (Bangladesh alone)**

---

## Slide 7: Traction / Validation

*(এখানে তোমার নিজের data বসাবে — কিছু suggestion:)*

- [ ] Beta user interviews (কতজন?)
- [ ] Survey: "X% নারী বলেছেন তারা সন্দেহজনক message পেয়েছেন কিন্তু নিশ্চিত হতে পারেননি"
- [ ] Academic validation: BD-SHS dataset-এ BanglaBERT ~91% F1
- [ ] Bangla smishing model: ~98.5% accuracy (arXiv 2502.01518)
- [ ] AIVA selection as proof of concept validation

---

## Slide 8: Business Model

**Freemium:**

| Free | Pro (Tk ৯৯/মাস) |
|---|---|
| SMS + WhatsApp notification scan | সব app (Messenger, imo, dating apps) |
| Basic scam rules | Advanced grooming pattern detection |
| Community reports দেখা | Community reports দেওয়া + priority alerts |
| — | Family alert (নিজে চাইলে trusted contact-কে জানাতে পারবে) |

**B2B (Phase 2):**
- NGO / women's safety organizations — bulk license
- Mobile operator partnership (Grameenphone, Robi) — bundled
- Bank/MFS (bKash, Nagad) — API integration for their users

---

## Slide 9: Competition & Differentiation

| | Surokkha AI | Bark | Truecaller | Norton Genie | Google Gemini |
|---|---|---|---|---|---|
| Bangla/Banglish | ✅ | ❌ | ❌ | ❌ | ❌ |
| Adult self-protection | ✅ | ❌ (children) | ⚡ (calls only) | ⚡ (manual) | ⚡ (limited) |
| Always-on chat monitoring | ✅ | ✅ | ❌ | ❌ | ✅ |
| On-device / private | ✅ | ❌ | ❌ | ❌ | ✅ |
| Romance/marriage scam | ✅ | ❌ | ❌ | ⚡ | ❌ |
| bKash/Nagad patterns | ✅ | ❌ | ❌ | ❌ | ❌ |

**Moat:** Bangla + Banglish training data, BD-specific scam patterns, community report network — এগুলো build করতে সময় লাগে। বড় vendor-রা করবে না কারণ market size তাদের কাছে ছোট।

---

## Slide 10: Team

*(তোমার নিজের info বসাবে)*

- **[তোমার নাম]** — Founder & CEO | [background]
- **[Co-founder]** — CTO | Android + NLP experience
- **Advisors:** [কেউ থাকলে]

**Why us:**
- বাংলাদেশের context সবচেয়ে ভালো বুঝি
- AIVA cohort: AI venture building expertise
- Mission-driven: এই সমস্যা আমাদের community-র

---

## Slide 11: Ask

**আমরা চাইছি:**

- **Funding:** $[X] — [কীসের জন্য: model training, Android dev, user acquisition]
- **Mentorship:** NLP/AI product, women's safety NGO network, BD regulatory
- **Partnerships:** NGO distribution, mobile operator, MFS integration intro

**12-month milestone:**
- [ ] MVP launch (bKash/romance scam detection)
- [ ] ১০,০০০ active users
- [ ] First B2B pilot (NGO or MFS partner)
- [ ] Bangla scam dataset publicly released (academic credibility)

---

## Slide 12: Vision

> **"Truecaller spam calls থেকে রক্ষা করে — Surokkha AI করবে dangerous conversations থেকে।"**

বাংলাদেশ থেকে শুরু।
তারপর South Asian diaspora।
তারপর যেখানেই low-resource language + vulnerable women + digital fraud।

**একটা মেয়েও যদি একটা scam থেকে বেঁচে যায় — সেটাই শুরু।**

---

*[Appendix: Technical architecture, dataset sources, academic citations — আলাদা ফাইলে]*
