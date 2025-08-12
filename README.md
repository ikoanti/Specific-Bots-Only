# Specific-Bots-Only — Strict robots.txt Allowlist Templates

Production-ready `robots.txt` templates that **allow only specific, documented crawlers** and **block everything else**. Use these when you want tight control over who can crawl your site.

> ⚠️ **Heads-up:** `robots.txt` is advisory. Many good bots follow it; some bad ones don’t. For real enforcement, pair this with WAF rules, rate-limiting, IP allowlists, and (when needed) reverse-DNS verification for Google/Bing.

---

## What’s in this repo

- `robots.search-only.txt` — classic search engines only  
- `robots.search+social.txt` — search + social preview bots  
- `robots.search+social+ai.txt` — search + social + AI/LLM controls (block training crawlers)

Each template ends with a catch-all **deny**:
```txt
User-agent: *
Disallow: /
````

Add your `Sitemap:` line(s) at the bottom of whichever template you use.

---

## Quick start

1. Pick a template below.
2. Replace the `Sitemap:` URL(s) with your real sitemap location(s).
3. Save it as `/robots.txt` at your site root (e.g., `https://example.com/robots.txt`).
4. Test with Google/Bing robots testers and verify expected hits in your logs.

---

## Templates

### 1) Search-only (strict)

```txt
# robots.search-only.txt — Allowlist (Search only)
# Updated: 2025-08-12

###########
# Search  #
###########

# Google (Search + media + general fetchers)
User-agent: Googlebot
Allow: /
User-agent: Googlebot-Image
Allow: /
User-agent: Googlebot-Video
Allow: /
User-agent: GoogleOther
Allow: /

# Microsoft Bing
User-agent: Bingbot
Allow: /
User-agent: AdIdxBot
Allow: /

# Apple
User-agent: Applebot
Allow: /

# DuckDuckGo
User-agent: DuckDuckBot
Allow: /

# (Optional regionals — uncomment if you want them)
# User-agent: YandexBot
# Allow: /
# User-agent: Baiduspider
# Allow: /
# User-agent: Slurp
# Allow: /

########################
# Block all else       #
########################
User-agent: *
Disallow: /

# Sitemaps (replace with yours)
Sitemap: https://www.example.com/sitemap.xml
```

---

### 2) Search + Social previews

```txt
# robots.search+social.txt — Allowlist (Search + Social previews)
# Updated: 2025-08-12

###########
# Search  #
###########

User-agent: Googlebot
Allow: /
User-agent: Googlebot-Image
Allow: /
User-agent: Googlebot-Video
Allow: /
User-agent: GoogleOther
Allow: /

User-agent: Bingbot
Allow: /
User-agent: AdIdxBot
Allow: /

User-agent: Applebot
Allow: /

User-agent: DuckDuckBot
Allow: /

# (Optional regionals — uncomment if desired)
# User-agent: YandexBot
# Allow: /
# User-agent: Baiduspider
# Allow: /
# User-agent: Slurp
# Allow: /

########################
# Social link previews #
########################

# Facebook & Instagram
User-agent: facebookexternalhit
Allow: /
User-agent: Facebot
Allow: /

# X (Twitter)
User-agent: Twitterbot
Allow: /

# LinkedIn
User-agent: LinkedInBot
Allow: /

# Slack
User-agent: Slackbot
Allow: /

# Telegram
User-agent: TelegramBot
Allow: /

# Discord
User-agent: Discordbot
Allow: /

# Skype
User-agent: SkypeUriPreview
Allow: /

# Pinterest (Rich Pins)
User-agent: Pinterestbot
Allow: /

# WhatsApp (link previews)
User-agent: WhatsApp
Allow: /

########################
# Block all else       #
########################
User-agent: *
Disallow: /

# Sitemaps (replace with yours)
Sitemap: https://www.example.com/sitemap.xml
```

---

### 3) Search + Social + AI/LLM controls (full)

```txt
# robots.search+social+ai.txt — Allowlist (Search + Social + AI/LLM)
# Updated: 2025-08-12

###########
# Search  #
###########

User-agent: Googlebot
Allow: /
User-agent: Googlebot-Image
Allow: /
User-agent: Googlebot-Video
Allow: /
User-agent: GoogleOther
Allow: /

User-agent: Bingbot
Allow: /
User-agent: AdIdxBot
Allow: /

User-agent: Applebot
Allow: /

User-agent: DuckDuckBot
Allow: /

# (Optional regionals — uncomment if desired)
# User-agent: YandexBot
# Allow: /
# User-agent: Baiduspider
# Allow: /
# User-agent: Slurp
# Allow: /

########################
# Social link previews #
########################

User-agent: facebookexternalhit
Allow: /
User-agent: Facebot
Allow: /
User-agent: Twitterbot
Allow: /
User-agent: LinkedInBot
Allow: /
User-agent: Slackbot
Allow: /
User-agent: TelegramBot
Allow: /
User-agent: Discordbot
Allow: /
User-agent: SkypeUriPreview
Allow: /
User-agent: Pinterestbot
Allow: /
User-agent: WhatsApp
Allow: /

#####################
# AI / LLM crawlers #
#####################

# Allow AI search & assistant retrieval (optional)
# (Lets assistants link to you in results; these are not used for model training.)
User-agent: OAI-SearchBot
User-agent: ChatGPT-User
User-agent: PerplexityBot
User-agent: PhindBot
User-agent: YouBot
Allow: /

# Block AI training / large-scale data collection
User-agent: GPTBot
User-agent: CCBot
User-agent: Google-Extended
User-agent: Applebot-Extended
User-agent: ClaudeBot
User-agent: Claude-SearchBot
User-agent: Meta-ExternalAgent
User-agent: Meta-ExternalFetcher
User-agent: Bytespider
Disallow: /

# Optional: also block user-triggered fetchers
# (These fetch when a user asks an assistant to open your page.)
# User-agent: Claude-User
# User-agent: Perplexity-User
# Disallow: /

########################
# Block all else       #
########################
User-agent: *
Disallow: /

# Sitemaps (replace with yours)
Sitemap: https://www.example.com/sitemap.xml
```

---

## Enforcement examples (optional but recommended)

**Cloudflare WAF — allow Verified Bots, challenge the rest**

```
Expression: not cf.client.bot
Action: Managed Challenge (or Block)
```

**Apache (.htaccess) — allow only known UAs (use with care; UA spoofing is trivial)**

```apache
SetEnvIfNoCase User-Agent "Googlebot|Bingbot|Applebot|DuckDuckBot" allowed_agent
<RequireAll>
  Require env allowed_agent
</RequireAll>
```

**Nginx — block specific UAs (example)**

```nginx
if ($http_user_agent ~* "(Bytespider|CCBot|ClaudeBot|Meta-ExternalAgent)") {
    return 403;
}
```

> 🔎 Tip: If you must hard-allow Google/Bing, verify real bots via reverse DNS (rDNS) or rely on Cloudflare’s **Verified Bots** signal rather than UA strings alone.

---

## Testing & validation

* Fetch `https://yourdomain.com/robots.txt` in an incognito browser.
* Use **Google Search Console → robots.txt Tester** and **Bing Webmaster Tools → robots.txt Tester**.
* Check server logs (or Analytics) to confirm only the allowed agents are hitting you.

---

## Sources & further reading

* Google Search Central — Robots.txt intro: [https://developers.google.com/search/docs/crawling-indexing/robots/intro](https://developers.google.com/search/docs/crawling-indexing/robots/intro)
* Google — Robots.txt parsing details: [https://developers.google.com/search/docs/crawling-indexing/robots/robots\_txt](https://developers.google.com/search/docs/crawling-indexing/robots/robots_txt)
* Bing Webmaster Tools — robots.txt tester: [https://www.bing.com/webmasters/help/robotstxt-tester-623520ca](https://www.bing.com/webmasters/help/robotstxt-tester-623520ca)
* Apple — About Applebot & Applebot-Extended: [https://support.apple.com/en-us/119829](https://support.apple.com/en-us/119829)
* DuckDuckGo — DuckDuckBot help: [https://duckduckgo.com/duckduckgo-help-pages/results/duckduckbot/](https://duckduckgo.com/duckduckgo-help-pages/results/duckduckbot/)
* OpenAI — GPTBot / bots overview: [https://platform.openai.com/docs/gptbot](https://platform.openai.com/docs/gptbot) , [https://platform.openai.com/docs/bots](https://platform.openai.com/docs/bots)
* Anthropic — Managing Claude crawlers: [https://support.anthropic.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler](https://support.anthropic.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler)
* Perplexity — Crawler docs: [https://docs.perplexity.ai/guides/bots](https://docs.perplexity.ai/guides/bots)
* Common Crawl — CCBot: [https://commoncrawl.org/ccbot](https://commoncrawl.org/ccbot)
* Meta (Facebook) — Web crawlers & user agents: [https://developers.facebook.com/docs/sharing/webmasters/web-crawlers/](https://developers.facebook.com/docs/sharing/webmasters/web-crawlers/)
* Cloudflare — Allow traffic from verified bots: [https://developers.cloudflare.com/waf/custom-rules/use-cases/allow-traffic-from-verified-bots/](https://developers.cloudflare.com/waf/custom-rules/use-cases/allow-traffic-from-verified-bots/)
* Cloudflare — Verified bots overview: [https://developers.cloudflare.com/bots/concepts/bot/verified-bots/](https://developers.cloudflare.com/bots/concepts/bot/verified-bots/)

---

## Changelog

* **2025-08-12**:  README with three templates, AI/LLM controls, and enforcement examples.

## License

MIT — see `LICENSE`.

