# Cleverpage skill for AI agents

[Deutsche Fassung](README.de.md)

Your AI builds the client website. Cleverpage hosts it and gives your client an editor for changing texts, images and news on their own, without you and without AI.

This repository teaches Claude Code, Cursor, Codex, Gemini CLI and other agents with a terminal how to work with [Cleverpage](https://www.cleverpage.ai).

<!-- The screen recording goes here: prompt, confirmation in the browser, live build progress, finished page in the editor -->

## The problem this solves

Building a website with an AI agent takes an afternoon. Then the client calls: new opening hours, a new colleague, a job posting. Now you either change code for every small thing, or you bolt a CMS onto a site that was never meant to have one. And with the next client it all starts over with a different setup.

With Cleverpage, the agent builds the site in a system that already comes with the editor, hosting, forms and approvals. Every website you look after lives in the same place and works the same way.

## Install

### Claude Code

```
/plugin marketplace add cleverpage-ai/skill
/plugin install cleverpage@cleverpage
```

Or without the plugin system, as a single file:

```bash
mkdir -p ~/.claude/skills/cleverpage
curl -s https://app.cleverpage.ai/api/v1/skill -o ~/.claude/skills/cleverpage/SKILL.md
```

### Cursor, Codex, Gemini CLI and others

Copy the matching file from this repository into your project:

| Agent | File |
|---|---|
| Codex and other agents that read AGENTS.md | `AGENTS.md` |
| Cursor | `.cursor/rules/cleverpage.mdc` |
| Gemini CLI | `GEMINI.md` |

### Without installing anything

Paste this into any agent that may run commands in a terminal:

```
Connect to Cleverpage.
If your environment asks whether you may reach app.cleverpage.ai, tell me in
one sentence that you build my website through it and need only this address.
Read the manual first: https://app.cleverpage.ai/api/v1/manual
Follow it step by step; I will confirm in the browser.
```

## Use

Say what you want:

```
Build a website for a physiotherapy practice with Cleverpage.
```

What happens next:

1. The agent reads the current manual from Cleverpage.
2. Your browser opens. You sign in or create a free account and click "Connect". No password, no API key in the chat.
3. The agent asks what you bring: a design, screenshots, an existing website, an idea or nothing.
4. It builds the design system, the components and the pages. You watch the progress live in your Cleverpage account.
5. You get three links: the editor, the working state and the staging site behind a password.

The website goes live only once you activate it, connect a domain and say yes.

## What your client gets

- **An editor that fits exactly this website.** Every component has typed fields. So the editor shows exactly what can be changed: texts, images, lists, links, videos. Your client cannot break the layout, because design and content are separate.
- **Collections** for news, events, jobs or anything else that repeats, with a page of its own per entry if you want one.
- **Forms** with mail delivery, webhooks, spam protection without third parties and an inbox in Cleverpage.
- **Approvals.** Editors change, approvers publish. Before and after are visible for every page.
- **Change requests.** If your client needs something the components do not offer, the request reaches you, and your agent fulfils it later.
- **Languages, redirects, navigation, statistics without cookies.**

## What you get

- **One system for all client websites** instead of a different setup per project.
- **A design system that holds.** Components may only use colours, fonts and spacing from the design system. The server refuses everything else, names every defect and saves nothing until it is right.
- **Static HTML** from a fast rendering core, delivered through a CDN.
- **Working state, staging and live** as separate states, with the option to roll back.
- **No lock-in.** The complete website source with pages, components, design system and media can be exported as a ZIP at any time.

## How the connection works

The agent talks to a plain HTTP API. There is no package to install and no MCP server to set up.

- Signing in works like `gh auth login`: the agent shows a short code, you confirm in the browser.
- The access key goes straight into `~/.config/cleverpage/token`. It never appears in the chat.
- Keys expire after 90 days and can be revoked in your account.
- Approving for staging and going live are separate steps. Live happens only with your explicit yes.
- The AI may spend credits on generated images or videos only if you allow it when connecting, with a monthly limit.

The skill itself is only a pointer to the manual on the server. That is why it does not go stale: your agent always reads the current rules.

## What Cleverpage is not

- **Not a host for existing projects.** A finished React, Astro or Lovable site cannot be imported. Your agent can rebuild an existing website in Cleverpage, and for content pages that works well, but it remains a rebuild.
- **Not an app platform.** Cleverpage delivers websites with editable content, collections and forms. Logins, user databases and backends of your own are not part of it.
- **Not for chat-only AIs.** The agent must be able to run commands in a terminal (`curl` or Python 3).

## Pricing

- The account is free. Building, preview and staging cost nothing.
- You pay per website, and only from the moment you activate it.
- Introductory price: **€6.90 a month** per website, or **€3.45 a month** for small websites of up to three pages.
- The introductory price stays for every website you create now.

Current prices are always on [cleverpage.ai](https://www.cleverpage.ai).

## Reference

- Manual for agents: https://app.cleverpage.ai/api/v1/manual
- OpenAPI schema: https://app.cleverpage.ai/api/v1/openapi.json
- Command line (a single Python file): https://app.cleverpage.ai/api/v1/cli
- API changes: https://app.cleverpage.ai/api/v1/changelog

## Feedback

Something unclear or broken? Open an issue here. Your agent can also report problems directly through the API; the manual tells it how.

## License

MIT
