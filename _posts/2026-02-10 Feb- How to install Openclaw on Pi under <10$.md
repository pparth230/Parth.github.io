---
layout: post
title: "How to run openclaw on pi under 10$/month"
date: 2026-02-10 21:35:00 +0530
categories: blog
---


# 🔐 Run Your Own AI Agent at Home — Security First

> A security-first guide to running your own AI agent at home. No cloud dependencies. Full control.

Nothing against VPS — I have a VPS setup too. But running things on a Pi or Mac Mini is much easier than managing a VPS (trust me on this one). Pi's native install makes it a little nicer to work with.

**This guide is written for non-techies.** Just follow along, copy-paste, and it should work. One-click deploy marketplace solutions exist, but security is my biggest concern — so we're doing this the right way.

Grab a big cup of coffee ☕ — this is a thorough guide.

---

## ⚠️ Is It Completely Safe?

No AI system or model is completely safe to run. That said, security hardening protocols significantly help **reduce the blast radius**.

**What's blast radius?** If something goes wrong, following these protocols limits the damage your AI agent can do to you.

---

## 🤔 Why Security First?

OpenClaw is a powerful tool — you can literally build a website from your phone. But that also means giving it access to your **private files, system logins, and key security details**. Without proper security, things can go seriously south.

---

## 💰 How Much Will This Cost?

| Item | Cost |
|---|---|
| Raspberry Pi 4B or 5 (4GB RAM) | ~$70 *(skip if you already have one)* |
| GLM 4.7 model — Lite plan (3x usage of Claude Code) | $3/month |
| Kimi 2.5 *(faster & better than GLM)* | $20/month |

> Using Claude will cost at least **10x more**. It's fast, but for non-techies both GLM and Kimi 2.5 work just fine.

That's it. Those are your only costs. Everything else depends on custom skills or different language models.

---

## 🚀 Let's Dive In

---

### 1. 🔑 Creating SSH Keys on Mac for Raspberry Pi Access

**What is SSH?**
Think of SSH keys like a special password that's really hard to guess. Instead of typing a password every time you connect to your Pi, your computer proves its identity automatically.

This lets you control your Pi **headless** — no screen or keyboard needed.

**Why it's more secure:**
- No password to forget or steal
- Can't be brute-forced
- Each device gets its own unique key

**The Two-Key System**

When you create SSH keys, you get two files:
- **Private key** — stays on your Mac *(keep this safe — it gives remote access to your Pi)*
- **Public key** — goes on your Raspberry Pi

Think of it like a lock and key: the Pi has the lock (public key), your Mac has the key (private key).

**Create your SSH key:**

```shell
ssh-keygen -t ed25519 -C "raspberry-pi"
```

Just hit Enter through the prompts — the key will be stored in its default location.

**Get your public key:**

```shell
cat ~/.ssh/id_ed25519.pub
```

This outputs something like:

```
ssh-ed25519 AAA23423sfdafsdfTE5AAAAIJw... raspberry-pi
```

Copy this key and paste it into your SSH setup when installing Raspberry Pi OS.

---

### 2. 🍓 Setting Up Raspberry Pi SSH with Your Laptop

This assumes you've already installed Raspberry Pi OS onto an SD card with SSH enabled.

**The only key thing:** enable SSH and paste your **public key** instead of a password. This removes the hassle of storing any password.

---

### 3. 🌐 Connecting Your Devices with Tailscale

**What is Tailscale?**

Imagine creating a private internet just for your devices. That's Tailscale.

| Normal Internet | With Tailscale |
|---|---|
| Your Pi and Mac talk through your router, ISP, and the public internet | Your devices create an encrypted tunnel directly to each other |
| Anyone could potentially intercept | Like a private network, even across different locations |

> ⚠️ **Tailscale is your first line of defense. Do not skip this step.**

**Why you need it:**
- Access your Pi from anywhere (work, vacation — anywhere with internet)
- Everything is encrypted automatically
- No complicated router settings
- Works even behind firewalls

**Step 1 — Create a Tailscale Account** *(totally free for personal use)*

Go to [tailscale.com](https://tailscale.com), click **"Get Started"** and sign up.

> Free for up to 3 devices — if you're only working with Mac and Pi, you're good.

**Step 2 — Install Tailscale on Your Raspberry Pi**

From your Mac, SSH into your Pi first:

```shell
ssh yourusername@yourpi.local
```

Install Tailscale:

```shell
curl -fsSL https://tailscale.com/install.sh | sh
```

Connect your Pi to Tailscale:

```shell
sudo tailscale up
```

You'll see a message like:
```
To authenticate, visit: https://login.tailscale.com/a/abc123xyz
```

Follow the link, log in — and your Pi will appear with a green signal in your Tailscale dashboard. 🎉

**Step 3 — Install Tailscale on Your Mac**

Go to [tailscale.com/download](https://tailscale.com/download), download for macOS, drag to Applications, open it, and log in with the same account.

**Both devices are now talking through a secured channel.**

**Connecting via Tailscale IP:**

Instead of using `yourpi.local`, use your Tailscale IP:

```shell
ssh yourusername@100.xx.xx.xx
```

**How to find your Tailscale IP:**
- Check your Tailscale dashboard in the browser
- Or from your Pi terminal: `tailscale ip -4`

> 💡 Make sure both devices run `sudo tailscale up` when powered on to stay connected.

---

### 4. 🦾 Installing OpenClaw

This is actually the simplest part — the onboarding is well designed.

**Prerequisites:**
- Raspberry Pi with internet connection
- SSH access via Tailscale (both Mac and Pi connected)

**Step 1 — Install OpenClaw:**

```shell
curl -fsSL https://openclaw.ai/install.sh | bash
```

**Step 2 — Complete the Onboarding Wizard**

The install script automatically starts the wizard. Configure the following:

| Setting | Recommended Value |
|---|---|
| Gateway mode | `local` |
| Workspace directory | Default location |
| Model authentication | Your AI model API key (GLM or Kimi) |
| Port | `18789` |
| Bind address | Tailscale IP only |
| Authentication | Token *(recommended)* |
| Gateway Token | Press Enter to auto-generate |
| Tailscale Configuration | Off |

> **GLM models:** [zhipuai.cn](https://open.bigmodel.cn)

---

### 5. 📱 Telegram Bot Setup

**Create your Telegram bot:**

1. Open Telegram on your phone
2. Search for `@BotFather`
3. Send: `/newbot`
4. Follow prompts to name your bot
5. Copy the token BotFather gives you
6. Paste it into the OpenClaw setup

**Back in OpenClaw setup:**

| Setting | Recommended Value |
|---|---|
| Configure DM access policies | Yes |
| Telegram DM policy | **Pairing** *(most secure — you approve each user)* |

> **Policy options:**
> - **Pairing** — you approve each new user ✅ *(recommended)*
> - **Allowlist** — pre-approved list of users
> - **Open** — anyone can message ❌ *(not recommended)*

---

### 6. 🛠️ Adding Skills

Skills are personal to what services you want. This part is also the **most susceptible to prompt injections** — be careful with what you enable.

---

## 🎉 Congratulations!

You now have your own personal AI assistant running securely at home.

---

## 📬 Need Help?

Feel free to reach out with any questions or concerns.

If you're a **founder, agency, or company** needing automation — feel free to DM.

Hope this helps you get started!

---

*Remember: Tailscale is only one of many security hardening protocols. There's more to explore, but this gets you started safely.*
