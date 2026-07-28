# WonderTale Kingdom — Prototype

This is the WonderTale Kingdom front-end prototype: a real, verified-working Vite + React
project (not just a single file) ready to deploy to a live URL.

**Important:** this is a UI prototype only. One sample story, one sample video, static
dashboard stats. There's no backend, no database, no accounts, no real AI generation, and
no payments yet — see the "Beyond This Prototype" section below for what a real launch needs.

## Option A — Deploy in ~5 minutes with no coding (recommended first step)

1. Go to https://vercel.com and sign up (free) with GitHub, Google, or email.
2. Go to https://github.com and create a new repository (e.g. `wondertale-kingdom`).
3. Upload every file in this folder to that repository. (On github.com you can use
   "Add file → Upload files" and drag the whole folder in — no terminal needed.)
4. Back in Vercel, click "Add New → Project", pick your `wondertale-kingdom` repo, and
   click Deploy. Vercel auto-detects Vite and configures the build for you.
5. In under a minute you'll get a live link like `wondertale-kingdom.vercel.app` you can
   send to anyone.

Netlify (https://netlify.com) works the same way if you'd rather use that.

## Option B — Run it locally first (if you have Node.js installed)

```bash
npm install
npm run dev
```

Then open the local address it prints (usually http://localhost:5173) in your browser.

To create the production build yourself:

```bash
npm run build
```

This outputs a `dist/` folder — the actual files a real web server would serve.

## Project structure

```
wondertale-project/
├── index.html          the page shell
├── package.json         dependencies (React, lucide-react icons, Vite, Stripe)
├── vite.config.js       build tool config
├── .env.example         template for your Stripe keys (copy to .env locally)
├── api/
│   ├── create-checkout-session.js   creates a real Stripe Checkout session
│   └── stripe-webhook.js            confirms real payment events from Stripe
├── src/
│   ├── main.jsx          mounts the app into the page
│   └── App.jsx           the whole WonderTale Kingdom app (all 11 pages)
```

## Setting up Stripe subscriptions (real payments)

There's a working "Upgrade to Premium" button on the Parent Dashboard. It calls a
real serverless function that creates a genuine Stripe Checkout session — this
isn't a mockup, it's actual payment infrastructure. To activate it:

1. Create a free account at https://dashboard.stripe.com
2. Go to **Products** → create a new Product (e.g. "WonderTale Premium") with a
   recurring monthly price. Copy the Price ID (starts with `price_...`).
3. Go to **Developers → API keys** → copy your **test mode** secret key
   (starts with `sk_test_...`) to start safely, before using real money.
4. In Vercel: **Project Settings → Environment Variables**, add:
   - `STRIPE_SECRET_KEY` = your secret key
   - `STRIPE_PRICE_ID` = your Price ID
5. Redeploy. The Upgrade button will now open a real, Stripe-hosted checkout page.
6. Test it with Stripe's test card `4242 4242 4242 4242`, any future expiry, any CVC.
7. For webhooks (recommended — confirms payments actually succeeded, not just that
   someone reached the success page): in Stripe, go to **Developers → Webhooks**,
   add an endpoint at `https://your-domain.vercel.app/api/stripe-webhook`, select
   the events `checkout.session.completed`, `customer.subscription.deleted`, and
   `invoice.payment_failed`, then copy the signing secret into Vercel as
   `STRIPE_WEBHOOK_SECRET`.
8. Only switch `STRIPE_SECRET_KEY` to your live key (`sk_live_...`) once you've
   tested the full flow in test mode and are ready to accept real charges.

**Important:** the webhook handler currently only logs events to the console —
it doesn't yet update anything, because there's no database yet to mark a user
as "premium" in. That's the next piece once you add a backend/database.


This front-end can stay mostly as-is, but a real product needs:

- **A backend + database** — real accounts, saved progress, an actual story library
  instead of hardcoded sample data
- **Real AI story generation** — an API (e.g. Anthropic's or OpenAI's) called from a
  backend, not the canned response currently shown
- **Real narration** — browser text-to-speech (currently used) is a fine scrappy MVP,
  but studio-quality launches usually use a paid TTS service or real voice actors
- **Stripe integration** — for the subscription/premium plans in the original spec;
  this requires a backend to handle webhooks and subscription state securely
- **COPPA compliance** — since this targets children under 13, US law requires parental
  consent flows and strict data-handling rules before you can legally collect any data
  from kids. This needs actual legal review, not just code.

For all of the above, a tool like **Claude Code** (which manages a real multi-file
project with a backend, not just a single chat artifact) or a hired developer is the
practical next step.
