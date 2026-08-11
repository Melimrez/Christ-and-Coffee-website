# Temple Wellness Coach — Setup Guide

Everything is coded and ready. This walks you through the two accounts you need
to create (I can't do this part for you — it involves your own billing info),
then wiring the two files together, then testing.

Total time: ~30-45 minutes.

---

## What you're setting up

- **The chat page** — already built: [`temple-wellness-coach.html`](../temple-wellness-coach.html)
  Lives right on your site once you push it (e.g. `christandcoffee.net/temple-wellness-coach.html`).
- **The backend** — already built: [`worker.js`](./worker.js)
  This is a small function that holds your API key and the coach's personality
  safely, and does the actual talking to Claude. It runs on Cloudflare, for free.

---

## Step 1 — Get an Anthropic API key

1. Go to **console.anthropic.com** and create an account (or sign in).
2. Add a payment method under **Settings → Billing**. Usage is pay-as-you-go —
   for a small group checking in daily, this is realistically a few dollars a
   month, not a subscription-sized cost.
3. Go to **Settings → API Keys → Create Key**. Name it something like
   `tw-coach`. Copy the key somewhere safe for a moment — you'll paste it into
   Cloudflare in Step 3.

## Step 2 — Create a free Cloudflare account

1. Go to **dash.cloudflare.com → Sign Up**. Free tier is plenty for this.
2. Once logged in, in the left sidebar go to **Workers & Pages**.
3. Click **Create → Workers → Create Worker**.
4. Give it a name — e.g. `tw-coach`. Cloudflare will show you a URL like
   `https://tw-coach.YOUR-NAME.workers.dev` — **copy this URL**, you'll need it
   in Step 4.
5. Click **Deploy** (it'll deploy Cloudflare's placeholder code — that's fine,
   you're about to replace it).

## Step 3 — Paste in the real code and set your secrets

1. On your new Worker's page, click **Edit Code** (opens an in-browser editor).
2. Delete everything in the editor, and paste in the entire contents of
   [`worker.js`](./worker.js) from this folder.
3. Click **Deploy** / **Save and Deploy**.
4. Go back to the Worker's main page → **Settings → Variables and Secrets**.
5. Add these two, both as **Secret** (not plain text):
   - `ANTHROPIC_API_KEY` → paste the key from Step 1
   - `TW_PASSCODE` → make up the shared passcode you'll give Temple Wellness
     members (e.g. a word + number, something easy to say out loud at a meeting
     but not guessable — not "temple123")
6. Save.

## Step 4 — Point the website at your Worker

1. Open [`temple-wellness-coach.html`](../temple-wellness-coach.html) in a text
   editor.
2. Find this line near the bottom (in the `<script>` section):
   ```
   const WORKER_URL = "https://tw-coach.YOUR-SUBDOMAIN.workers.dev";
   ```
3. Replace it with the actual URL you copied in Step 2.4.
4. Save the file.

*(Tell me when you've done Steps 1-3 and I can make this edit for you instead —
just paste me the Worker URL.)*

## Step 5 — Add it to the site

1. Commit and push `temple-wellness-coach.html` to your GitHub repo like any
   other page (I can do this part with you).
2. Add a link to it somewhere Temple Wellness members will find it — e.g. a
   button on the Programs & Events page, or on the dedicated Temple Wellness
   page if we build that next.
3. Give out the passcode from Step 3.5 to registered Temple Wellness members
   (e.g. in a welcome email/text, or announced at orientation).

## Step 6 — Test it

1. Visit the page yourself, enter the passcode, and have a real conversation —
   introduce yourself as a first-time user and see how it onboards you.
2. Try a few edge cases: a slip-up confession, a craving question, attaching a
   food photo, and a wrong passcode (should be rejected).
3. Once you're happy with the tone, roll it out.

---

## Notes for later

- **Changing the passcode**: just update the `TW_PASSCODE` secret in Cloudflare
  (Step 3.5) — no code changes needed. Good to do each new Season.
- **Changing the coach's personality**: edit the `SYSTEM_PROMPT` text near the
  top of `worker.js` and redeploy (Step 3.1-3.3). I can help with wording changes
  any time.
- **Chat history**: currently saved in each woman's own browser (so it
  remembers her across visits on the same device/browser). If she clears her
  browser data or switches devices, history resets. If that becomes a problem,
  we can upgrade to server-side storage — just ask.
- **Cost control**: if usage ever gets high, we can switch the `MODEL` constant
  in `worker.js` to a cheaper model, or add a daily message cap.
