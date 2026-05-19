# WMS Doc Agent — Deployment Guide
## Unicommerce Internal Tool · Powered by Claude AI

---

## What's in this package

```
wms-doc-agent/
├── index.html                      ← The doc agent (no API key inside)
├── netlify.toml                    ← Netlify config (iframe permissions)
├── netlify/
│   └── functions/
│       └── claude-proxy.js        ← Serverless proxy (holds API key securely)
└── README.md                       ← This file
```

---

## Deploy to Netlify (10 minutes)

### Step 1 — Create a free Netlify account
Go to https://netlify.com and sign up (free tier is enough).

### Step 2 — Deploy the folder
**Option A — Drag & drop (easiest):**
1. Go to https://app.netlify.com
2. Click "Add new site" → "Deploy manually"
3. Drag the entire `wms-doc-agent/` folder into the upload area
4. Netlify gives you a URL like `https://random-name-123.netlify.app`

**Option B — GitHub (recommended for updates):**
1. Push this folder to a private GitHub repo
2. In Netlify: "Add new site" → "Import from Git" → connect your repo
3. Build settings: leave blank (no build command needed)
4. Deploy — future pushes to GitHub auto-redeploy

### Step 3 — Add your Anthropic API key (CRITICAL)
1. In Netlify dashboard → your site → **Site configuration** → **Environment variables**
2. Click "Add a variable"
3. Key: `ANTHROPIC_API_KEY`
4. Value: your Anthropic API key (get from https://console.anthropic.com)
5. Click Save → go to **Deploys** → click "Trigger deploy" to apply

### Step 4 — Test it
Open your Netlify URL → fill in a feature name → click Generate.
If it works, you'll see a styled HTML document in the preview pane.

### Step 5 — Rename your site (optional)
Netlify dashboard → Site configuration → Change site name → e.g. `wms-doc-agent`
Your URL becomes: `https://wms-doc-agent.netlify.app`

---

## Embed in Confluence

### Option A — Full-page embed (recommended)
1. Create a new Confluence page titled "WMS Document Generator"
2. Click the "+" to insert a macro → search for **iframe** or **HTML**
3. Paste your Netlify URL
4. Set height to `780px`, width `100%`
5. Publish the page → share the Confluence link with your team

### Option B — Confluence page with direct link
If your Confluence doesn't allow iframes:
1. Add a button or link on your Confluence page pointing to the Netlify URL
2. Team clicks the link → opens the agent in a new tab

### Confluence iframe macro settings:
```
URL:    https://your-site.netlify.app
Width:  100%
Height: 780
Border: 0
Scroll: auto
```

---

## Security notes

- **The Anthropic API key never touches the browser.** It lives only in Netlify's environment variables and is used server-side by the proxy function.
- **Usage costs:** Each document generation uses Claude Sonnet. Check https://console.anthropic.com for usage. Consider setting a monthly spend limit.
- **Access control:** The agent URL is public by default. To restrict to your team only, enable Netlify's "Password protection" (paid) or use Netlify Identity.
- **Optional — restrict to your domain:** In `claude-proxy.js`, uncomment the `allowedOrigins` block and add your Confluence domain to only allow calls from your Confluence site.

---

## Updating the agent

If you used GitHub: edit `index.html` → push → Netlify auto-redeploys in ~30 seconds.
If you used drag & drop: drag the updated folder to Netlify again.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| "API key not configured" error | Add `ANTHROPIC_API_KEY` env var in Netlify and redeploy |
| Blank preview after generating | Open browser console → check for errors → likely API key issue |
| Confluence won't show iframe | Use "HTML macro" instead of "iframe macro", or check your Confluence iframe whitelist settings |
| Images not embedding | Ensure images are under 5MB each. Very large images may hit the API token limit. |
| Agent loads but Generate does nothing | Check browser console for CORS errors — ensure `netlify.toml` is included in your deployment |
