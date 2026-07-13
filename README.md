<p align="center">
  <img src="assets/banner.png" width="400" alt="TG Reaction Bot Panel banner" />
</p>

<h1 align="center">⚡ TG Reaction Bot Panel</h1>
<p align="center"><b>Automated Telegram Reaction Bot Deployment on Cloudflare Workers</b></p>

<p align="center">
  <a href="README.md"><img src="https://img.shields.io/badge/English-2f81f7?style=for-the-badge" alt="English"></a>
  <a href="README.fa.md"><img src="https://img.shields.io/badge/فارسی-2ea043?style=for-the-badge" alt="فارسی"></a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-1.0.0-caa14d?style=flat-square" alt="version">
  <img src="https://img.shields.io/badge/platform-Cloudflare%20Workers-f38020?style=flat-square" alt="platform">
  <img src="https://img.shields.io/badge/database-Cloudflare%20D1-2f81f7?style=flat-square" alt="database">
  <img src="https://img.shields.io/badge/deployment-single%20file-2ea043?style=flat-square" alt="deployment">
  <a href="#-license"><img src="https://img.shields.io/badge/license-MIT-8957e5?style=flat-square" alt="license"></a>
</p>

---

<p align="center">
  <img src="assets/screenshot-dashboard.png" width="340" alt="Panel preview" />
</p>

---

## 🎯 What is TG Reaction Bot Panel?

This project is a dashboard for running Telegram bots that do exactly one thing: react
to posts — the same ❤️ 🔥 🎉 you see under channel posts. Drop in a token from
BotFather, pick a set of emojis, and the panel takes care of registering the webhook,
listening for Telegram updates, and reacting the moment a new post comes in.

The whole application — routing, HTML rendering, session handling, calls to the
Telegram API, everything — sits inside **a single JavaScript file** that runs as a
Cloudflare Worker. Data is stored in Cloudflare D1, and the table structure is created
automatically the first time the Worker runs. There's no build step and no manual
migration — you upload one file, connect a database, and the panel is live.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🤖 Unlimited bots | Add as many bots as you have BotFather tokens for, each with its own emoji set and its own `/start` message |
| 🔗 Automatic webhook registration | Adding a bot automatically calls Telegram's `setWebhook`, secured with its own secret token |
| 🎯 Manual reactions by link | React to one or several `t.me/...` links with up to 40 bots at once, random or fixed emoji, adjustable delay |
| 🧠 Smart reaction mode | Picks an emoji based on the tone of the message text instead of choosing one at random |
| 📥 Bulk import | Import a whole batch of bots from a simple file in `token,emoji1|emoji2,welcome` format |
| 🔁 Retry on failure | Retry only the reactions that failed, without redoing the entire batch |
| 📊 History & daily stats | Every manual reaction is logged; each bot keeps a counter and a 7-day chart |
| 👤 Account system | Custom avatar, password changes, multi-session management, notification preferences |
| 🎨 Five themes | Dark, light, classic, glass, and cyberpunk — remembered per device |
| ⚡ No separate infrastructure | Runs entirely on Workers + D1, free-tier friendly |

---

## 📦 What Gets Created

When you deploy the panel, the following resources are created inside your own
Cloudflare account:

| Resource | Purpose |
|---|---|
| ⚙️ Cloudflare Worker | The whole panel — routing, dashboard, calls to the Telegram API |
| 🗄️ D1 Database | Stores users, bots, reaction history, sessions, and notifications |
| 🔗 D1 Binding | Connects the database to the Worker under the name `DB` |
| 🌐 workers.dev address | Your public address for logging in and receiving Telegram webhooks |
| 🪝 Per-bot webhook | `POST /webhook/<id>`, registered automatically for every bot you add |

---

## 📋 Requirements

| Requirement | Notes |
|---|---|
| Cloudflare account | Free plan is enough |
| Cloudflare D1 database | Created once, before you deploy |
| One or more Telegram bot tokens | From [@BotFather](https://t.me/BotFather) |
| A Chrome browser or similar | To use the dashboard |
| Node.js 18+ and npm | Only needed for the Wrangler method |

There's no other API key or outside service to set up — the session-signing key is
generated automatically the first time the Worker runs and is stored in your own D1
database.

---

## 🚀 Quick Start

Two ways to deploy, each covered from start to finish below: **Method 1** uses only the
Cloudflare dashboard in your browser — no terminal needed. **Method 2** uses Wrangler,
for anyone who'd rather have deployment be a single command from then on.

### 🖱️ Method 1 — Cloudflare Dashboard (Manual, recommended for beginners)

#### Step 1 — Create a Cloudflare account

1. Go to [dash.cloudflare.com/sign-up](https://dash.cloudflare.com/sign-up).
2. Enter your email and choose a password.
3. Confirm your email through the verification link.
4. Log in at [dash.cloudflare.com](https://dash.cloudflare.com).

> 💡 **Tip:** You don't need a domain, a paid plan, or payment details to use Workers
> and D1 on the free tier.

#### Step 2 — Create the D1 database

1. In the sidebar, click **Storage & Databases**.
2. Open the **D1** tab.
3. Click **Create database**.
4. Give it a name like `reaction-db` and click **Create**.
5. Keep the database page open — we'll connect it to the Worker in a moment.

You don't need to create any tables. The Worker builds its own schema the first time it
runs.

#### Step 3 — Create the Worker

1. In the sidebar, click **Compute** and open the **Workers & Pages** tab.
2. Choose **Create application**, then click **Start with Hello World!** among the
   options shown.
3. Pick a name (the suggested one works fine too), then click **Deploy**.

#### Step 4 — Upload `worker.js`

1. On the confirmation page, click **Edit code** to open Cloudflare's editor.
2. Select all the existing code and delete it.
3. In the project repository, open the **worker.js** file.
4. Click **Raw**, then select and copy the entire content.
5. Paste the copied code into the **Cloudflare** editor (alternatively, you can delete
   the `worker.js` already on Cloudflare and instead upload the file you downloaded
   from the repository).
6. Click **Save and Deploy**.

> ⚠️ Now we need to connect the **D1** database to the **Worker**.

#### Step 5 — Connect the D1 database to the Worker

1. From **Workers & Pages**, click on your Worker (the one you just created).
2. Go to the **Settings** tab.
3. Scroll down to **Bindings** and click **Add binding**.
4. Set the binding type to **D1 database**, then fill in two fields:
   - **Variable name:** `DB` — this has to be exactly this value.
   - **D1 Database:** the database you created in step 1.

   Click **Save**.
5. Go back to the Worker's main page and click **Deploy**.

> 🔔 **Note:** Bots and webhook secrets are generated automatically and stored in D1.

#### Step 7 — Open the panel and finish first-time setup

1. Open the Worker's address in your browser.
2. Since no user exists yet, you'll be sent to `/setup` automatically.
3. Choose a username and password (at least 6 characters) for the admin account.
4. Submit the form — this creates your account, logs you in, and opens the dashboard.

> 💡 **Tip:** The first account created through `/setup` becomes the admin account.
> After that, `/setup` redirects to `/login`, so keep your password somewhere safe.

#### Step 8 — Add your first bot

1. On the dashboard, use the **add bot** form and paste a token from
   [@BotFather](https://t.me/BotFather).
2. Choose which emojis it's allowed to use, and optionally set a custom `/start`
   message.
3. Submit — the panel verifies the token with Telegram, registers the webhook
   automatically, and the bot goes live.

---

### 💻 Method 2 — Wrangler CLI

⚠️ Note: log into the Cloudflare dashboard/account in your browser first, then start
these steps.

#### Step 1 — Install Node.js

Wrangler needs Node.js 18 or newer.

1. Download an LTS release from [nodejs.org](https://nodejs.org/).
2. Install it.
3. Run the following command in CMD (or your terminal) to make sure Node.js is
   installed:

```bash
node -v
```

You should see something like `v18.x.x` or higher. If you get an error instead,
reinstall Node before continuing.

#### Step 2 — Get the project onto your machine

```bash
cd reaction-bot
```

If you only have the core files, put `worker.js`, `wrangler.toml`, and `package.json`
together in one folder and `cd` into it the same way.

#### Step 3 — Install Wrangler

Install Wrangler with the following command in CMD or your terminal:

```bash
npm install wrangler
```

This installs Wrangler into `node_modules` — there's nothing else to install, since
everything else runs on Workers' built-in APIs.

#### Step 4 — Log in to Cloudflare

```bash
wrangler login
```

This opens a browser window so you can authorize Wrangler against your Cloudflare
account.

#### Step 5 — Create the D1 database

```bash
wrangler d1 create reaction-bot-db
```

Wrangler prints an output block containing a `database_id`. Copy it.

#### Step 6 — Update `wrangler.toml`

Open this file in an editor like VS Code or Notepad.

```toml
name = "reaction-bot"
main = "worker.js"
compatibility_date = "2024-09-23"

[[d1_databases]]
binding = "DB"
database_name = "reaction-bot-db"
database_id = "REPLACE_WITH_YOUR_DATABASE_ID"
```

Replace `REPLACE_WITH_YOUR_DATABASE_ID` with the ID from step 5. Leave
`binding = "DB"` exactly as it is.

#### Step 7 — Deploy the Worker

```bash
wrangler deploy
```

Wrangler uploads the file, wires up the D1 binding, and publishes the Worker, printing
an address like:

```
https://tg-reaction-bot.your-subdomain.workers.dev
```

#### Step 9 — Open the deployed panel

Open the address from step 7. Just like Method 1, you'll land on `/setup`
automatically — create your admin account there.

**That's it — from an empty terminal to a fully working panel.**

---

## ⚙️ Configuration

| Setting | Where it lives | How it's set |
|---|---|---|
| Database connection | `DB` binding | Once, during deployment |
| Session signing secret | `app_config` table | Generated automatically on first run |
| Admin username/password | `users` table | Set by you during `/setup` |
| Bot tokens, emojis, welcome messages | `bots` table | Set by you from the dashboard |
| Theme preference | Browser storage | Set from the theme switcher in the header |

## 🔑 Environment Variables

None are required. The only thing you need to configure is the `DB` binding —
technically it isn't an "environment variable" in the Workers sense of the term, but
it's the one piece of configuration the app can't run without.

## 🗄️ Database

On the very first request against a new database, the Worker runs the following
`CREATE TABLE IF NOT EXISTS` statements:

| Table | Purpose |
|---|---|
| `users` | Accounts, password hashes, avatars, notification preferences |
| `bots` | Registered bots, tokens, emoji sets, status |
| `stats_daily` | Per-bot daily counters used for the dashboard charts |
| `link_reactions` | History of manual link-based reaction batches |
| `recent_links` | Recently used links, for autocomplete |
| `sessions` | Active sessions per device |
| `notifications` | In-app notifications |
| `app_config` | Small internal key/value store (currently just the session secret) |

Since every statement is `IF NOT EXISTS`, running the Worker against an
already-set-up database is always safe — nothing gets deleted. The same logic adds new
columns to older databases when the code is updated, so updating never requires manual
SQL.

## 🔒 Security

- Passwords are hashed with PBKDF2 (SHA-256, 100,000 iterations) plus a random salt
  per user — the raw password is never stored.
- Sessions use an HMAC-signed token, kept only in an `HttpOnly`, `Secure`,
  `SameSite=Lax` cookie; the signing key is generated randomly and stored in D1.
- Every bot has its own random webhook secret, checked on every incoming update
  through the `X-Telegram-Bot-Api-Secret-Token` header — a mismatch means the request
  is rejected right away.
- Review and revoke sessions, individually or all at once, from the account page.

> 🔔 **Note:** Bot tokens are stored as-is in your own D1 database, which only you and
> anyone with access to your Cloudflare account can read. Treat that access the same
> way you'd treat a server's root password.

## ❓ FAQ

**Do I need a paid Cloudflare plan?**
No — the free Workers quota and D1's storage/row limits are usually more than enough
for a personal setup.

**Can I run more than one bot?**
Yes, with no hard limit, other than the 40-bot cap per batch of manual link reactions.

**What if I forget my password?**
There's no automatic recovery yet — you'd need to reset it directly from the D1
console in the Cloudflare dashboard, or redeploy with a fresh database.

**Can more than one person use the same panel?**
Multiple accounts are supported, but bots, links, and history are scoped to each
account — there's no shared/team view.

**Does this go against Telegram's rules?**
Reaction bots only interact with Telegram through its official Bot API, using tokens
you create yourself via BotFather. Using it in line with Telegram's rules, for your own
bots and channels, is on you.

## 🛠️ Troubleshooting

| Problem | Likely cause | Fix |
|---|---|---|
| Blank page or "Internal Server Error" | `worker.js` wasn't fully copied, or the `DB` binding isn't set up | Recheck step 4 and 5 (Method 1) or step 6 (Method 2) |
| Redirected to `/setup` even after creating an account | The D1 binding points to a different database | Check the database name/ID in the binding |
| "Invalid token" when adding a bot | Wrong token, or the bot was deleted/regenerated in BotFather | Get a fresh token from `/mybots` |
| A bot that used to work stops reacting | Webhook registration failed, or the bot lost access in the chat | Use **retry webhook**; check the bot's membership |
| All manual reactions fail | The channel/group isn't public, or the bot isn't a member | Use a public link, or add the bot |
| Session keeps logging out | The `DB` binding was changed | Log in again; make sure only one database is bound |

## 🔄 Updating

- **Dashboard method:** open **Edit code**, replace the content with the new
  `worker.js`, click **Save and deploy**.
- **Wrangler method:** replace `worker.js`, then run `wrangler deploy` again.

New tables/columns are created automatically on the first request after redeploying —
existing bots, history, and accounts are left untouched.

## 🏷️ Version

Current release: **v1.0.0** — first public release: bot management, manual link
reactions with smart mode, bulk import, account/session management, and five themes.

## 🙏 Credits

Built and maintained by **Mehdi Rashidi-zadeh**
([@code_watch](https://t.me/code_watch) on Telegram).

## 📄 License

Released under the MIT License. See the `LICENSE` file for the full text.
