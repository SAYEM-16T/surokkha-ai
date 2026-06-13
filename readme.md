পুরো রিসার্চ হাতে পেয়েছি। নিচে তোমার Surokkha AI আইডিয়ার জন্য একটা পরিষ্কার, গোছানো ছবি দিলাম — **(ক)** তোমার আইডিয়াটা আসলে কী, **(খ)** সবচেয়ে জরুরি একটা ঝুঁকি যেটা তোমাকে আগেই জানতে হবে, **(গ)** এই নিয়ে দুনিয়াতে আগে কী কী কাজ হয়েছে, **(ঘ)** বাজারে কী কী আছে, **(ঙ)** Android-এ technically কতটা সম্ভব, আর **(চ)** তোমার আসল সুযোগটা কোথায়।

---

# Surokkha AI — Context & Landscape

## ১. তোমার আইডিয়াটা পরিষ্কার করে বললে

তুমি আসলে **একটা না, দুইটা আলাদা জিনিস** একসাথে চাইছ — দুটোই valid, কিন্তু আলাদা করে চেনা দরকার:

| | কী ধরা পড়বে | উদাহরণ |
|---|---|---|
| **A. Scam / প্রতারণা detection** | টাকা/তথ্য হাতানোর চেষ্টা | লটারি জিতেছেন, account number দিন, bKash OTP দিন, "বিয়ে করব বিদেশ নেব" রোমান্স স্ক্যাম |
| **B. Grooming / abuse detection** | সম্পর্কের ছদ্মবেশে manipulation | ধীরে ধীরে বিশ্বাস তৈরি করে যৌন/আর্থিকভাবে fাঁদে ফেলা |

তোমার "Truecaller for chats" framing-টা সঠিক — Truecaller যেমন **crowdsource (মানুষের report) + ML** মিলিয়ে spam caller ধরে, ঠিক সেই hybrid model-ই chat-এ apply করা যায়। এটা academically প্রমাণিত। কিন্তু একটা কথা আগে বলে নিই, কারণ এটা তোমার পুরো ventureটার দিক ঠিক করে দেবে 👇

---

## ২. ⚠️ সবচেয়ে জরুরি কথা: কে কার chat পড়ছে — এটাই তোমার venture-এর ভাগ্য

তোমার বর্ণনায় একটা জায়গা আছে — *"অন্য কেউ মেয়েটার chat দেখে সতর্ক করবে"*। **এই version-টা বানালে app টা বাঁচবে না** — technically, legally, আর ethically তিন দিক থেকেই মরবে। কারণ:

- কেউ **অন্য একজন adult-এর private chat চুপিচুপি পড়লে** সেটা globally **stalkerware**-এর সংজ্ঞায় পড়ে — EFF + Kaspersky-র Coalition Against Stalkerware এটাকে ঠিক এভাবেই define করেছে ([stopstalkerware.org](https://stopstalkerware.org/information-for-tech-companies/))।
- **Google Play সরাসরি ban করে** — এমনকি স্বামী/স্ত্রী/সঙ্গীকে track করা *তাদের অনুমতি থাকলেও* নিষিদ্ধ ([Play policy](https://support.google.com/googleplay/android-developer/answer/10065487))।
- **"মেয়েকে রক্ষা করব" — এই framing-টাই** stalkerware বিক্রেতারা ব্যবহার করে। গবেষণা বলছে coercive control-এর ৯৫% ভুক্তভোগী নারী। অর্থাৎ "নারীকে রক্ষার" নামে app টা সহজেই **নারীকে নিয়ন্ত্রণের হাতিয়ার** হয়ে যায় — উল্টো ফল।
- বাংলাদেশেও **Cyber Security Act 2023 / Cyber Security Ordinance 2025** অনুযায়ী অন্যের message চুপিচুপি access করা unauthorized access/hacking (১৪ বছর পর্যন্ত শাস্তি)।

**সমাধান — শুধু একটা design সিদ্ধান্ত পুরো জিনিসটা বদলে দেয়:**

> ❌ "চেলের chat পড়ে মেয়ের অভিভাবককে সতর্ক করি" → stalkerware
> ✅ **"মেয়ে নিজে নিজের ফোনে app রাখবে, নিজের inbox-এর scam/grooming নিজেই ধরবে, নিজেই সতর্ক হবে"** → একটা empowerment tool

এই reframe-টা করলে — user নিজের ফোনে, নিজের consent-এ, visible app (লুকানো না), on-device processing — তখন এটা legal, ethical, আর Play-store-friendly। **AIVA pitch-এ এটাই হবে তোমার সবচেয়ে শক্তিশালী differentiator: "We protect the woman herself — we don't surveil her."**

---

## ৩. আগে কী কী কাজ হয়েছে (Prior Work)

ভালো খবর: এর প্রতিটা টুকরো নিয়ে solid academic কাজ আছে — তোমাকে শূন্য থেকে শুরু করতে হবে না।

### ক) Grooming / predator detection
- **মূল benchmark: PAN-2012** shared task — predator-ধরা F0.5 ≈ **0.93** ([task page](https://pan.webis.de/clef12/pan12-web/sexual-predator-identification.html))।
- আধুনিক transformer/LLM (RoBERTa, LLaMA) **F1 ≈ 0.96–0.99** দাবি করে — **কিন্তু সাবধান**: এই সংখ্যাগুলো একটা "clean", English, imbalanced dataset-এ; বাস্তবে এত হবে না।
- আসল না-সমাধান হওয়া সমস্যা: **early detection** — পুরো chat শেষে নয়, grooming শুরুর দিকেই ধরা ([ACL 2021 eSPD](https://aclanthology.org/2021.acl-long.386/))। আর grooming-এর শুরুটা যৌন কথা না, বরং **"বন্ধুত্ব/বিশ্বাস তৈরির" পর্যায়** — তাই শুধু খারাপ শব্দ খুঁজলে কাজ হবে না।

### খ) Romance / বিয়ের প্রলোভন স্ক্যাম
- মূল system: **Suarez-Tangil et al. (IEEE 2019)** — profile-এর ছবি+লেখা+demographic মিলিয়ে **~97%** ([arXiv](https://arxiv.org/abs/1905.12593))।
- Chat-level-এ **BiLSTM/BiGRU, BERT-এর চেয়ে ভালো করে (F1 ≈ 0.99)** কারণ এগুলো grooming-এর সময়ের ধারা ধরতে পারে ([Hossain et al. 2025](https://arxiv.org/pdf/2509.05362))।
- **FTC** স্ক্যামারদের top মিথ্যাগুলো গুনে দিয়েছে (অসুস্থ আত্মীয়, "invest শেখাব", military-তে আছি, বিয়ের কথা) — এগুলো সরাসরি তোমার label/feature হিসেবে ব্যবহারযোগ্য।
- **বাংলাদেশ-context আছে**: CID ভুয়া Facebook প্রোফাইল ও **"BCCB Matrimonial" ভুয়া বিয়ে স্ক্যাম** (Tk ১.৫ কোটি) ধরেছে। "আগে Bitcoin কেনো, তারপর বিয়ে" crypto pig-butchering ভারত/বাংলাদেশের matrimonial সাইটে ছড়াচ্ছে।
- ⚠️ বড় সতর্কতা: **stock LLM safety filter romance-baiting-এর 0.0% ধরেছে** — তাই ChatGPT/Claude-কে শুধু জিজ্ঞেস করলেই হবে না, **dedicated classifier** লাগবে।

### গ) Financial / লটারি / OTP স্ক্যাম
- এটা সবচেয়ে পরিণত ক্ষেত্র। Bangla smishing-এ **BERT + char-CNN ~98.5%** ([arXiv 2502.01518](https://arxiv.org/html/2502.01518))।
- **bKash/Nagad স্ক্যামের নির্দিষ্ট pattern documented** — OTP চাওয়া (~৭০% কেস), ভুয়া transaction SMS (আসলটা শুধু **16247** থেকে আসে), "ভুল করে টাকা পাঠিয়েছি ফেরত দিন", "৩০ মিনিটে account block"। **মূল নিয়ম: bKash কখনো PIN/OTP চায় না** — এটা একটা নিশ্চিত rule, model ছাড়াও কাজ করে।

### ঘ) Bangla + Banglish NLP (তোমার আসল চ্যালেঞ্জ)
- **খাঁটি বাংলা** ভালো অবস্থায়: BanglaBERT + **BD-SHS dataset (৫০,০০০+ comment)** → ~৮৮–৯১% সম্ভব।
- **Banglish (রোমান হরফে বাংলা)** কঠিন — accuracy **৮০-র ঘরে নেমে আসে**, কারণ বানান standardize না। এর জন্য আলাদা model দরকার: **BanglishBERT, MuRIL, XLM-RoBERTa** + datasets **BanTH (৩৭k), TB-OLID**।
- 👉 মানে: তোমার একটাই model দিয়ে হবে না — **বাংলা script আর Banglish-এর জন্য routing/আলাদা model** লাগবে।

---

## ৪. বাজারে কী আছে (Competitors)

| Product | কী করে | তোমার জন্য মানে |
|---|---|---|
| **Bark** (US, 7.5M kids) | Text + 30+ app-এ AI দিয়ে predator/bullying/sextortion ধরে, parent-কে শুধু flagged অংশ দেখায় | **সবচেয়ে কাছের analog** — কিন্তু এটা **parent → child surveillance**, adult নিজেকে রক্ষা করার tool না |
| **Truecaller** | crowdsource report + ML, spam caller | তোমার hybrid model-এর blueprint |
| **Google Gemini Nano** | **on-device, real-time** call/message scam detection | প্রমাণ যে on-device সম্ভব — কিন্তু শুধু নতুন Pixel/Samsung, ইংরেজি, নারী/বাংলা-কেন্দ্রিক না |
| **Norton Genie / McAfee** | scam detector, বেশিরভাগ **copy-paste করে** দিতে হয় | always-on না — এটাই তোমার ফাঁক |
| **Canopy** | real-time **ছবির** nudity ধরে (টেক্সট না) | conversational text scam ফাঁকা রেখে গেছে |
| **ScamMukt** (India) | প্রথম consumer scam app | general-purpose, নারী/আঞ্চলিক ভাষা-কেন্দ্রিক না |

**সারকথা: কোনো একটা product একসাথে এই তিনটা করে না** — তাই এটাই তোমার white space (নিচে §৬)।

---

## ৫. Android-এ technically কতটা সম্ভব

- ✅ **সম্ভব**, কিন্তু শুধু দুই পথে: **NotificationListenerService** (notification থেকে message পড়া — সবচেয়ে নিরাপদ) আর **AccessibilityService** (স্ক্রিনে যা দেখা যাচ্ছে)।
- **WhatsApp-এর E2E encryption কোনো বাধা না** — ফোনেই decrypt হয়ে notification-এ চলে আসে, সেটা পড়া যায়। (নেটওয়ার্কে আটকানো অসম্ভব, কিন্তু সেটা তোমার লাগবেও না।)
- ⚠️ **আসল বাধা code না, Google Play policy**: READ_SMS শুধু default SMS app পায়; AccessibilityService-এর অপব্যবহার ২০২৬-এ আরও কড়া হচ্ছে। তাই **নিজের notification পড়ার app + পরিষ্কার consent + on-device data** — এই lane-ই নিরাপদ।

---

## ৬. তোমার আসল সুযোগ (Where Surokkha AI can win)

তিনটা না-পূরণ-হওয়া চাহিদার **মাঝখানে** তোমার জায়গা — যা একসাথে কেউ করছে না:

1. **নিজেকে রক্ষা (self-protection), surveillance না** — Bark/Canopy অভিভাবকের নজরদারি; তুমি **adult নারীকে নিজের হাতিয়ার** দাও (romance/grooming victim প্রায়ই পরিবার থেকে লুকায় — তাই self-tool-ই কাজের)।
2. **বাংলা + Banglish** — model প্রমাণিত (~৯৮%), কিন্তু **কোনো shipped consumer product নেই**। এটা খাঁটি ফাঁকা বাজার।
3. **On-device + privacy-first** — "তোমার chat কখনো cloud-এ যায় না" — Gemini Nano প্রমাণ করেছে সম্ভব; ২০২৬-এ Aura-র ডেটা breach-এর পর এটা শক্তিশালী বিক্রির পয়েন্ট।

---

## ৭. বাস্তবতা: ৮০-৯০% accuracy আর false alarm

- ৮০-৯০% **বাস্তবসম্মত লক্ষ্য**, বিশেষত খাঁটি বাংলায়। কিন্তু benchmark-এর ৯৯% ভুলে যাও — ওটা clean English data-তে।
- **Accuracy-র চেয়ে precision-এ জোর দাও** — আসল বন্ধু/পরিবারকে "scam" বলাটাই সবচেয়ে বড় ক্ষতি (trust হারাবে)। তাই: high-confidence হলে warn, কম হলে শুধু "একটু সাবধান" — auto-block না।
- **মানুষের feedback loop বানাও** (Report / Not-spam) — Truecaller এভাবেই false positive কমায়।
- **explainability দাও** — "কেন flag করলাম: OTP চেয়েছে + অচেনা number" — তাতে user বিশ্বাস করে।

---

## ৮. AIVA-র জন্য পরবর্তী পদক্ষেপ (আমার সাজেশন)

1. **আইডিয়াটা reframe করো** → "self-protection tool for women against scams & manipulation" (stalkerware ফাঁদ এড়াও — investor/judge রাও এটা ধরবে)।
2. **একটা wedge দিয়ে শুরু করো**, সব একসাথে না → আমার পরামর্শ: **bKash/Nagad + romance/বিয়ে স্ক্যাম, বাংলা+Banglish-এ** (সবচেয়ে concrete, data আছে, ক্ষতিও বেশি)।
3. **Ready datasets ব্যবহার করো**: BD-SHS, BanTH, TB-OLID, Bangalabarta, scamdigger।
4. **Architecture**: NotificationListener → on-device ছোট transformer (BanglaBERT/MuRIL) + bKash-এর মতো hard rules + crowdsource report layer।

---

আমি চাইলে এখন এর যেকোনো একটা গভীরে নিয়ে যেতে পারি — যেমন **AIVA pitch deck-এর outline**, **technical architecture ডায়াগ্রাম**, নাকি **MVP-তে কোন scam type আগে ধরবে তার পরিকল্পনা**। কোনটা চাও?
