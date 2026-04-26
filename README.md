# OpenClaw on macOS: getting started

Draft for Rich.

## Goal

Set up OpenClaw on a Mac in a way that is:

- easy to install and maintain
- good enough for day-to-day use
- less risky than running it inside the main admin account

This guide assumes:

- OpenClaw will run inside a **separate macOS user account**
- that account will **not** have admin privileges
- Node will be installed via **Pixi**
- OpenClaw will be installed with npm using that Node

## Cliff notes

If you just want the short version, the basic pipeline is:

1. **Pixi** installs and manages software environments
2. **Node** gets installed inside Pixi's setup
3. **OpenClaw** gets installed using npm, which comes with Node
4. **Telegram bot** gives you a simple way to talk to OpenClaw
5. once that barebones setup works, you can ask OpenClaw to help read GitHub, explain errors, and gradually help configure more things

A few practical ideas:

- when setting up OpenClaw, configure it to use **OpenAI Codex** as the model/provider, since that is the setup path we followed
- computers are very happy when instructions live in **plain text files** inside a user's home folder
- if you need to edit a text file from Terminal, **`nano`** is a simple editor to start with

For example, a file in the user's home directory might live at:

```bash
~/notes.txt
```

And Rich could edit it in Terminal with:

```bash
nano ~/notes.txt
```

## Definitions and basic computer stuff

These click-to-open sections explain a few terms Rich might not have seen before.

<details>
<summary><strong>What is Pixi?</strong></summary>

Pixi is a package manager and environment manager.

A package manager is a tool for installing software and keeping track of the fiddly dependencies that software needs. We usually use package managers for programming tools because they handle a lot of the annoying plumbing for us.

In this guide, we use Pixi to install Node and to create a separate voice-transcription environment. The nice bit is that it keeps those tools more self-contained inside the OpenClaw user account.
</details>

<details>
<summary><strong>What is Node?</strong></summary>

Node.js is the runtime that OpenClaw uses. You can think of it as the engine that runs JavaScript/TypeScript command-line apps.

Node also comes with **npm**, which is the package manager used to install OpenClaw.

OpenClaw is distributed as a Node package, so Node has to be installed first.
</details>

<details>
<summary><strong>What is OpenAI Codex in this setup?</strong></summary>

In this guide, OpenAI Codex is the model/provider setup you should use for OpenClaw.

That is the path we used together, and it is a sensible starting point for coding help, investigation, and general assistant work.
</details>

<details>
<summary><strong>What is npm?</strong></summary>

npm is the package manager that comes with Node.js.

Package managers are the usual way to install programming software because they handle versioning, dependencies, and other behind-the-scenes setup steps for you.

In this guide, npm is what installs OpenClaw itself.
</details>

<details>
<summary><strong>What is sudo?</strong></summary>

`sudo` is the macOS/Linux command for “run this command with administrator power”.

For this setup, that is usually **not** what we want. If a step asks for `sudo`, that often means the install is trying to write outside the OpenClaw user account.
</details>

<details>
<summary><strong>What is a standard/non-admin macOS user?</strong></summary>

A standard user can use the Mac normally, but cannot freely change system-wide settings or install privileged software without an admin password.

That is useful here because it reduces the damage OpenClaw could do if something went wrong.
</details>

<details>
<summary><strong>What is BotFather?</strong></summary>

BotFather is Telegram’s official bot for creating and managing other Telegram bots.

When you create an OpenClaw Telegram bot, BotFather is where you get the bot token.
</details>

<details>
<summary><strong>What is Whisper?</strong></summary>

Whisper is a speech-to-text tool. It takes audio, like a Telegram voice note, and turns it into text.

In this setup, we use a local Whisper install so Rich can send voice notes and OpenClaw can read them.
</details>

<details>
<summary><strong>What is a text file, and why put it in the home folder?</strong></summary>

A text file is just a plain file full of words, settings, or notes.

A user's home folder is the normal place for personal files on macOS. Keeping setup notes and small config-related files there is simple and predictable.
</details>

<details>
<summary><strong>What is nano?</strong></summary>

`nano` is a simple text editor that runs inside Terminal.

It is handy when you need to edit a text file without learning a more advanced editor first.
</details>

## Big-picture safety model

This setup is meant to reduce risk, not eliminate it.

What this helps with:

- keeping OpenClaw away from the main user account's files by default
- making it harder for OpenClaw or a compromised tool to casually modify system settings
- reducing the chance of accidental damage in the main account

What this does **not** guarantee:

- it is **not** a hard sandbox
- a process that somehow gains root/admin access could still break out of the separate user account
- browser, kernel, or privilege-escalation bugs are still in scope

So the recommended stance is:

- **separate non-admin macOS user** = sensible baseline
- **separate machine or VM** = stronger isolation if needed later

## Recommended setup

### 1. Create a dedicated macOS user

Create a dedicated user for OpenClaw, for example:

- username: `openclaw`
- account type: **Standard** (not Administrator)

Why:

- keeps files, browser state, tokens, and config separate from the normal user
- reduces the blast radius if something goes wrong

Notes:

- do **not** grant this user admin rights
- do **not** add passwordless sudo
- do **not** share SSH keys or shell history between the normal user and the OpenClaw user

### 2. Open Terminal and install Pixi

Log into the dedicated `openclaw` account, then open the **Terminal** app and run:

```bash
curl -fsSL https://pixi.sh/install.sh | sh
```

This is the install command shown on Pixi's site for macOS/Linux.

Then restart Terminal, or open a fresh Terminal window, and confirm Pixi is available:

```bash
pixi --version
```

Official docs:

- <https://pixi.sh/latest/installation/>

### 3. Install Node using Pixi

Install Node inside this user account with:

```bash
pixi global install nodejs
```

Then confirm:

```bash
node --version
npm --version
which node
which npm
```

If `node` or `npm` is not found, close Terminal and open it again.

## 4. Install OpenClaw

Install OpenClaw from npm in the dedicated account:

```bash
npm install -g openclaw@latest
```

Then confirm:

```bash
openclaw --version
openclaw status
```

If npm asks for admin permissions or suggests `sudo`, stop and fix the user-local Node/npm setup first. The goal is to keep this install inside the non-admin OpenClaw account.

### 4.1 Choose OpenAI Codex during setup

After OpenClaw is installed, run:

```bash
openclaw configure
```

During setup, choose **OpenAI Codex** as the provider/model path.

That is the setup we used together, and it is the best default for this guide.

## 5. Run OpenClaw only inside this account

Important rule:

- OpenClaw should be installed, configured, and run only inside the dedicated `openclaw` user
- avoid also installing/configuring it in the brother's normal user account unless there is a clear reason

This keeps:

- tokens
- config
- session data
- browser/session state
- memory files

confined to the OpenClaw account by default.

## 6. Set up Telegram with BotFather

If you want to talk to OpenClaw through Telegram, the cleanest route is a bot.

### Create the Telegram bot

1. Open Telegram.
2. Start a chat with **@BotFather**.
3. Run:

```text
/newbot
```

4. Follow the prompts to choose:
   - a bot name
   - a bot username ending in `bot`
5. Save the bot token somewhere safe.

### Connect that bot to OpenClaw

Open Terminal in the `openclaw` user and run:

```bash
openclaw configure --section channels
```

Then enable Telegram and paste the bot token when prompted.

A good default setup is:

- Telegram enabled
- DM policy: pairing
- group replies only when mentioned

That is roughly the setup we used.

### Start the gateway and approve the first DM

Start OpenClaw:

```bash
openclaw gateway start
```

Then send the bot a DM from Telegram.

Because the default DM policy is usually **pairing**, approve the first conversation with:

```bash
openclaw pairing list telegram
openclaw pairing approve telegram <CODE>
```

Pairing codes expire, so if one goes stale, just message the bot again and approve the fresh one.

## 7. Set up voice note transcription

This is the part that took a bit of fiddling in our setup.

The version that actually worked reliably was:

- keep Telegram as the chat channel
- use a **local Whisper CLI** for transcription
- run that Whisper CLI inside a **Pixi environment** so `ffmpeg` is available on `PATH`

### Why this approach

We first tried provider-based transcription, but the setup that ended up working cleanly was a local Whisper toolchain managed by Pixi.

That matters because Telegram voice notes arrive as audio files (commonly `.ogg`), and Whisper needs the right media tooling around it.

### Create a dedicated Pixi environment for transcription

Make a separate folder for voice transcription work:

```bash
mkdir -p ~/voice-transcription
cd ~/voice-transcription
```

Create a `pixi.toml` like this:

```toml
[workspace]
authors = ["Rich"]
channels = ["conda-forge"]
name = "voice-transcription"
platforms = ["osx-arm64", "osx-64"]
version = "0.1.0"

[tasks]
transcribe = "mkdir -p transcripts && whisper --model small --output_format txt --output_dir transcripts"

[dependencies]
python = "3.11.*"
pip = ">=26.0.1,<27"
ffmpeg = ">=8.0.1,<9"

[pypi-dependencies]
openai-whisper = ">=20250625, <20250626"
```

Then install the environment:

```bash
pixi install
```

Optional quick smoke test:

```bash
pixi run whisper --help
```

### Point OpenClaw at that transcription setup

The working pattern we used was to configure OpenClaw audio transcription to call Whisper via Pixi instead of calling the Whisper binary directly.

That preserves the Pixi-managed environment, including `ffmpeg`.

The relevant idea is:

```json
{
  "tools": {
    "media": {
      "audio": {
        "enabled": true,
        "timeoutSeconds": 600,
        "models": [
          {
            "type": "cli",
            "command": "pixi",
            "args": [
              "run",
              "-m",
              "/Users/RICH_USERNAME/voice-transcription",
              "-x",
              "whisper",
              "--model",
              "small",
              "--output_format",
              "txt",
              "--output_dir",
              "/Users/RICH_USERNAME/voice-transcription/transcripts",
              "{{MediaPath}}"
            ],
            "timeoutSeconds": 600
          }
        ]
      }
    }
  }
}
```

Notes:

- replace `RICH_USERNAME` with the actual macOS username for the OpenClaw account
- the important part is **using `pixi run -m ... -x whisper`**
- this is the detail that fixed transcription for us

After updating config, restart OpenClaw and test by sending the bot a short Telegram voice note.

## 8. Stopping it when he wants the laptop “back”

If he wants the machine to behave like a normal laptop for a while, the simplest option is to stop the OpenClaw gateway:

```bash
openclaw gateway stop
```

And later:

```bash
openclaw gateway start
```

This is effectively the “stasis” mode.

## Suggested operating pattern

### Low-friction option

- normal day-to-day computing happens in the brother's usual macOS user
- OpenClaw lives in the separate `openclaw` user
- when needed, fast user switch into the OpenClaw user
- stop the gateway when OpenClaw should be inactive

This is probably the best first version.

## Security checklist

- [ ] dedicated **non-admin** macOS user created
- [ ] Pixi installed in that user only
- [ ] Node installed via Pixi in that user only
- [ ] OpenClaw installed/configured in that user only
- [ ] no passwordless sudo for the OpenClaw user
- [ ] no unnecessary shared credentials between users
- [ ] FileVault enabled on the Mac
- [ ] macOS auto-updates enabled
- [ ] OpenClaw stopped when not needed

## Open questions / things to decide

1. **Launch style**
   - manual start/stop only?
   - or install the gateway as a per-user service/login item?
2. **How isolated should this really be?**
   - separate user may be enough
   - if the risk tolerance is lower, a separate Mac mini / VM might be the better call
3. **How much access should OpenClaw have?**
   - email?
   - GitHub?
   - Telegram only?
   - browser control?
4. **How polished should voice transcription be?**
   - just working locally via Whisper?
   - or turn it into a cleaner reusable template/script later?

## Provisional recommendation

If the goal is “practical and not too annoying”, start here:

1. separate **standard** macOS user
2. install Pixi there
3. install Node there
4. install OpenClaw there
5. keep it non-admin
6. stop the gateway when not in use

If later it feels too exposed, move to a dedicated machine or a VM.
