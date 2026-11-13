---
name: cleverpage
description: Build, change or continue a website with Cleverpage. Use whenever the person mentions Cleverpage, or a website she builds or runs there (Website, Webseite, Projekt).
---

# Cleverpage

1. Everything goes through app.cleverpage.ai. If your environment asks whether you
   may reach it, tell her in one sentence that you build her website
   through it and need only this address.
2. Read [references/manual.md](references/manual.md) completely before the
   first call. It came with this skill and describes contract 1.20.8 (2026-11-13);
   work by it, not by what you remember from earlier sessions.
3. The token lies in ~/.config/cleverpage/token. Check it with `GET https://app.cleverpage.ai/api/v1/me` as the
   manual shows; never print it. Without the file, or on 401, connect as
   the manual says under "Connecting".
4. Once connected, tell her as whom and in which organisation and list her
   websites. Ask whether she wants a new website or one of these, unless
   she has said so already. Nothing about how you connected. Publish only
   after her explicit yes.

## What lies beside this file

The manual and the building guide of the Cleverpage API, contract 1.20.8 (2026-11-13).
You work by these files; none of them is fetched from the server.

- [references/manual.md](references/manual.md): connecting, signing a call, the order of the work,
  how to talk to her, the answers, the tools.
- [references/guide-component.md](references/guide-component.md): Builds components for a Cleverpage project. Use as soon as the task involves a new component, a change to an existing one, or the components of a client project.
- [references/guide-draft.md](references/guide-draft.md): Translates a design draft (Claude Design, HTML/CSS) into the components of a Cleverpage project. Use when a project is set up fresh or an additional draft page is added.

The manual says how you name this copy's version in your calls and what
you tell her when a newer plugin is out.
