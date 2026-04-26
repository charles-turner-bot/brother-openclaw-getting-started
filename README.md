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

Initial plan:

```bash
pixi global install nodejs
```

Then confirm:

```bash
node --version
npm --version
```

## 4. Install OpenClaw

Install OpenClaw from npm in the dedicated account.

One likely path:

```bash
npm install -g openclaw
```

Then confirm:

```bash
openclaw --version
openclaw status
```

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

## 6. Stopping it when he wants the laptop “back”

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

1. **Pixi global command**
   - confirm exact package name / best install command for Node on macOS
2. **Best OpenClaw install path**
   - `npm install -g openclaw` may be fine, but we should confirm whether a user-local npm prefix is cleaner than a system-global install
3. **Launch style**
   - manual start/stop only?
   - or install the gateway as a per-user service/login item?
4. **How isolated should this really be?**
   - separate user may be enough
   - if the risk tolerance is lower, a separate Mac mini / VM might be the better call
5. **How much access should OpenClaw have?**
   - email?
   - GitHub?
   - Telegram only?
   - browser control?

## Provisional recommendation

If the goal is “practical and not too annoying”, start here:

1. separate **standard** macOS user
2. install Pixi there
3. install Node there
4. install OpenClaw there
5. keep it non-admin
6. stop the gateway when not in use

If later it feels too exposed, move to a dedicated machine or a VM.
