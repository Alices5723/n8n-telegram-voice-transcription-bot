# Telegram Voice Bot — n8n template

A private Telegram bot that turns your voice messages into clean, structured text. Built as a single [n8n](https://n8n.io/) workflow you can import in one click.

Send a voice note (your own or forwarded from someone else) → the bot replies with **two messages**: a cleaned transcript and a short summary. One tap on the inline button to translate, or to draft a polished English email from the same content. If you literally say *"write an email"* in the voice, the bot generates the email automatically as a third message.

Powered by **Groq** (Whisper-large-v3-turbo for transcription, Llama-3.3-70b-versatile for cleanup / summarization / translation / email composition). Designed for the Groq **free tier** — zero infrastructure cost beyond your existing n8n instance.

---

## Main features

- 🎙 **Transcription** — fast and accurate, removes filler words ("uh", "ну", "like", "типа", etc.), false starts, and stutters; preserves original language.
- 📝 **Smart summary** — automatically generated bullet points covering the key content. Conservatively detects **action items**, **decisions**, and **open questions** if they were stated explicitly.
- 🌐 **One-tap translation** — every reply has a `Translate` button. Voice in English → translates to Ukrainian. Voice in Ukrainian or Russian → translates to English. Each message (transcript / summary / email) can be translated independently.
- ✉️ **Email composer** — tap the `Email EN` button next to a transcript to convert it into a polished English business email with `Subject:`, greeting, body, and sign-off.
- 🗣 **Voice triggers** — if you say *"write an email"*, *"напиши имейл"*, *"напиши лист"*, *"draft a letter"* etc. in the voice, the bot auto-generates the English email as a third message without you needing to tap anything.
- ↪️ **Forwarded voice support** — works on voice notes forwarded to the bot from any other chat, with a `↪️ Forwarded` badge in the reply.
- 🔒 **Whitelisted by chat_id** — only you (and anyone you explicitly add) can use the bot. Everyone else is silently ignored.
- 🧹 **Built-in deduplication** — Telegram occasionally sends the same voice note twice; the workflow drops duplicates by `file_unique_id` and `chat:message_id` automatically.

---

## What you need to provide

You will need three things. None of them require a paid subscription.

### 1. Telegram bot token

1. Open [@BotFather](https://t.me/BotFather) in Telegram.
2. Send `/newbot`, follow the prompts to pick a name and username.
3. BotFather replies with a token like `8898734097:AAEDIjz-uZYuZE2wzNalSCZDOkm3rQiXdE0`. **Copy it.**

### 2. Your Telegram chat_id

This is the numeric ID of your personal chat with the bot (used for the whitelist so strangers can't burn through your API quota).

1. Open the bot you just created in Telegram and send it any message (e.g., `/start`).
2. In your browser, visit: `https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates`
   (replace `<YOUR_BOT_TOKEN>` with the token from step 1)
3. Look in the JSON response for `"chat":{"id": 123456789,...}` — the number after `"id":` is your `chat_id`. **Copy it.**

Alternative: message [@userinfobot](https://t.me/userinfobot), it will reply with your `chat_id`.

### 3. Groq API key

1. Go to [console.groq.com](https://console.groq.com/) and sign up (free).
2. Open **API Keys** → **Create API Key**.
3. Copy the key — it starts with `gsk_`.

---

## Setup (n8n)

### Step 1. Import the workflow

1. Download [transcriptor.json](./transcriptor.json) from this repo.
2. In your n8n instance, open the workflows list → **Add Workflow** → **Import from File** → select `transcriptor.json`.

### Step 2. Create credentials

n8n needs two credentials. Create them once and reuse across all nodes.

**A) Telegram credential**

- In n8n: **Credentials** → **Create New** → **Telegram API**.
- Name: `Telegram Voice Bot` (or anything you like).
- Access Token: paste your bot token from BotFather.
- Save.

**B) Groq credential**

- In n8n: **Credentials** → **Create New** → **Header Auth**.
- Name: `Groq API` (or anything you like).
- Name (header): `Authorization`
- Value: `Bearer gsk_...` (paste your Groq key after the word `Bearer ` — note the space)
- Save.

### Step 3. Link credentials to all nodes

Open each of these workflow nodes and pick the matching credential from the dropdown:

**Telegram credential** (7 nodes):
- `Telegram Trigger`
- `Get Voice File`
- `Send Reply`
- `Send Reply (Email btn)`
- `Answer Callback`
- `Send Translation`
- `Translation Not Found`

**Groq credential** (3 nodes):
- `Whisper (Groq)`
- `LLM Clean+Summary`
- `Translate LLM`

Tip: in n8n you can right-click a credential field and apply it to all nodes of the same type at once.

### Step 4. Set your chat_id whitelist

Open the node **`Auth (voice)`** in the workflow. In the JavaScript, replace `123456789` with your real `chat_id`:

```js
const ALLOWED = [123456789];   // ← put your chat_id here
```

Do the same in the **`Auth (callback)`** node (same line, same replacement).

To allow multiple users: `const ALLOWED = [111111111, 222222222];`

### Step 5. Activate

Toggle the workflow's **Active** switch to ON. n8n will automatically register the webhook with Telegram on activation — no manual `setWebhook` call needed.

### Step 6. Test

Open your bot in Telegram and send a voice note. Within ~5–10 seconds you should receive two replies: the cleaned transcript and a short summary, each with a `Translate` button. The transcript also has an `✉️ Email EN` button.

---

## Free tier limits

The workflow is designed to fit comfortably in Groq's free tier ([rate limits page](https://console.groq.com/docs/rate-limits)).

| Model | Free tier limit | What it does |
|---|---|---|
| `whisper-large-v3-turbo` | 20 RPM • 2 000 requests/day • 7 200 sec audio/hour | Voice transcription |
| `llama-3.3-70b-versatile` | 30 RPM • 1 000 requests/day • 12 K tokens/min • **100 K tokens/day** | Cleanup, summary, translation, email |

**Practical capacity per day on the free tier:**

| Usage pattern | Approx voices/day |
|---|---|
| Just transcribe + summarize | ~50 |
| + tap Translate on every reply | ~25 |
| + auto-email or Email button on every reply | ~15–20 |

The bottleneck is **TPD** (tokens per day) on Llama. If you hit it, you have three options:
1. Upgrade to Groq **Developer tier** for much higher limits.
2. Swap the LLM model to `llama-3.1-8b-instant` (higher limits, lower quality).
3. Add a fallback to OpenAI `gpt-4o-mini` (~$0.001 per voice, not free but cheap).

Whisper limits (audio seconds/hour and requests/day) are not a real constraint for personal use.

---

## How it works

1. Telegram delivers a voice (or forwarded voice) to the n8n webhook.
2. `Route` switches between `message` and `callback_query` updates.
3. `Auth (voice)` checks the whitelist and dedup keys, drops duplicates and unauthorized users.
4. `Get Voice File` downloads the `.oga` audio binary from Telegram.
5. `Fix Binary Filename` renames the binary to `voice.ogg` (Groq Whisper rejects `.oga`).
6. `Whisper (Groq)` transcribes the audio and returns the raw text + detected language.
7. `LLM Clean+Summary` makes one JSON-mode call that returns: cleaned transcript, summary bullets, action items, decisions, questions, language code, `email_requested` boolean, and `email_en` (if requested).
8. `Build HTML` formats 2 or 3 outbound messages with localized section headers.
9. `Needs Email Button?` IF-node routes to one of two `Send Reply` nodes — one with `[Translate]` only, one with `[Translate] [Email EN]`.
10. `Store Translation Map` saves the reply `message_id` → `{ text, sourceLang, targetLang, kind }` mapping in workflow static data (cap: 200 entries, FIFO).
11. When you tap a button, the callback path looks up the stored text, runs a translate or email-compose LLM call, and sends a new reply with the result.

---

## Customization

- **Add more buttons** — duplicate one of the `Send Reply` nodes and add new buttons (e.g., *"Shorter version"*, *"Formal tone"*, *"Slack message"*). Wire a new callback action in `Lookup + Build Translate`.
- **Change default translation target** — search the workflow's JS for `targetLang = sourceLang === 'en' ? 'uk'` and replace `'uk'` with any ISO 639-1 code (e.g., `'ru'`, `'es'`, `'de'`).
- **Archive transcripts** — add a Google Sheets / Notion / Postgres node after `Store Translation Map` to log every voice into a searchable archive.
- **Send to Slack/Discord** — fork the `Send Reply` outputs into your team channel for sharing voice-derived notes.
- **Auto-pin action items** — add a `pinChatMessage` Telegram call when the summary contains a non-empty `action_items` list.

---

## License

MIT — do anything you want with it.

---

## Credits

Built with [n8n](https://n8n.io/) (workflow automation), [Groq](https://groq.com/) (Whisper + Llama hosting), and the [Telegram Bot API](https://core.telegram.org/bots/api).
