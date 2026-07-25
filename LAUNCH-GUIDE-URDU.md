# ClipShip — Launch Guide (Roman Urdu)

Ye guide aapko zero se live SaaS tak le kar jayegi. Har step ke liye free tier available hai.

---

## Step 0: Kya kya chahiye (sab free signup)

| Service | Kis liye | Link |
|---|---|---|
| GitHub account | Code rakhne ke liye | github.com |
| Vercel account | Hosting (free) | vercel.com |
| Neon account | PostgreSQL database (free) | neon.tech |
| Google Cloud Console | Google login ke liye | console.cloud.google.com |
| MuAPI account | YouTube/TikTok publishing | muapi.ai |
| Stripe account | Payments lene ke liye | stripe.com |

---

## Step 1: Local par chalao (test ke liye)

```bash
cd Free-AI-Social-Media-Scheduler
npm install
cp .env.example .env
```

Ab `.env` file open karo aur neeche wali values fill karo (Step 2–5 se milengi).

---

## Step 2: Database (Neon.tech — free)

1. neon.tech par signup karo
2. New Project banao
3. Dashboard se **connection string** copy karo
4. `.env` mein daalo:
   - `DATABASE_URL` = pooled connection string (jis mein `-pooler` ho)
   - `DIRECT_URL` = direct connection string

Phir migrations chalao:
```bash
npx prisma migrate deploy
```
(Agar ye fail ho to `npx prisma db push` try karo.)

---

## Step 3: Google Login (Google Cloud Console)

1. console.cloud.google.com → New Project
2. "APIs & Services" → "OAuth consent screen" → External → basic info fill karo
3. "Credentials" → "Create Credentials" → "OAuth Client ID" → Web application
4. **Authorized redirect URIs** mein add karo:
   - Local test: `http://localhost:3000/api/auth/callback/google`
   - Live site: `https://aapki-domain.com/api/auth/callback/google`
5. Client ID aur Secret copy kar ke `.env` mein daalo:
   - `GOOGLE_CLIENT_ID`
   - `GOOGLE_CLIENT_SECRET`

`NEXTAUTH_SECRET` generate karne ke liye terminal mein:
```bash
openssl rand -base64 32
```

---

## Step 4: MuAPI (publishing engine)

1. muapi.ai par signup karo
2. Dashboard se API key copy karo → `.env` mein `MUAPIAPP_API_KEY`
3. `WEBHOOK_URL` mein apni site ka URL daalo (local test: `http://localhost:3000`)

**Cost note:** MuAPI pay-as-you-go hai — jitna use, utna paisa. Shuru mein $5 top-up kaafi hai testing ke liye. Har publish par credits katenge — apne Stripe pricing se hamesha zyada margin rakho.

---

## Step 5: Stripe (payments)

1. stripe.com par signup karo (Pakistan se directly Stripe available nahi — options: Stripe Atlas, ya kisi supported country ki entity, ya alternative jaise Lemon Squeezy/Paddle jo Pakistan support karte hain. Ye pehle research kar lena.)
2. Test mode mein API keys lo:
   - `STRIPE_SECRET_KEY` (sk_test_...)
   - `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` (pk_test_...)
3. Webhook banao: Dashboard → Developers → Webhooks → endpoint `https://aapki-domain.com/api/stripe/webhook` → signing secret copy karo → `STRIPE_WEBHOOK_SECRET`

**Pricing already set hai** (`src/lib/config.js` mein):
- Starter: 100 credits = $5
- Pro: 500 credits = $15
- Business: 2000 credits = $45

Apne hisaab se change kar sakte ho.

---

## Step 6: Test run

```bash
npm run dev
```
Browser mein `http://localhost:3000` kholo. Google se login karo, video URL paste karo, schedule karo.

---

## Step 7: Vercel par deploy (live karna)

1. Apna code apne GitHub account par push karo:
```bash
git remote remove origin
git remote add origin https://github.com/AAPKA-USERNAME/clipship.git
git add -A && git commit -m "ClipShip launch" && git push -u origin main
```
2. vercel.com → "Add New Project" → apni repo select karo
3. Environment Variables section mein `.env` ki saari values paste karo
   - `NEXTAUTH_URL` ab aapki live URL hogi (e.g. `https://clipship.vercel.app`)
4. Deploy dabao

Deploy ke baad Google OAuth redirect URI aur Stripe webhook URL mein live domain add karna mat bhoolna.

---

## ⚠️ Zaroori Warnings (honestly)

1. **TikTok/YouTube approval:** MuAPI publishing ke through platform policies apply hoti hain. Agar aap khud direct YouTube Data API / TikTok Content Posting API use karna chahen to app review lagti hai jo din/hafte le sakti hai.
2. **Stripe Pakistan mein directly nahi chalta** — ye sabse bara practical hurdle hai. Lemon Squeezy ya Paddle jaise "merchant of record" options dekho, ya kisi aur payment integration par migrate karo.
3. **MuAPI dependency:** Poora publishing MuAPI par depend karta hai. Agar wo band ho jaye ya prices barha de to aapka product ruk jayega. Unki terms of service parhna zaroori hai — especially reselling/white-labeling ke liye.
4. **Terms & Privacy pages:** Footer mein /terms aur /privacy ke links hain — launch se pehle ye pages likhna legally zaroori hai.
5. **Ye "ready to sell" tab hoga jab customers aayenge** — code sirf 20% kaam hai. Asli kaam distribution hai: kis ko bechoge, kahan se laoge. Pehle 10 customers manually dhoondo (agencies, YouTubers, TikTok creators).

---

## Naam change karna ho to

Sirf 3 jagah:
1. `src/lib/config.js` → `appName`
2. `src/app/layout.js` → `title` aur `description`
3. `src/components/Footer.js` → copyright line
