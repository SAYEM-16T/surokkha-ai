# Surokkha AI — Build Session Transcript

> এই ফাইলটা পুরো working session-এর transcript — সিদ্ধান্ত, প্রশ্নোত্তর, আর যেসব ফাইল/কোড তৈরি হয়েছে — সব markdown format-এ গুছিয়ে রাখা।

---

## 🤖 Assistant — সিদ্ধান্ত ও কৌশল

তোমার সব context, architecture, MVP plan, pitch deck পড়লাম। এখন সবচেয়ে গুরুত্বপূর্ণ কথাটা সোজাসুজি বলি, তারপর সিদ্ধান্ত আর প্রশ্ন।

### আসল বাস্তবতা (এটা না বুঝলে কালকে submit মিস হবে)

তোমার MVP plan ১২ সপ্তাহের। পুরো native Android app — NotificationListenerService + on-device BanglaBERT/MuRIL TFLite model train করে, phone-এ চালিয়ে, Play Store-এ দিয়ে — **একদিনে অসম্ভব**। কেউ পারবে না। ওটার পেছনে দৌড়ালে কালকে খালি হাতে যাবে।

কিন্তু ভালো খবর: **AIVA-র ৩টা requirement একটা working web app দিয়েই পুরোপুরি পূরণ হয়।** ওদের নিজের কথা —

> "A webpage or app store link... show the service can function using a real implementation or test cases."

মানে webpage চলবে, এবং real detection দেখাতে হবে — placeholder না।

### আমার সিদ্ধান্ত (তুমি delegate করেছ, তাই নিচ্ছি)

কালকের মধ্যে যা বাস্তবে চালানো যাবে এবং judge-দের impress করবে:

| # | Deliverable | কী বানাবো | কীভাবে |
|---|---|---|---|
| 1 | **Live product link** | একটা real working web app — message/chat paste করলে Bangla+Banglish scam/manipulation সত্যিই detect করে, কারণসহ verdict দেখায় | Next.js + hybrid engine (hard rules + AI API) → Vercel-এ deploy (free https link) |
| 2 | **Demo video** | ওই live app-এ ৩টা real test case চালিয়ে screen-record (romance scam, bKash OTP scam, safe message), ৩ মিনিট, English subtitle | তুমি record করবে, আমি script + subtitle file দেবো |
| 3 | **Pitch deck** | তোমার `pitch-deck.md` → আসল slides (PDF) + detailed speaker notes (judge রা notes পড়ে নম্বর দেয়) | আমি বানিয়ে দেবো |

Native Android app/on-device model = pitch-এ **"production roadmap / Phase 2"** হিসেবে দেখাবে। Live demo cloud-AI দিয়ে — এটা সম্পূর্ণ legitimate startup path, judge রা এটাই expect করে।

**Model/effort:** তুমি এখন Opus 4.8 [1M] select করেছ — এটাই সর্বোচ্চ। এটাই রাখো, পুরো build আমি এখানেই এই session-এ করব। তোমাকে আলাদা prompt চালাতে হবে না — আমি সরাসরি তোমার laptop-এ code লিখব, আর শুধু যেখানে তোমার account/key/recording লাগবে সেখানে exact কমান্ড/prompt দেবো।

---

## ❓ AskUserQuestion — ৩টা সিদ্ধান্ত (তোমার account/key লাগবে)

| প্রশ্ন | তোমার উত্তর |
|---|---|
| Live demo-তে scam detect করার AI "brain" — ১ ঘণ্টায় কোন key জোগাড় করতে পারবে? | **Google Gemini (free key)** |
| Web app ছাড়াও কি Android APK লাগবে কালকের submission-এ? | **শুধু Web app (recommended)** |
| Live product link কোথায় host হবে? | **Vercel (free, recommended)** |

> চমৎকার — সিদ্ধান্ত পরিষ্কার: **Next.js web app + Gemini (free) + Vercel, web only**।

---

## 🖥️ Environment Check

প্রথমে laptop-এর environment চেক করলাম (কী installed আছে):

```bash
node -v   # v20.19.5
npm -v    # 10.8.2
git --version  # git version 2.43.0
gh --version   # gh version 2.83.1 (2025-11-13)
pwd       # /home/technonext/AA__ROOT/surokkha-ai
```

✅ সব tools আছে (Node 20, npm, git, gh CLI)।

---

## ✅ Build Checklist (Todo)

1. Scaffold Next.js project (configs, package.json)
2. Build hard-rules detection engine (`lib/rules.ts`)
3. Build Gemini classifier (`lib/gemini.ts`)
4. Build `/api/analyze` API route combining rules + Gemini
5. Build main UI (`page.tsx`, layout, CSS) with sample test cases
6. `npm install` + run dev + verify detection works locally
7. Write README, `.env.example`, `.gitignore`
8. Generate pitch deck (slides + speaker notes) as PDF-ready
9. Write demo video script + English subtitles (SRT)
10. Give user exact deploy (Vercel) + Gemini key + YouTube steps

---

## 📄 যেসব ফাইল তৈরি হয়েছে

### `package.json`

```json
{
  "name": "surokkha-ai",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "next": "14.2.5",
    "react": "^18.3.1",
    "react-dom": "^18.3.1"
  },
  "devDependencies": {
    "@types/node": "^20",
    "@types/react": "^18",
    "@types/react-dom": "^18",
    "typescript": "^5"
  }
}
```

### `tsconfig.json`

```json
{
  "compilerOptions": {
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [{ "name": "next" }],
    "paths": {
      "@/*": ["./*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

### `next.config.js`

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
};

module.exports = nextConfig;
```

### `.gitignore`

```gitignore
# dependencies
/node_modules
/.pnp
.pnp.js

# next.js
/.next/
/out/

# production
/build

# misc
.DS_Store
*.pem

# debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# env files (NEVER commit your real keys)
.env
.env.local
.env*.local

# typescript
*.tsbuildinfo
next-env.d.ts
```

### `.env.example`

```bash
# Copy this file to .env.local and fill in your real key.
# Get a FREE Gemini API key at: https://aistudio.google.com/app/apikey

GEMINI_API_KEY=your_gemini_api_key_here

# Optional: override the model (default is gemini-2.0-flash)
GEMINI_MODEL=gemini-2.0-flash
```

### `lib/rules.ts` — Hard Rules Engine

```typescript
// Surokkha AI — Hard Rules Engine
// Layer 1 detection: fast, deterministic, regex-based.
// Works fully offline. Catches the highest-confidence structural scam patterns
// in Bangla, Banglish (romanized Bangla) and English.

export type ScamType =
  | "financial_scam"
  | "romance_scam"
  | "grooming"
  | "safe";

export interface RuleHit {
  id: string;
  scamType: ScamType;
  weight: number; // contribution to confidence (0..1)
  labelBn: string; // shown to user (Bangla)
  labelEn: string; // English (for judges / subtitles)
}

interface RuleDef {
  id: string;
  scamType: ScamType;
  weight: number;
  labelBn: string;
  labelEn: string;
  patterns: RegExp[];
}

// NOTE: patterns are intentionally broad/structural. The AI layer (Gemini)
// handles nuance; these rules guarantee the obvious scams are never missed.
const RULES: RuleDef[] = [
  // ---------------- FINANCIAL SCAM ----------------
  {
    id: "otp_pin_request",
    scamType: "financial_scam",
    weight: 0.9,
    labelBn: "OTP/PIN/গোপন কোড চাওয়া হয়েছে",
    labelEn: "Asks for your OTP / PIN / secret code",
    patterns: [
      /\b(otp|o\.t\.p)\b/i,
      /\b(pin|gopon\s*code|gopon\s*number|secret\s*code)\b/i,
      /(পিন|ও\s*টি\s*পি|ওটিপি|গোপন\s*(কোড|নম্বর|নাম্বার|পিন))/,
      /(verification\s*code|verify\s*code)/i,
    ],
  },
  {
    id: "share_code_action",
    scamType: "financial_scam",
    weight: 0.55,
    labelBn: "কোড/তথ্য পাঠাতে বলা হয়েছে",
    labelEn: "Pressures you to send a code or detail",
    patterns: [
      /(code|kod|number|নম্বর|নাম্বার|কোড)[^\n]{0,30}(send|pathao|pathan|den|dao|দিন|দাও|পাঠান|পাঠাও|share|forward)/i,
      /(send|share|forward|pathao|pathan)[^\n]{0,20}(otp|pin|code|kod|কোড|পিন)/i,
    ],
  },
  {
    id: "bkash_nagad_mention",
    scamType: "financial_scam",
    weight: 0.45,
    labelBn: "bKash/Nagad-এর নামে যোগাযোগ",
    labelEn: "Claims to be from bKash / Nagad / Rocket",
    patterns: [
      /\b(bkash|bikash|nagad|nogod|rocket|upay)\b/i,
      /(বিকাশ|নগদ|রকেট|উপায়)/,
    ],
  },
  {
    id: "account_blocked_threat",
    scamType: "financial_scam",
    weight: 0.6,
    labelBn: "অ্যাকাউন্ট বন্ধ/ব্লক হওয়ার ভয় দেখানো",
    labelEn: "Threatens that your account will be blocked/closed",
    patterns: [
      /(account|একাউন্ট|অ্যাকাউন্ট|akkount)[^\n]{0,30}(block|blocked|bondho|close|closed|suspend|বন্ধ|ব্লক|বাতিল)/i,
      /(block|bondho|বন্ধ|ব্লক)[^\n]{0,25}(account|একাউন্ট|অ্যাকাউন্ট)/i,
    ],
  },
  {
    id: "lottery_prize",
    scamType: "financial_scam",
    weight: 0.7,
    labelBn: "লটারি/পুরস্কার জেতার দাবি",
    labelEn: "Claims you won a lottery / prize / lakh taka",
    patterns: [
      /(jiteche|jitechen|jiteichen|winner|lottery|lottary|prize|puroskar|জিতেছেন|জিতেছ|বিজয়ী|লটারি|পুরস্কার|পুরষ্কার)/i,
      /(lakh|lac|লাখ|কোটি|coti|crore)[^\n]{0,25}(taka|tk|টাকা|৳)/i,
      /(congratulations|congrats|অভিনন্দন)[^\n]{0,40}(won|winner|select|নির্বাচিত|জিতেছেন)/i,
    ],
  },
  {
    id: "send_money_to_receive",
    scamType: "financial_scam",
    weight: 0.65,
    labelBn: "টাকা পেতে আগে টাকা/ফি পাঠাতে বলা",
    labelEn: "Asks you to pay a fee first to receive money/prize",
    patterns: [
      /(fee|charge|chorch|registration|processing)[^\n]{0,30}(send|pathao|pay|dao|din|দিন|পাঠান|পরিশোধ)/i,
      /(taka|tk|টাকা|৳)[^\n]{0,40}(pathale|dile|pathan|pay korle)[^\n]{0,40}(paben|return|ferot|ফেরত|পাবেন)/i,
    ],
  },
  {
    id: "wrong_send_refund",
    scamType: "financial_scam",
    weight: 0.6,
    labelBn: "“ভুলে টাকা পাঠিয়েছি, ফেরত দিন” কৌশল",
    labelEn: "“I sent money by mistake, please return it” trick",
    patterns: [
      /(vule|bhule|bhul|vul|ভুলে|ভুল\s*করে)[^\n]{0,40}(pathiye|pathiyechi|send|টাকা|taka)/i,
      /(ferot|ফেরত|return|payback)[^\n]{0,25}(din|dao|দিন|দাও|koren|করেন)/i,
    ],
  },

  // ---------------- ROMANCE / MARRIAGE SCAM ----------------
  {
    id: "fast_love",
    scamType: "romance_scam",
    weight: 0.45,
    labelBn: "খুব দ্রুত ভালোবাসা/বিয়ের প্রস্তাব",
    labelEn: "Very fast love / marriage proposal from a stranger",
    patterns: [
      /(love\s*you|valobashi|bhalobashi|ভালোবাসি|bhalobasi)/i,
      /(biye|bibaho|marry|marriage|বিয়ে|বিবাহ)[^\n]{0,40}(korbo|korte|chai|kori|করব|করতে|চাই)/i,
      /(jibon\s*songi|life\s*partner|জীবনসঙ্গী|জীবন\s*সঙ্গী)/i,
    ],
  },
  {
    id: "abroad_promise",
    scamType: "romance_scam",
    weight: 0.5,
    labelBn: "বিদেশে নেওয়ার প্রতিশ্রুতি",
    labelEn: "Promises to take you abroad (UK/USA/Canada/Dubai)",
    patterns: [
      /(bidesh|videsh|abroad|বিদেশ)[^\n]{0,30}(nebo|niye|jabo|settle|নেব|নিয়ে|যাব)/i,
      /\b(uk|usa|u\.s\.a|canada|dubai|london|malaysia|italy|england)\b/i,
      /(কানাডা|দুবাই|লন্ডন|মালয়েশিয়া|আমেরিকা|ইতালি|ইংল্যান্ড)/,
    ],
  },
  {
    id: "gift_parcel_customs",
    scamType: "romance_scam",
    weight: 0.7,
    labelBn: "গিফট/পার্সেল কাস্টমসে আটকে আছে — টাকা চায়",
    labelEn: "Gift/parcel 'stuck at customs' — asks for clearance money",
    patterns: [
      /(gift|parcel|courier|পার্সেল|গিফট|কুরিয়ার)[^\n]{0,40}(customs|kastom|airport|atke|আটকে|কাস্টমস|বিমানবন্দর)/i,
      /(customs|kastom|কাস্টমস)[^\n]{0,30}(fee|charge|taka|টাকা|clearance)/i,
    ],
  },
  {
    id: "investment_crypto_love",
    scamType: "romance_scam",
    weight: 0.55,
    labelBn: "প্রেম + ইনভেস্টমেন্ট/ক্রিপ্টো শেখানোর প্রস্তাব",
    labelEn: "Love + 'let me teach you investment / crypto' (pig-butchering)",
    patterns: [
      /(invest|investment|bitcoin|crypto|trading|forex|ইনভেস্ট|বিটকয়েন|ক্রিপ্টো|ট্রেডিং)/i,
      /(profit|labh|double|diguno|লাভ|দ্বিগুণ)[^\n]{0,30}(guaranteed|nischit|100|sure|নিশ্চিত)/i,
    ],
  },

  // ---------------- GROOMING / MANIPULATION ----------------
  {
    id: "off_platform_push",
    scamType: "grooming",
    weight: 0.5,
    labelBn: "অন্য (গোপন) অ্যাপে নিয়ে যাওয়ার চেষ্টা",
    labelEn: "Tries to move you to another (private) app",
    patterns: [
      /(telegram|whatsapp|imo|signal|hangout|snapchat)[^\n]{0,20}(aso|asho|chol|add|number|den|dao|আসো|চলো|নম্বর)/i,
      /(personal|alada|private|গোপন|আলাদা|পার্সোনাল)[^\n]{0,20}(app|chat|number|message|নম্বর|কথা)/i,
    ],
  },
  {
    id: "secrecy_request",
    scamType: "grooming",
    weight: 0.5,
    labelBn: "কাউকে না বলার জন্য চাপ দেওয়া",
    labelEn: "Pressures you to keep it secret from family/others",
    patterns: [
      /(karake|karke|kauke|keo|keu)[^\n]{0,20}(bolo na|bolba na|jano na|বোলো না|বলবা না|জানাবে না)/i,
      /(amader|eta|this)[^\n]{0,20}(secret|gopon|গোপন|secret\s*rakho)/i,
      /(parents|family|ma\s*baba|বাবা\s*মা|পরিবার)[^\n]{0,20}(janle|bolle|জানলে|বললে)[^\n]{0,20}(somossa|problem|সমস্যা|raag|রাগ)/i,
    ],
  },
  {
    id: "urgency_pressure",
    scamType: "financial_scam",
    weight: 0.4,
    labelBn: "তাড়াহুড়ো/এখনই করার চাপ",
    labelEn: "Creates urgency — 'do it now / today'",
    patterns: [
      /(akhoni|ekhuni|এখনই|এখুনি|jeldi|তাড়াতাড়ি|taratari|urgent|argent|joruri|জরুরি)/i,
      /(ajke\s*er\s*moddhe|aj\s*er\s*moddhe|আজকের\s*মধ্যে|within\s*today|10\s*minute|5\s*minute|১০\s*মিনিট)/i,
    ],
  },
  {
    id: "suspicious_link",
    scamType: "financial_scam",
    weight: 0.45,
    labelBn: "সন্দেহজনক লিংকে ক্লিক করতে বলা",
    labelEn: "Asks you to click a suspicious link",
    patterns: [
      /https?:\/\/(bit\.ly|tinyurl|cutt\.ly|t\.co|shorturl|rb\.gy|is\.gd)/i,
      /(click|klik|ক্লিক|press)[^\n]{0,20}(link|লিংক|link\s*e|here|ekhane|এখানে)/i,
      /(verify|update|confirm)[^\n]{0,20}(account|profile)[^\n]{0,20}(https?:\/\/|link|লিংক)/i,
    ],
  },
];

export interface RulesResult {
  hits: RuleHit[];
  // confidence purely from rules (0..1), combined non-linearly
  ruleConfidence: number;
  // dominant scam type from rules, or "safe" if none
  dominantType: ScamType;
}

export function runRules(text: string): RulesResult {
  const hits: RuleHit[] = [];

  for (const rule of RULES) {
    const matched = rule.patterns.some((p) => p.test(text));
    if (matched) {
      hits.push({
        id: rule.id,
        scamType: rule.scamType,
        weight: rule.weight,
        labelBn: rule.labelBn,
        labelEn: rule.labelEn,
      });
    }
  }

  if (hits.length === 0) {
    return { hits, ruleConfidence: 0, dominantType: "safe" };
  }

  // Combine weights with "noisy-OR" so multiple weak signals add up
  // but never exceed 1.0.
  let surv = 1;
  for (const h of hits) surv *= 1 - h.weight;
  const ruleConfidence = 1 - surv;

  // Dominant type = type with the highest summed weight.
  const byType: Record<string, number> = {};
  for (const h of hits) {
    byType[h.scamType] = (byType[h.scamType] || 0) + h.weight;
  }
  let dominantType: ScamType = "safe";
  let best = 0;
  for (const [t, w] of Object.entries(byType)) {
    if (w > best) {
      best = w;
      dominantType = t as ScamType;
    }
  }

  return { hits, ruleConfidence, dominantType };
}
```

---

## 📌 Status (এখন পর্যন্ত)

**হয়ে গেছে:**

- ✅ Web app-এর base setup (`package.json`, `tsconfig.json`, `next.config.js`, `.env.example`, `.gitignore`)
- ✅ Detection engine-এর প্রথম অংশ — hard-rules engine (`lib/rules.ts`)

**বাকি আছে:**

- ⬜ Gemini classifier (`lib/gemini.ts`)
- ⬜ `/api/analyze` API route
- ⬜ UI (`page.tsx`, layout, CSS)
- ⬜ `npm install` + run + verify
- ⬜ Pitch deck (slides + speaker notes)
- ⬜ Demo video script + subtitles
- ⬜ Vercel deploy + Gemini key + YouTube steps
