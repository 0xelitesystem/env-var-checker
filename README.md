# env-var-checker

Compare your `.env.example` (the committed reference) against your actual `.env`. Find missing variables, undocumented additions, and possible leaked secrets in either file. Browser only.

**Live demo:** https://0xelitesystem.github.io/env-var-checker/

## Use

Open [`index.html`](./index.html). Paste both files. Click Check.

You get:

- Summary counts: vars in each file, missing, undocumented, possible leaks
- **Missing in .env**: variables `.env.example` documents that you haven't set
- **Extra in .env**: variables you've set that aren't in the example (undocumented)
- **Possible leaked secrets in .env.example**: real-shaped values committed where placeholders should be
- **Placeholder values in .env**: values like `your-key-here` left in your real file

## What it detects as a real secret

| Pattern | Example |
|---|---|
| Anthropic API key | `sk-ant-...` |
| OpenAI API key | `sk-...` (40+ chars) |
| GitHub PAT / OAuth | `ghp_...` / `gho_...` / `github_pat_...` |
| Stripe live keys | `sk_live_...`, `rk_live_...` |
| AWS access keys | `AKIA...` plus secret-shape detection |
| Slack tokens | `xoxa-`, `xoxb-`, `xoxp-`, `xoxr-` |
| Google API key | `AIza...` |
| JWTs | `eyJ...` |
| Private key blocks | `-----BEGIN ... PRIVATE KEY-----` |
| MongoDB / Postgres URLs with non-placeholder passwords | `mongodb://user:realPass@...` |
| High-entropy strings in secret-shaped variables | 32+ char base64-shaped values in `*_KEY` / `*_SECRET` / etc. |

## What it detects as a placeholder

`your-...-here`, `xxx`, `<...>`, `${...}`, `changeme`, `example`, `placeholder`, `todo`, `fixme`, `foo`, `bar`, etc.

## Why this exists

Two common vibe-coding failures:

1. **Committing real secrets to .env.example**, a tool generates an example file and uses an actual key from your environment as the placeholder. Now it's in git history.
2. **Leaving placeholder values in .env**, you copy from `.env.example` and forget to update some values. App breaks in production.

This tool catches both classes before you push.

## What this is not

- Not a secret scanner for git history (use git-secrets, gitleaks, or trufflehog)
- Not a substitute for `.gitignore` rules
- Not a vault, don't paste production secrets into anything except your own machine

## Privacy

All parsing is local. No upload, no analytics, no third-party scripts. The values you paste never leave the browser.

If you use the theme toggle, your light or dark choice is saved in your browser's localStorage under the key `theme`. Nothing you paste or type is stored.

## If you find a leaked secret

1. Rotate the credential immediately (the leak doesn't go away just by un-committing)
2. Remove from .env.example
3. Replace with a clear placeholder
4. Use `git filter-repo` or BFG to strip from history if needed (and force-push)
5. Audit logs for usage during the leak window

## Run locally

```
git clone https://github.com/0xelitesystem/env-var-checker
cd env-var-checker
```

Open `index.html` in a browser.

## Contribute

PRs welcome:

- More provider patterns (Cloudflare, Vercel, Sentry, DataDog, Twilio, etc.)
- Better entropy heuristics
- Shannon-entropy scoring for unknown-pattern detection
- Multi-file mode (compare staging vs prod env)

Don't add: external scripts, npm dependencies, telemetry. Single file.

## Build

No build. Single HTML file.

## More

Part of a catalog of single-file browser tools and plain-language references, all MIT licensed and dependency-free: [0xelitesystem.github.io](https://0xelitesystem.github.io/). Built by [elitesystem.ai](https://elitesystem.ai).

## License

MIT.

## Related

- [agent-diff-reviewer](https://github.com/0xelitesystem/agent-diff-reviewer) - flags secrets in diffs
- [secrets-scanner-bookmarklet](https://github.com/0xelitesystem/secrets-scanner-bookmarklet) - scan whole pages
- [byok-security-checklist](https://github.com/0xelitesystem/byok-security-checklist) - bring-your-own-key best practices
