# Cleverpage for Claude, always current

Build, publish and run client websites with Claude. Cleverpage hosts the website under its own domain, handles its forms and gives your client an editor for texts, images and news.

## What the plugin does

It adds one skill, `cleverpage`. The skill applies when you mention Cleverpage or a website you build or run there, and when a website or draft you built with Claude should go online or be handed to a client. Claude then reads the current manual of the Cleverpage API at https://app.cleverpage.ai/api/v1/manual and works by it: it sets up the design system, builds the components, creates the pages, uploads your images and films, and puts finished pages on staging. Going live happens only after your explicit yes.

Because the manual is read from Cleverpage, a new tool is there the day it ships, and nothing in the plugin needs an update for it. That includes images, films and speech made with AI, captions from a film's sound and audio descriptions, paid from your organisation's AI credit and only if you allow it when connecting, with a monthly limit.

The plugin contains this one skill. It has no hooks, no scripts and no MCP server.

## Install

```
/plugin marketplace add cleverpage-ai/skill
/plugin install cleverpage-latest@cleverpage
```

The plugin `cleverpage` in the same marketplace is the version for Anthropic's plugin directory: it carries a copy of the manual, makes nothing with AI and is updated only after a review. Install one of the two, not both.

## Try it

```
Build a website for a physiotherapy practice with Cleverpage.
```

```
I built this website draft with Claude. Put it online with Cleverpage so that my client can change the texts.
```

```
Make captions for the film on my Cleverpage website and show them to me before they go online.
```

## What you need

- A Cleverpage account. Creating one is free, and Claude opens the sign-in page when it connects.
- A terminal Claude may use, with `curl`.

## How it connects

Signing in works like `gh auth login`: Claude shows a short code, and you confirm in the browser. The access key goes straight into `~/.config/cleverpage/token` and never appears in the chat. Keys expire after 90 days and can be revoked in your account.

## What it sends and fetches

Everything goes to one host, `app.cleverpage.ai`: the manual and the building guide, Claude's calls to the API signed with the key from the token file, the OpenAPI schema when Claude needs a tool's exact arguments, and the files of your design that Claude uploads to your website.

## Troubleshooting

- **No browser opened.** Claude shows the address and the code. Open the address yourself.
- **Claude says it has to connect again.** The key expired or was revoked. Let it connect once more.
- **Anything else.** Open an issue at https://github.com/cleverpage-ai/skill/issues.

## Privacy and terms

- Privacy notice: https://app.cleverpage.ai/privacy
- Terms: https://www.cleverpage.ai/agb

## License

MIT
