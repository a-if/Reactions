# Auto Reaction Bot

Telegram bot that reacts to every message in channels, groups and private chats with a random emoji. Runs free on Cloudflare Workers.

## Commands

| Command | Who | What it does |
| --- | --- | --- |
| `/start` | everyone | Welcome message with buttons |
| `/reactions` | everyone | Shows the enabled emojis |
| `/donate` | everyone | Telegram Stars donation invoice |
| `/stats` or `/users` | admins | Number of users, groups and channels |
| `/broadcast` | admins | Reply to any message with `/broadcast [users\|groups\|channels\|all]` (default: users). A confirm button is shown first. |

Broadcast copies the message exactly (text, photo, video, buttons) with no "forwarded" tag. Chats that blocked the bot or removed it are cleaned up automatically.

## Deploy on Cloudflare (free)

1. Install and log in: `npm install` then `npx wrangler login`
2. Create the database: `npx wrangler d1 create reaction-bot` and paste the `database_id` into `wrangler.toml`
3. Create the queue: `npx wrangler queues create broadcast-queue`
4. Add the settings (dashboard: Workers > your worker > Settings > Variables, or `npx wrangler secret put NAME`):
   `BOT_TOKEN`, `BOT_USERNAME`, `EMOJI_LIST`, `ADMIN_IDS` (your Telegram ID from @userinfobot), and optionally `UPDATES_URL`, `SUPPORT_URL`, `START_ANIMATION`, `DONATE_ANIMATION`, `RANDOM_LEVEL`, `RESTRICTED_CHATS`
5. Deploy: `npx wrangler deploy`
6. Set the webhook: open `https://api.telegram.org/bot<BOT_TOKEN>/setWebhook?url=<YOUR_WORKER_URL>`

The users table is created automatically. Telegram can't list who already used a bot, so chats are counted from the moment this version goes live: users when they next message the bot, channels on their next post.

Free plan notes: a broadcast runs in small queue chunks (about 15 to 20 messages per second), so 1,000 users take about a minute. Free limits are 100,000 requests per day and 10,000 queue operations per day, which is plenty for this bot.

## Other hosts

`npm start` runs the same bot as a normal Node server (VPS, Docker, Render). Users are then saved in `data/chats.json` (`DATA_DIR` to change), so the disk must survive restarts. See `.env.example` for all settings.
