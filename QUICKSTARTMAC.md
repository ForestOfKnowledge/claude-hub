# 🚀 Quick Start Guide (macOS Edition)

Get Claude responding to your GitHub issues in minutes using Cloudflare Tunnel.

---

## Prerequisites

* GitHub account
* Docker installed (Install via [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop/))
* Claude.ai Max plan (5x or 20x)
* Cloudflare account (free tier works)

---

## Step 1: Create a GitHub Bot Account

1. Log out of GitHub and create a new account for your bot (e.g., `YourProjectBot`)
2. Create a [Personal Access Token](https://github.com/settings/tokens) with `repo` and `write` permissions
3. Add the bot as a collaborator to your repos from your main GitHub account

---

## Step 2: Clone and Configure

```bash
# Clone the repo
git clone https://github.com/claude-did-this/claude-hub.git
cd claude-hub

# Copy and configure env file
cp .env.quickstart .env
nano .env  # or use TextEdit or VSCode if you prefer a GUI editor
```

Edit these variables:

* `GITHUB_TOKEN`: Bot account's personal token
* `GITHUB_WEBHOOK_SECRET`: Run `openssl rand -hex 32` to generate
* `BOT_USERNAME`: e.g. `@YourProjectBot`
* `BOT_EMAIL`: Your bot's email
* `AUTHORIZED_USERS`: Comma-separated GitHub usernames allowed to use the bot

---

## Step 3: Authenticate Claude

```bash
# Run the interactive Claude setup script
./scripts/setup/setup-claude-interactive.sh
```

This will open a browser window for Claude.ai login and store credentials.

---

## Step 4: Start the Service

```bash
docker compose up -d
docker compose logs -f webhook
```

---

## Step 5: Install Cloudflare Tunnel (macOS)

### Option A: Homebrew (recommended for macOS)

```bash
brew install cloudflare/cloudflare/cloudflared
```

### Option B: Manual Download

Visit [https://developers.cloudflare.com/cloudflared/install/](https://developers.cloudflare.com/cloudflared/install/)
Or run:

```bash
curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-darwin-amd64.tgz | tar xvz
sudo mv cloudflared /usr/local/bin/
```

Make it executable:

```bash
chmod +x /usr/local/bin/cloudflared
```

---

## Step 6: Create Tunnel

```bash
cloudflared tunnel --url http://localhost:3002
```

Copy the resulting URL (e.g., `https://abc123.trycloudflare.com`)

---

## Step 7: Configure GitHub Webhook

In your repo:

1. **Settings** → **Webhooks** → **Add webhook**
2. **Payload URL**: Your tunnel URL + `/api/webhooks/github`
   *Example*: `https://abc123.trycloudflare.com/api/webhooks/github`
3. **Content type**: `application/json`
4. **Secret**: Same as `GITHUB_WEBHOOK_SECRET`
5. **Events**: Select:

   * Issues
   * Issue comments
   * Pull requests
   * Pull request reviews

---

## ✅ You're Done!

Try it:

```markdown
@YourProjectBot Can you help me understand this codebase?
```

---

## Optional Enhancements

* **Persist tunnel**:

  ```bash
  cloudflared service install
  ```
* **Advanced features**: See `.env.example`
* **Multiple repos**: Add the same webhook

---

## Troubleshooting

**Bot not responding?**

* Check: `docker compose logs webhook`
* Check GitHub → Webhooks → Recent Deliveries
* Ensure user is in `AUTHORIZED_USERS`

**Claude issues?**

* Re-run: `./scripts/setup/setup-claude-interactive.sh`
* Verify you're on Claude Max plan

---

## Community

* [Discord server](https://discord.gg/yb7hwQjTFg)
* [Docs](https://claude-did-this.com/claude-hub/overview)
