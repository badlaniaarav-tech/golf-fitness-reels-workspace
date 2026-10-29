# Getting AttentionOS running for your golf and workout reels

This folder is your pre-filled **workspace**: the Brand Kit (voice, lanes, house style, hashtags) and a config with
the editing module switched on. AttentionOS itself runs on your own computer with Docker. It cannot run in the cloud
session that made this folder, because it needs Docker, a database, your Claude login, your footage and your
Instagram/TikTok connection.

## What you need before you start (about 15 minutes to sign up)

| Item | Why | Where |
|---|---|---|
| A Mac or Linux computer (Windows: use WSL 2) with 8 GB RAM and 10 GB free disk | Runs the stack | |
| Docker Desktop (or OrbStack on Mac) | Runs the containers | docker.com |
| Claude Pro or Max plan | Powers the agents on your plan, no API bill | claude.ai |
| Zernio account (optional, needed to post for you) | Publishing, inbox, analytics | zernio.com |
| Apify account (optional) | Watching competitor accounts for outlier posts | apify.com |
| Telegram bot (optional) | Approvals and chat from your phone | Telegram BotFather |

Without Zernio the system still edits your videos; you download the finished MP4 and post it yourself.

## Install (copy and paste, one block at a time)

```bash
export AOS=~/AttentionOS
mkdir -p "$AOS"/{workspace,backups} && chmod 700 "$AOS/backups"
git clone --depth 1 https://github.com/evanbaker315/AttentionOS "$AOS/src"
cd "$AOS"
cp src/deploy/{docker-compose.yml,aos,uninstall.sh} .
cp src/deploy/.env.example .env && chmod 600 .env
```

```bash
cd ~/AttentionOS && perl -pi -e "
  s#^POSTGRES_PASSWORD=.*#POSTGRES_PASSWORD=$(openssl rand -hex 20)#;
  s#^ATTENTIONOS_STUDIO_TOKEN=.*#ATTENTIONOS_STUDIO_TOKEN=$(openssl rand -hex 24)#;
  s#^AOS_SRC_DIR=.*#AOS_SRC_DIR=./src#;
  s#^AOS_STACK_DIR=.*#AOS_STACK_DIR=$AOS#;
" .env
```

Now copy this pre-filled workspace in (the folder this file is in). Adjust the first path to wherever you saved it:

```bash
cp -r /path/to/golf-fitness-workspace/. ~/AttentionOS/workspace/
```

On Linux only:

```bash
sudo chown -R 1000:1000 ~/AttentionOS/workspace
```

Build and start (first build takes 5 to 15 minutes), then sign in to Claude:

```bash
cd ~/AttentionOS && docker compose build --pull attentionos && docker compose up -d --remove-orphans --wait --wait-timeout 900 && ./aos status
```

```bash
cd ~/AttentionOS && ./aos claude-login
```

Open http://localhost:4747. Run `./aos doctor` if anything is off.

## Finish the Brand Kit (10 minutes)

Search for `TODO` in the `brand/` folder and fill in:

- `brand/brand.yaml`: your real handles, your timezone.
- `brand/competitors.yaml`: swap the placeholder handles for 5 to 15 golf and fitness accounts you want to learn from.
- `brand/proof-inventory.yaml`: your real numbers (club head speed, lifts) with a source. Agents only quote numbers listed here.

Studio's onboarding will also interview you and draft `voice.md` from transcripts of your own posts.

## Your first edited reel

1. In Studio open **Scripts**, approve a script (or skip and use **No script, just edit it**).
2. Film vertical on your phone. Talk to camera: say the hook first, redo lines freely, the **last full take wins**.
   Say "cut that" out loud to drop the take before it.
3. Open **Edit** and drop the .mov/.mp4 files on it.
4. It transcribes, cuts dead air and retakes, adds word-highlighted captions, fixes loudness, runs QC.
5. Under **Ready for you**: watch, pin notes on the timeline ("tighten the hook", "no music"), or press **Looks good**
   and then **Schedule** (Zernio) or **Download**.

## One honest limit for golf and workout footage

The cut engine is built around speech: it finds your words, removes pauses and retakes, and captions what you say.
It works best when you talk in the reel (a cue over the swing, a recap over the session). Pure silent swing or lift
montages with music will still render, but the automatic cutting and captions have little to work with. Record a
quick voiceover or talk to camera for the best results.
