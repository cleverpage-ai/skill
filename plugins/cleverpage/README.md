# Cleverpage for Claude

Build, change and run client websites with Claude. Cleverpage hosts the website and gives your client an editor for texts, images and news.

## What the plugin does

It adds one skill, `cleverpage`. The skill applies when you mention Cleverpage or a website you build or run there. Claude then works through the Cleverpage API: it sets up the design system, builds the components, creates the pages, uploads your images and films, and puts finished pages on staging. Going live happens only after your explicit yes.

The plugin contains the skill, the manual of the Cleverpage API and the building guide for components, each as of the API version named in it. It has no hooks, no scripts and no MCP server.

## Install

```
/plugin marketplace add cleverpage-ai/skill
/plugin install cleverpage@cleverpage
```

## Try it

```
Build a website for a physiotherapy practice with Cleverpage.
```

```
Add a page "Team" to my Cleverpage website and put it in the menu.
```

```
Which pages of my Cleverpage website have changes that are not approved yet? Put them on staging.
```

## What you need

- A Cleverpage account. Creating one is free, and Claude opens the sign-in page when it connects.
- A terminal Claude may use, with `curl`.

## How it connects

Signing in works like `gh auth login`: Claude shows a short code, and you confirm in the browser. The access key goes straight into `~/.config/cleverpage/token` and never appears in the chat. Keys expire after 90 days and can be revoked in your account.

## What it sends and fetches

Everything goes to one host, `app.cleverpage.ai`:

- Claude's calls to the API, signed with the key from the token file. Each call names the version of the manual the plugin carries.
- The OpenAPI schema, when Claude needs a tool's exact arguments.
- The images, films and other files of your design, which Claude uploads to your website. Images your design names by address are fetched from where they lie.

The plugin downloads no program and takes no instructions from a server. It makes no images, videos or audio with AI. Claude works with the files you give it and with your website's media library.

## Troubleshooting

- **Claude says the plugin has an update.** The API is ahead of the manual in the plugin. Update the plugin under `/plugin`, Installed. The older copy keeps working until then.
- **No browser opened.** Claude shows the address and the code. Open the address yourself.
- **Claude says it has to connect again.** The key expired or was revoked. Let it connect once more.
- **Anything else.** Open an issue at https://github.com/cleverpage-ai/skill/issues.

## Privacy and terms

- Privacy notice: https://app.cleverpage.ai/privacy
- Terms: https://www.cleverpage.ai/agb

## License

MIT
