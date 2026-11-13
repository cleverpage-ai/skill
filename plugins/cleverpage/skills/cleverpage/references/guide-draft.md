# Translating a draft into components

A draft shows **one** state of a page. A component set is the set of
building blocks from which many such states can arise. Between the two
lies work that is only partly mechanical — the rest are decisions that
belong to the agency, not to you.

This guide is derived from two real translations: a dark, four-page
furniture store and a light, single-page app site. Where it says
"held both times" below, it means exactly that.

## The principle

**You propose, the agency decides.** Do not translate silently all
the way through. The split, the roles, and above all the names are the
product the agency later sells — if it was not in the room for those,
it will not trust the result, and rightly so.

So between reading and submitting, present **one** proposal: a table
of the building blocks, what the editor may do per block, and the two
or three places where you guessed. Not more — a list of thirty
questions is not a proposal, it is an imposition.

## What you need before you start

The draft, in whatever form the person has it — and no second form
when the first one is complete. A Claude Design standalone HTML is one
file with the page, every image, the video and the fonts packed inside
(the manual says how to unpack it); a ZIP export carries the page
source, with its images as addresses; screenshots with the texts work
too. A link to a Claude artifact (`claude.ai/artifact/…`) opens only for
Claude in the account that owns it; elsewhere, ask once for the
standalone HTML. And the project: an existing one the person names, or
a new one you create — always on the server whose address the person
gave you, never on another one, or she will not find it.

## Order

It is not yours to choose; validation enforces it:

1. **Deliver the images** (the media route, `POST
   /api/v1/projects/<project>/media`)
2. **Fonts** (`adopt_fonts`) and **design system**
   (`submit_design_system_v2`)
3. **Global content** (`submit_global_fields`) — logo, contact line
4. **Types and components** (`submit_types`, then `submit_component`,
   one after the other; header and footer are components of kind
   `layout`)
5. **Layout** (`submit_layout`, parts `header` and `footer` with
   `instances`)
6. **Pages** (`submit_page`), with the draft's texts in the props
7. **Look at it**, then publish once

Fixed colours in component CSS are rejected, and so is `var(--x)` on a
variable that does not exist. Without a design system, no component
gets through. That is intentional: it forces the design language to be
settled before anything gets built.

## 1 · Read everything first

Read **all** pages of the draft before you cut anything. Both times
the right split only became visible once everything was on the table:
four hero versions on four pages are one component with variants, not
four components. From the first page alone you would not have seen
that.

## 2 · Derive the design system

Take the values that **repeat**. A value that occurs exactly once is
not yet a token. `read_design_system_v2` shows the structure; submit
only what differs; everything you leave out keeps its active value.

- **Colours are worlds.** One base colour per world (`primary`,
  `secondary`, `accent`, `success`, `warning`, `error`, `info`,
  `neutral`); the 12-step scale and the roles (`--color-primary-bg`,
  `-text`, `-border`, `-bg-subtle` …) are derived. The draft's brand
  colour is usually `primary`, its greys `neutral`, its paper and ink
  the surfaces and text of the default context.
- **Fonts are served by the site itself.** `adopt_fonts` lays a
  family into the site and answers the entries for
  `typography.fonts`. A `<link>` to fonts.googleapis.com would send
  each visitor's IP address to a third party; the gate refuses it.
- **What the draft does to a primitive everywhere** — ink-coloured
  buttons, an outline button with an ink border, a badge in green —
  goes into `primitives.<primitive>.styling.<variant>` as
  `{"css": "background:var(--color-text);color:var(--color-surface)"}`
  (with `<variant>:hover` beside it), or into
  `primitives.<primitive>.custom-variants.<name>`. Only colour roles
  and the design system's other names are allowed there.
- **What one component does differently** — an overline in the brand
  colour, a hairline under a row — stays in that component's CSS,
  bound to a role: `color: var(--color-primary-text)`.

Two traps from practice:

- **Transparent variants of a colour** are not a token of their own.
  `rgb(243 242 239 / .14)` is the text colour at 14% — write
  `color-mix(in srgb, var(--color-text) 14%, transparent)`. That
  way the colour keeps a single source.
- **Half-tones that arose in the draft by accident.** `#6f7276` and
  `#7f8288` next to each other are almost certainly the same grey in
  two spellings. Merge them and ask the agency.

## 3 · Cutting

**Top-level `<section>` is the default cut.** Held both times. What
sits above it (header) and below it (footer) is layout: a component of
kind `layout`, placed once with `submit_layout` (`part` `header` or
`footer`, `instances` like page content). It appears on every page and
keeps its props, so the editor changes the menu or the button text
there.

A logo or the contact line belongs in the global fields
(`submit_global_fields`) and is read as `{{ site.* }}`. If you instead
rebuild the logo as inline SVG or write the legal links as fixed
paths, that does not survive the client's first change.

A building block is too big if the editor might need only part of it.
It is too small if it never occurs on its own.

## 4 · Recognizing variants

Two sections that are **structurally** identical and differ only in
the presence or arrangement of parts are one building block with a
`variante`. The variant drives a class; the CSS does the rest.

An example that worked: the four hero versions became `page-hero`
with `variante: video-voll | bild | nur-text`.

An example where I decided **against** merging: the hero and the
closing call-to-action of the app page are structurally almost
identical — but the closing one sits centered on a colored surface
and carries no feature pills and no device image. A shared component
would have needed three variants, two of which occur only once. Rule
of thumb: **a variant pays off from two real uses.**

That is exactly one of the places that belong in the proposal.

## 5 · Assigning roles

Every prop gets a `role`. The ground rule that carried both times:

| | |
|---|---|
| **`content`** | Visible texts, images, lists — everything that changes without the page *looking* different |
| **`design`** | A choice between states you have designed. Always a `select` with fixed options, never a free field |
Global content — logo, legal links, contact line — you create with
`submit_global_fields`. It carries the same contract as a prop
(`type`, `label`, `description`) plus the `wert` (its value), and it
is the only thing the layout can read: `{{ site.<name> }}`.

When in doubt, `content`. Too much locking annoys daily; too little
locking is noticed once and corrected.

## 6 · Naming

The names are what the editor sees later. `abschnitt-3` is worthless.
Name by the **role in the text**, not by the looks: `ablauf` instead
of `drei-karten`, `zusage-block` instead of `teal-banner`.

Propose names — but say that they are open to change. The agency
knows its clients and their language.

## 7 · Separating content

The draft contains real texts. They belong in the **page**, neither in
the template nor in the component's `default`:

```
Template:  <h1 class="held__titel">{{ ueberschrift }}</h1>
Page:      {"component": "held", "props": {"ueberschrift": "Nie wieder Etiketten"}}
```

That is the moment the picture becomes a component set. Leave no text
in the template that someone might want to change — otherwise the
editor has to file a request for it. A `default` is what a new
insertion starts with; Cleverpage copies the defaults into an entry
that leaves a prop out, so the page holds its own text and does not
change when someone later edits the component.

**Images likewise.** They go into the media library, and the page
knows only their ID. Deliver them in one batch before you build:

```
curl -s -H "Authorization: Bearer <token>" \
     -F file=@uploads/hero.jpg -F file=@uploads/team.jpg \
     https://app.cleverpage.ai/api/v1/projects/<project>/media
→ { "media": [{ "id": "m-1a2b3c4d", … }, …] }
```

You set the ID as the value of an `image` prop; in the template,
`{{ bild | image }}` yields the source and `{{ bild | image_alt }}`
the alt text. A draft without photos still gets image props: the
editor fills them later. Never send an image as base64 through your
context.

## 8 · Motion

Everything the draft shows in scroll effects belongs in the
component's CSS:

```
Motion = driver × range × progression
         animation-timeline / animation-range / @keyframes
```

The hidden state belongs in the `@keyframes` (`from`), **never** in
the base rule — otherwise the content is simply gone in browsers
without scroll timelines. Validation rejects that.

## 9 · Interaction

First the platform, then an island. There is nothing in between.

The app page had a modal with state in the draft — and still needed
no island: `command="show-modal" commandfor="…"` does it
declaratively. The finished page carries not a single script of its
own.

So always ask first: can `<details>`, `<dialog>`, `popover`, invoker
commands, scroll snap do it? Filter buttons over a menu are platform
too: radio inputs named after `{{ instanz }}` and
`:has(…:checked)` (see the component guide). An island is for state,
network, and backend — the furniture store's lightbox is one, because
it has to remember which image is open.

## 10 · Show that it is right

After submitting: the built page next to the draft. Every accepted
`submit_page` answers `links.preview`, the running preview of the
working state, and `site_password`, which it asks for once; open it,
take a screenshot at desktop and phone width, and compare it with the
draft section by section. Name the password to the person in the chat
when you hand the website over: no link opens it without. That is the only
question the agency really asks. Answer it before it is asked.

## When another draft page arrives

Do not retranslate. First `list_components`, then check how far the
existing building blocks carry. The right sentence is:

> Page 2 can be built to 80% with your components. For the rest I
> need a new building block — or a variant of `page-hero`.

That makes it the same process as a request from loop 3, only
initiated by the agency. There should be only **one** way building
blocks come into being.

## When the draft changes afterwards

No back-sync. The components have schemas, roles, and variants the
draft knows nothing about — a sync would destroy them on every run.
Adopt selectively: a component's markup, while the schema stays.
`read_history` and `revert_component` cover the way back.

## Mistakes I made

So they do not happen again:

- **Thought of the design system too late.** Both times, tokens were
  missing for things that only surfaced while building — media
  scrims, placeholder surfaces, muted text, text on accent surfaces.
  Collect them while reading, not while fixing validation errors.
- **Left draft text in the template.** Only shows up when the editor
  wants to change it and cannot.
- **Guessed the loop fields.** They are `forloop.index`,
  `forloop.length`, `forloop.first`, `forloop.last` — the German
  spellings from older components still work.
- **Referenced a foreign timeline without declaring it.** If it is
  missing on a page, the motion stays silently off. Choose the base
  state so that it is correct without the other component.

## What you tell the agency at the end

Brief. The links first (`links` in the answers: the project in the
studio, the preview, the website after publishing), then what came
into being, what the editor can do alone from now on, and what stays
deliberately locked — with the reason. Every place where the result
differs from the draft, with the reason; "the contract wants it" is
only a reason if you can name the rule. Not the file list.
