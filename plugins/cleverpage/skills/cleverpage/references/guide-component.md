# Building Cleverpage components

You are building a component for an agency. It will later be filled
with content by editors — people without technical knowledge who must
not be able to break anything. That is exactly why the contract is
tight.

If the task is to turn a **design draft** into a whole component set —
a new project or an additional draft page — then `guide-draft.md` is
the right guide. This one is about the single component.

## Workflow

1. `list_components` — what already exists? Often the solution is an
   existing component, not a new one.
2. `read_component` on a related model — adopt its structure, naming,
   and the tone of its description.
3. `read_design_system_v2` — colours, dimensions, and type come
   exclusively as names from it.
4. Build, and while doing so **`render` repeatedly**: render, inspect,
   correct. This is the same core that later publishes — what is clean
   here is clean everywhere.
5. `submit_component`. Cleverpage checks everything automatically. If it
   rejects, it names every defect — fix, submit again. On rejection
   nothing is written.

## The contract — read this first

A component **may design, but every visible value is a name from the
site's design system**. The gate enforces exactly this, so a style swap
still reaches everything:

- **Layout is free**: display, grid/flex, gap, alignment, width/height,
  padding, margin, position, aspect-ratio, overflow, text-align,
  transform, opacity, transition, animation, container queries. Gap and
  padding take `var(--spacing-stack-N)` and `var(--spacing-section-X)`.
- **Look is bound to a name**: `color`, `background`, `border*`,
  `outline-color`, `fill`, `stroke` take a colour role
  (`var(--color-primary-text)`, `var(--color-text)`,
  `var(--color-border)`, `var(--color-success-bg-subtle)` …);
  `font-family` a font variable (`var(--font-…)`); `font-size` a role
  size (`var(--text-<role>-size)`) or `em`/`%`; `border-radius` a radius
  (`var(--radius-…)`); `box-shadow` a shadow (`var(--shadow-…)`). A
  fixed value (`#B23A24`, `14px` on a font, `Georgia`) is refused
  (`css-forbidden-value`, `css-value-unbound`). An overline in the brand
  colour is therefore one line:
  `.x__overline { color: var(--color-primary-text); }`.
- **What a design shows everywhere belongs in the design system**, not
  in each component: the colours of a button variant, a badge in the
  success colour. `read_design_system_v2` names the colour worlds
  (primary, secondary, accent, success, warning, error, info, neutral)
  and the primitives; a primitive's variant is restyled under
  `primitives.<primitive>.styling.<variant>` with `{"css": "…"}`, and a
  site's own variant under `primitives.<primitive>.custom-variants`; a
  prop names it like a built-in one (`"variant": "ink"`, class
  `p-card--ink`), and a button list offers it as a style of its own.
- **Roles, not steps.** The scales (`--color-neutral-3`,
  `--color-primary-7`) are the design system's stock and refused in a
  component: a step keeps its colour on a dark or tinted surface, a role
  follows it. Surfaces are `--color-surface`, `-surface-elevated`,
  `-surface-lifted`; text `--color-text`, `-text-subtle`, `-text-muted`;
  lines `--color-border`, `-border-subtle`, `-border-strong`; each world
  `--color-<world>-bg`, `-bg-subtle`, `-text`, `-border`.
- **Text takes a role.** A text/richtext prop carries `"textRole": "hero" |
  "headline" | "title" | "lead" | "body" | "label" | "caption" |
  "overline"`, and the element that outputs it carries `class="t-<role>"`.
- **Richtext carries `allow`**: `"inline"` (paragraphs and emphasis) or
  `"prose"` (also headings, lists, quotes, tables). Wrap the output in
  `class="rt"` or `class="rt rt--prose"`.
- **Buttons are always a list of buttons.** `button` is Cleverpage's own type
  (text, target, variant); every button is an entry of a list of it, even
  where the design shows one. The editorial team then adds a second
  button and chooses each one's style on the page:

  ```json
  "actions": {"type": "list", "of": "button", "size": "lg", "role": "content",
              "label": "Buttons", "max_entries": 3,
              "description": "The buttons under the text: the main action first.",
              "default": [
                {"text": "Book an appointment", "target": {"type": "page", "page": "<id>"}, "variant": "filled"},
                {"text": "0761 234 567 0", "target": {"type": "phone", "number": "+497612345670"}, "variant": "outline"}]}
  ```

  ```liquid
  {% if actions %}<div class="x__actions">{% for b in actions %}{% if b.text %}<a class="p-button p-button--{{ b.variant }} p-button--lg" href="{{ b.target | link }}">{{ b.text }}</a>{% endif %}{% endfor %}</div>{% endif %}
  ```

  The fields come from the type; write none of your own. The variant
  class comes from each entry, the size is the list's. The row lines up
  with the text above it and follows its alignment
  (`display: flex; flex-wrap: wrap; gap: …; justify-content` as the text
  stands); it has no alignment of its own for the editorial team to set.
  A link drawn as a `p-button` outside such a list is refused as well
  (`binding-button-single`), also one drawn from the site's action
  (`site.action`): a header's call to action is a list of buttons of its
  own. A button repeated for every entry of another list, an address
  computed from fields and a link a script reads are no call to action.
- **Elements with a form are primitives.** Cards, notices, badges,
  images, forms, tables, lists, quotes: `"primitive": "card", "variant":
  "outlined"` on the prop, and the element carries `class="p-card
  p-card--outlined"`. See `read_design_system_v2` for the catalogue and
  each one's variants.
- **The surface is a context.** `"context": "default" | "tinted" |
  "inverse" | "brand"` on the schema; if not default, the template's
  root element carries `data-context="<context>"`. Colours come from it —
  a component is never rebuilt for a dark surface.
- **`usage` is required**: one or two sentences on when this component
  is the right one, and when not. The build matches content against it
  before it builds anything new.

## The schema — complete skeleton

The smallest schema that passes. `id`, `name`, `displayName`,
`description`, and `kind` are required; `name` must equal the `name`
argument of `submit_component`. (Existing components with the earlier
German keys — `beschreibung`, `art`, `rolle` — remain valid; new ones
are written in English.)

```json
{
  "id": "preis-tafel",
  "name": "preis-tafel",
  "displayName": "Preistafel",
  "description": "Drei Preise nebeneinander, jeder mit Titel und Betrag.",
  "kind": "section",
  "props": {
    "ueberschrift": {
      "role": "content", "type": "text", "label": "Überschrift", "localizable": true,
      "description": "Steht über den Preisen.", "default": "Unsere Preise"
    }
  }
}
```

`kind` states what the component is for — exactly one of these values:

| `kind` | Purpose |
|---|---|
| `section` | A block in the page flow. The normal case. |
| `layout` | The frame around all pages (header, footer). Cannot be inserted into pages. |
| `element` | A small building block inside a section. |

## Project types — one structure per kind of information

A site has kinds of information that recur across components: a service,
a team member, an opening hour. They are defined ONCE per project with
`submit_types` and reused by reference:

```json
"services": { "type": "list", "of": "service", "role": "content",
              "label": "Services", "default": [],
              "description": "The services listed in this section." }
```

`of` works on `list` and `group`; the fields come from the type, so do not
spell out `props` next to it. Call `list_types` before modelling a list —
if a matching type exists, use it; if not, define it first, then the
component. A type carries `label`, `description` and `props` with the same
contract as component props.

## The template language

Deliberately small: it always terminates and cannot break out.

```liquid
{{ prop }}                          output (HTML-escaped)
{{ bild.alt | default: titel }}     filters: default:, upcase
{{ bild.alt | default: 'Logo' }}    literal text — in quotes
{% if variante == "video" %}…{% else %}…{% endif %}
{% for person in site.team %}{{ forloop.index }}. {{ person.name }}{% endfor %}
{% comment %} … {% endcomment %}
```

Three namespaces: the instance's **props**, **`site.*`** for global
content (contact, opening hours, team), **`forloop`** inside loops —
with exactly these fields:

| Field | Meaning |
|---|---|
| `forloop.index` | position, **1-based** |
| `forloop.length` | length of the list |
| `forloop.first` | true on the first iteration |
| `forloop.last` | true on the last |

The German spellings `anzahl`, `erster`, `letzter` keep working in
existing components; new ones use the English names.

Deliberately *absent*: arithmetic, function calls, assignments,
includes, state, event handlers. Wherever one of these seems to be
missing, the logic belongs in an island.

## Hard rules

| Rule | Why |
|---|---|
| No `<script>` in the template | Motion lives in the component's CSS, state in a sealed island |
| Structured data as microdata: `itemscope`, `itemtype="https://schema.org/…"`, `itemprop` | A JSON-LD `<script>` is a script; the type's address is a name nobody loads and needs no privacy notice |
| No fixed colours, fonts, radii or shadows in the CSS — only names: `var(--color-…)`, `var(--font-…)`, `var(--radius-…)`, `var(--shadow-…)` | A style swap replaces the design system and must reach every component |
| No `{{ }}`/`{% %}` inside `<style>` | Styles are static; variants run through classes |
| Prop keys: English, `lower_with_underscores` (a-z, 0-9, _) | Keys are technical names like HTML attributes — what's visible is `label`/`description` |
| Every prop: `type`, `label`, `description`, `default`, `role` | Without the contract there is no editor UI and no validation — and without `description` the prompt processing cannot tell which prop is meant |
| The `default` is a value of the prop's type: `3`, not `"3"`; `false`, not `"false"`; a select's default is one of its option values; a collection's is its id or `""` | Every page that leaves the prop alone shows the default — the gate checks it like page content (`prop-default-invalid`) |
| `type` only from the catalog: `text`, `richtext`, `number`, `boolean`, `select`, `date`, `image`, `file`, `video`, `icon`, `link`, `collection`, `form`, `group`, `list`, `slot` | The catalog is closed — every interface and every renderer understands every prop |
| `text` carries **bold** and *italic* by itself: `**…**`, `*…*` | The core renders the marks wherever a text prop stands as text, and leaves the bare words in an attribute or a title. Output it as `{{ title }}`, never through `\| markdown`, and never split one sentence into several props only to emphasize a word |
| `richtext` in the template always via `{{ text \| markdown }}` | The editor's Markdown (paragraphs, `##`/`###`, `-`/`1.` lists, `**bold**`, `*italic*`, `` `code` ``, `[text](target)`) becomes HTML — without the filter, visitors would see asterisks. HTML in the value is escaped; `\| markdown` belongs ONLY on richtext |
| References as a `link` prop, and `{{ target \| link }}` in the template | Internal targets point at page IDs and survive every rename |
| Media: `{{ picture \| image }}` is the whole `<img>` with its sizes, loading lazily; `{{ logo \| image: "eager" }}` loads at once, for a picture visible without scrolling outside the content (a logo in the header; the content's first picture is made eager by the build), and a ratio goes beside it (`image: "3:2", "eager"`); `{{ picture \| image_source }}` the bare address; `{{ clip \| video }}` a player that streams the film in the size the visitor's line carries (only its still loads before Play), `{{ clip \| video: "loop" }}` the same film playing by itself, muted, in a loop and without controls, for a background or a short clip that shows something: it starts once it is laid out and in view (not in a closed tab, not below the fold), sharp from its first picture, and rests when it is not; an island may drive that `<video>` with `play()`, `pause()`, `currentTime` and `loop`, and a clip it plays starts wherever it is; `{{ clip \| video_source }}` the bare address of its 720p MP4, for a player of your own, which is never as sharp as the upload: where sharpness counts, take the loop and drive it; `{{ clip \| video_chapters }}` the film's chapters as a list of buttons (see "A film's chapters"), `""` without any; `{{ doc \| file_name }}` and `{{ doc \| file_size }}` name a download ("price-list.pdf", "121 KB") and give `""` when nothing is chosen | The library hands out ids; the filters turn them into what the page needs |
| A legal text the website receives from its provider (IT-Recht Kanzlei, eRecht24; the person connects it under Settings › Legal texts): a `select` prop `kind` with the options `terms`, `privacy`, `imprint`, `withdrawal`, `privacy_social_media`, and `{{ kind \| legal_text }}` in the template, so one component serves every legal page. It renders the text in the page's language as checked HTML inside `<div class="cleverpage-legal-text">` (headings, paragraphs, lists, tables, links), `""` while none was received | The provider keeps the text current and an update reaches the page by itself; a text copied into a prop goes stale the day the provider changes it. Style it through the class, never through the text |
| `{{ when \| date }}` reads an ISO date (`2027-03-14`, a time after it is fine) in the site's language: "14. März 2027", "14 March 2027"; anything else stays as it is | Dates are stored in one form and shown in the reader's |
| `link` values: `{type: "page", page: "s-…"}` (page ID, not name or path), `{type: "external", url}`, `mail`/`address`, `phone`/`number`, `file`/`path`, `anchor`/`name`, optional `anchor` with `page`. An address is `https://…`, `mailto:`, `tel:`, a path or `#anchor` | The key per type is fixed — page IDs come from `list_pages`. `javascript:`, `data:` and the like are refused: they would run in the visitor's browser |
| `role: "content"` or `"design"` | Decides how the field is sorted in the editor UI: content is free, design is a predefined choice — both remain editable by the editor |
| Everything in the template is declared in the schema | Otherwise someone later sets props nobody knows about |
| A `<form>` needs an inbox (`data-cleverpage-inbox` + `inbox` in the schema) or a `form` prop through `form_action` | A form without a target loses every entry silently — see *Forms* below |

## Values

What a page (or a default) may put into a prop. The gate checks each
value against its type, inside groups and list rows as on top, and
refuses what the core could not build.

| Type | Value |
|---|---|
| `icon` | `"lucide:check"` — always with its set, never `"check"` |
| `image`, `file` | a media id `"m-…"`, or `{media: "m-…", alt}`; `""` is nothing chosen |
| `video` | a video of the library, `"m-…"` or `{media: "m-…"}`; or an embed, `{provider: "youtube" \| "vimeo", id}`; `""` is nothing chosen. The media id must be a video |
| `collection` | its id `"c-…"`, or `{target: "c-…", limit: 6}` (`ziel`/`grenze` are read too); it must exist in the project (`list_collections`) |
| `form` | its id `"f-…"`; it must exist in the project (`list_forms`) |
| `link` | see the hard rules above |
| any `localizable` prop | `{"_languages": {"de": …, "en": …}}`: only project languages, the default language always among them, each value of the prop's type |

## The design system

Every project has ITS design system (`read_design_system_v2`), and it
comes before the first component: colour worlds from one base colour
each, the fonts (`adopt_fonts` lays a family into the site), the text
roles, spacing, radii, shadows, the primitives. The gate knows the
variables it produces and refuses a `var(--…)` that does not exist,
naming it — so when a finding says a variable is unknown, read the
design system instead of guessing a neighbour.

## The spacing between sections

The design system sets the default gap: `--spacing-section-md` (with
`-xs` … `-2xl` beside it). Section components take their vertical
padding from there — `padding-block: var(--spacing-section-md)` — and
never write it as fixed pixels. Only then do the editorial spacing
controls work: when a component is inserted, the spacing above/below
can be adjusted per instance (`spacing_top`/`spacing_bottom` in the
page entry — a factor of the default, or a fixed length); the core
computes that as a delta against `--spacing-section-md`.

## IDs: avoid on principle

Fixed `id` attributes in the template are a trap: if the component
appears twice on one page, the HTML is invalid and anchors like
`aria-labelledby` point to nothing. The build then warns (`duplicate-id`).

- Styling and behavior run through **classes**, never through IDs.
- Jump targets for editors are a **link prop** with `type: "anchor"`
  — not a hard-wired id.
- If a technical id is genuinely needed (say, `aria-labelledby` within
  the component), derive it from **`{{ instanz }}`** — the number of
  this insertion on the page, provided by the core:

```liquid
<section aria-labelledby="faq-titel-{{ instanz }}">
  <h2 id="faq-titel-{{ instanz }}">{{ titel }}</h2>
</section>
```

`instanz` does not need to appear in the schema; a prop of your own
with that name would take precedence.

## Variants instead of freedom

You grant design leeway as a `select` with fixed options, never as a
free field. The variant drives a class; the CSS does the rest:

```liquid
<section class="hero hero--{{ variante }}">…</section>
```

```json
"variante": {
  "role": "design", "type": "select", "label": "Darstellung",
  "description": "Wie die Komponente auftritt — mit großem Bild oder nur mit Text.",
  "default": "bild",
  "options": [{ "value": "bild", "label": "Mit Bild" }, { "value": "nur-text", "label": "Nur Text" }]
}
```

The gate enforces this: a non-select prop interpolated into a `class`
attribute is rejected, and template constructs inside a `style`
attribute (or `<style>` block) are always rejected — CSS never comes
from props.

This way the editor can choose — but only between states you have
designed.

## A film's chapters

Chapters belong to the film, not to the component: they are kept in the
media library (`update_media` with `chapters`), and every component that
shows the film shows the same ones. `{{ clip | video_chapters }}` renders
them, `""` for a film without chapters:

```html
<ol class="cleverpage-chapters" data-cleverpage-chapters="m-…" aria-label="Kapitel">
  <li><button type="button" data-cleverpage-start="64" aria-label="1 Minute 4 Sekunden: Entwurf">
    <time datetime="PT1M4S">1:04</time> <span>Entwurf</span></button></li>
  …
</ol>
```

The player makes a click seek there and play, also before the film was
started, and marks the chapter that plays with `aria-current="true"`;
without a script the list stands as text. The list's name and the spoken
time follow the page's language. Position and look are yours: style
`.cleverpage-chapters`, its buttons and `[aria-current="true"]`. Only a film of
the library with its stream has chapters; an embed has none.

Under the film, beside it from a wide screen on:

```liquid
<section class="film">
  <div class="film__player">{{ clip | video }}</div>
  {% if chapters == "show" %}{{ clip | video_chapters }}{% endif %}
</section>
```

```css
.film { display: grid; gap: 1.5em; }
@media (min-width: 64rem) {
  .film:has(.cleverpage-chapters) { grid-template-columns: 2fr 1fr; align-items: start; }
}
.film .cleverpage-chapters { list-style: none; margin: 0; padding: 0; display: grid; gap: .25em; }
.film .cleverpage-chapters button { display: flex; gap: .75em; width: 100%; text-align: left;
  font: inherit; color: inherit; background: none; border: 0; padding: .5em .75em;
  border-radius: .5em; cursor: pointer; }
.film .cleverpage-chapters time { font-variant-numeric: tabular-nums; opacity: .7; }
.film .cleverpage-chapters button:hover { background: color-mix(in srgb, currentColor 8%, transparent); }
.film .cleverpage-chapters [aria-current="true"] { background: color-mix(in srgb, currentColor 14%, transparent); }
```

Spacing, radius and the colour of the current chapter belong to the
design system's variables (`read_design_system_v2`); the example leaves
them out because their names are the project's.

A `select` prop `chapters` with `show` and `hide` lets the editors decide
per section; it needs no new type. The page carries the film as a
`VideoObject` with a `Clip` per chapter on its own, so a search can show
key moments.

## Structured data

The build writes three kinds of schema.org data by itself, never a
component:

- the start page names the website and its owner (`WebSite`,
  `Organization` with name, web address, logo, description and social
  profiles);
- each entry of `site.locations` becomes a `LocalBusiness` of its kind
  with address, phone, opening hours, special days, departments and
  phone hours (see *The website's locations*);
- every page with a film of the library carries its `VideoObject`.

All three need absolute addresses, so they appear only once a domain is
connected (`connect_domain`); a preview or a staging build without one
shows none, and that is not a fault.

What a component shows, it may describe itself in microdata on its own
markup: the visible words are the data, so they cannot drift apart. A
`<script>` stays refused, JSON-LD included. The gate checks every item
against a closed catalogue — questions and answers (`FAQPage`,
`Question`, `Answer`), events, people, products with offers and
reviews, articles, job postings, courses, breadcrumbs and lists, and
the places, addresses and amounts inside them — and refuses an unknown
type, an unknown property (`microdata-property-unknown`) and a missing
one Google needs before it shows anything
(`microdata-property-missing`). The business itself (`LocalBusiness`,
`Store`, …) and films are the build's to say.

```liquid
<section itemscope itemtype="https://schema.org/FAQPage">
  {% for e in questions %}
  <details itemprop="mainEntity" itemscope itemtype="https://schema.org/Question">
    <summary itemprop="name">{{ e.question }}</summary>
    <div itemprop="acceptedAnswer" itemscope itemtype="https://schema.org/Answer">
      <div itemprop="text">{{ e.answer | markdown }}</div>
    </div>
  </details>
  {% endfor %}
</section>
```

An event needs `name`, `startDate` and a `location` (a `Place` with its
name or address); a date the page shows as words carries its ISO form in
`<time itemprop="startDate" datetime="{{ when }}">{{ when | date }}</time>`.
schema.org addresses are names, not servers: they need no
`notices.privacy`.

## The website's locations

Where the business is and when it is open is the website's, not a
component's: `site.locations`, written with `submit_global_fields`
(`{"locations": {"value": [...]}}`, no declaration — Cleverpage declares
it) and changed by the editors in the panel of the part that shows it.
It is optional; a website that does not need it leaves it out.

Each entry is one of three kinds, and a department or phone hours
belong to the location above them:

| kind | what it is | needs |
|---|---|---|
| `location` | a place of business: a shop, a second house, a branch | `street`, `postal_code`, `city` |
| `department` | a part of it with hours of its own: the workshop, a counter, consulting hours | hours; its own address only if it differs |
| `phone` | when the location answers the phone | hours; its own `phone` only if it differs |

Every entry carries `name` (empty for the only location), `type` (a
location's or department's kind of business: `FurnitureStore`,
`Physician`, `Restaurant`, … — `read_site` lists the shape), `phone`,
`email`, `hours` (one row per stretch of days with the same times,
`{"from_day": "mo", "to_day": "fr", "opens": "09:00", "closes": "18:00"}`;
a day with a break has two rows), `exceptions` (`{"from": "2026-12-24",
"to": "2026-12-26", "closed": true}` or with `opens`/`closes`) and
`note` for what hours cannot say ("appointments outside these hours on
request"), which is shown but never enters the structured data.

A footer shows the locations instead of keeping a copy:

```liquid
{% for place in site.locations %}{% if place.kind == "location" %}
  <p>{{ place.street }}, {{ place.postal_code }} {{ place.city }}</p>
  {% for h in place.hours %}<p>{{ h.from_day }}–{{ h.to_day }} {{ h.opens }}–{{ h.closes }}</p>{% endfor %}
  {% if place.note %}<p>{{ place.note }}</p>{% endif %}
{% endif %}{% endfor %}
```

Days are the codes `mo` … `su`; a component that shows them as words
maps them in its template.

## Forms — the inbox

A `<form>` in a template has to lead somewhere. One that does not is
not a broken component: it renders, it looks finished, and every entry
a visitor makes falls on the floor. The gate rejects it.

There are exactly two ways, and never both at once.

**The inbox — the component asks its own questions.** The `<form>`
carries `data-cleverpage-inbox` and no `action`, `method` or `enctype`;
the schema gets an `inbox` next to `props`:

```json
"inbox": {
  "name": "Newsletter",
  "message": "Danke — du bist dabei.",
  "fields": [
    {"name": "mail", "label": "E-Mail-Adresse", "type": "email", "required": true}
  ]
}
```

The field types are the ones from `list_forms`: `text`, `textarea`,
`email`, `phone`, `number`, `date`, `select`, `radio`, `checkbox`,
`rating`, `file`. `select` and `radio` list their `options`.

Every `name="…"` in the form must appear in `fields`, and every field
must appear in the markup — otherwise the submission is rejected later,
at the visitor, and is gone. Never write hidden fields, a honeypot or a
captcha: the system adds those when the page is built, along with the
address.

What the component owns is the **questions**, and its own thanks.
Recipients, retention and access belong to the operator and are set
next to the component — do not put them, a redirect or a spam check in
the schema; the gate says so. `keep` is not yours either: whether entries are stored is a
question a person answers in the interface.

Spam protection needs nothing from the component. The honeypot and the
origin check are always on; the system adds neither an arithmetic
question nor a consent box to a component's form. Nothing reads what a
submission says.

The system sends the form itself and keeps the visitor on the page. An
island that wants to send it — to shape a value first, or to show its
own thanks — calls `preventDefault()` in its `submit` listener; the
system's sender then stands aside, and one submission makes one entry.
The hidden fields stay in the form, so `new FormData(form)` carries
them. Post that to the form's `action` through `dienste.holen` with
`Accept: application/json`; the answer is `{ok, message}` or
`{ok: false, errors: [...]}`.

**The other way — an existing form.** A prop of type `form`, the
address from `{{ prop | form_action }}` and the field list from
`{% for f in prop | form_fields %}`. Use this when the form already
exists and several components show it.

## Interactivity — two tiers, no third

1. **The platform**: `<details>` for the accordion, `<dialog>` and
   `popover` for overlays, scroll snap, container queries, view
   transitions — and **all motion** (see the next section). Choosing
   one view among several (tabs, a filter by group, before/after) is
   platform too: radio inputs and `:has(…:checked)`, no form and no
   script. Name the group after `{{ instanz }}` so two insertions on one
   page do not share it:

   ```liquid
   <div class="karte">
     <div class="karte__wahl" role="radiogroup" aria-label="{{ filter_label }}">
       <label><input type="radio" name="karte-{{ instanz }}" value="alle" checked> Alle</label>
       {% for gruppe in gruppen %}<label><input type="radio" name="karte-{{ instanz }}" class="karte__wahl--{{ forloop.index }}"> {{ gruppe.titel }}</label>{% endfor %}
     </div>
     {% for gruppe in gruppen %}<section class="karte__gruppe karte__gruppe--{{ forloop.index }}">…</section>{% endfor %}
   </div>
   <style>
   .karte:has(.karte__wahl--1:checked) .karte__gruppe:not(.karte__gruppe--1) { display: none; }
   .karte:has(.karte__wahl--2:checked) .karte__gruppe:not(.karte__gruppe--2) { display: none; }
   </style>
   ```

   One rule per position you offer; without a match every group shows,
   which is the right state without the choice.
2. **An island** — for everything that needs state kept across pages,
   network, or a backend (booking, configurator, calculator, lightbox).
   See below.

There is no tier in between. The platform ships **no** named effects
or behaviors you could call — it knows no patterns, only rules. What
your component does, you describe yourself.

## Motion

Scroll-driven motion is written in standard CSS, in your component's
`<style>`. There is no dedicated dialect for it, because the standard
already expresses the whole model:

```
Motion = driver ×   range ×     progression
         animation- animation-  @keyframes
         timeline   range
```

**Driver** — `scroll()` for the page scroll, `view()` for the
element's own passage through the viewport, `--name` for the passage
of another element. **Range** — `entry` / `contain` / `exit` with
percentages, or lengths like `0 120px`. **Progression** — arbitrary
properties in `@keyframes`.

```css
/* Parallax: driver is the element's own passage, progression a translation */
.held__bild {
  animation: bild-parallax linear both;
  animation-timeline: view();
  animation-range: contain 0% exit 100%;
}
@keyframes bild-parallax {
  from { transform: translateY(0); }
  to   { transform: translateY(14%); }
}
```

### Timelines across component boundaries

If one component is to react to the passage of **another**, one
publishes a timeline and the other consumes it:

```css
.held           { view-timeline: --held block; }   /* publishes */
.leiste__knopf  { animation-timeline: --held; }    /* consumes */
```

The name belongs to the project like a class name — the core collects
it mechanically and sets `timeline-scope` at the root. If you consume
a foreign timeline, **declare it in the schema**:

```json
"bewegung": { "konsumiert": ["held"] }
```

And plan for the case that the timeline is missing on a page: then
the animation is inert and the base state applies. Choose the base
state so that it is correct without the other component.

### Validation rules

| Rule | Why |
|---|---|
| No `!important` on `animation*` | It would defeat the enforced motion reduction |
| Every `@keyframes` name used is defined | A typo would otherwise fail silently |
| Foreign timelines are listed in `bewegung.konsumiert` | The dependency is declared instead of guessed |

`prefers-reduced-motion` you do **not** have to handle yourself — the
core enforces it for the whole page.

`infinite` and a duration in seconds are **allowed**. The gate names
them in `warnungen` and lets the build through: a carousel that moves
on by itself, a recording dot, a marquee that keeps going are all
legitimate. Prefer scroll-driven motion where the movement happens
*while reading* — it stops when the reader stops. Where the reader or
the clock is meant to drive it, drive it, and give anything that runs
for longer than a few seconds a way to stop it.

## Islands

An island is a sealed program. The contract is tight because it is
the only arbitrary JavaScript on the page:

| Rule | Why |
|---|---|
| Exports `montiere(element, props, dienste)` | One entry point, no self-start |
| Sees **only its element** — no `document.querySelector`, no `window.*` | It must not touch other components |
| Network only via `dienste.holen(pfad)` | Direct `fetch` bypasses the declaration |
| Page scroll only via `dienste.scroll(cb)`, moving the page only via `dienste.scrollTo(y, {duration, curve})`, motion preference only via `dienste.ruhig(cb)` | `window` is sealed off; the runtime bundles what an island may need of it |
| Every path is listed in `island.netz.erlaubt` **and** in `data-island-netz` | Validation checks the schema, the runtime checks the markup — both must agree |
| No `localStorage`, `sessionStorage`, `document.cookie` | Storage is not part of the contract |
| `island.ohne_js` describes the fallback | Without JS a poorer page, not a broken one |

Props arrive as `data-` attributes from the template — that is, from
the schema; no second data channel:

```liquid
<div data-island="terminbuchung"
     data-island-netz="/api/buchung/slots /api/buchung"
     data-einleitung="{{ einleitung }}">
  … static shell that stays in place without JS …
</div>
```

The shell in the template is not a placeholder but the fallback. Build
it so the page is still worth something without JavaScript.

The island is submitted as the `islands` argument of
`submit_component`: `{ "<component-name>.js": "<source code>" }`.
Validation checks all the rules above; on violation nothing is
written.

## Responsive and accessible

The draft is usually desktop only. The component must work on mobile:
`clamp()` for font sizes, switch `grid-template-columns` in a
`@media` query, images with `object-fit`. Every `<img>` needs a
meaningful `alt` that comes from a prop; interactive elements need
accessible names.

## Checklist before submitting

- [ ] Rendered with the schema defaults — no diagnostics
- [ ] Rendered with realistic content — looks right
- [ ] Empty lists and missing images break nothing
- [ ] Checked narrow (~380 px)
- [ ] No fixed colors, no `font-family`, no script
- [ ] The description says *what the component is for* — it is what
      the editor AI reads later
- [ ] Any `<form>` has an inbox, and markup names and `inbox.fields`
      match in both directions
- [ ] Every content `text` and `richtext` prop says `localizable`

## Language

Prop keys and component names are conceived as English-technical;
`label`, `description`, and `displayName` you write in the website's
default language (`read_site` → `sprachen.standard`) — that is what
the editorial team works with.

Every `text` and `richtext` prop with `role: "content"` says whether it
is translated: `"localizable": true` for words a visitor reads
(headings, body text, button and link texts, labels, notes),
`"localizable": false` for what stays the same in every language (a
person's or company's name, a number like "120+", a place, a photo
credit, an e-mail address, a phone number, an anchor). Decide it for
every such prop. A content text without the flag counts as
untranslated: a page holding one never appears in a further language,
because it would show that text there in the default language.

Collection fields say `localizable` the same way (`submit_collection`,
on `text`, `richtext`, `image`, `file`, `link`). A list built into the
template reads its entries in the page's language by itself. An island
that queries a collection in the browser does not: it sends the
language with every query, `/_daten/<id>?language=…`, handed over from
the template as `data-language="{{ site.languages.current }}"` (an
island never sees `<html>`). The answer then carries the values in that
language and each entry's `url` to its page there; without it, every
language gets the default one.

A language switch reads `site.languages.all`: per language `code`,
`name`, `current`, and `url`, the address of **this page** in that
language (`/en/privacy` on `/datenschutz`). Where the page is not
published in a language yet, `url` leads to that language's start page
and `available` is `false`. It works in the header as well, since the
build renders a frame that uses `url` for each page. `site.languages.others`
is empty on a website with one language, so the switch shows only once
there is a second one (a condition compares with `==` or `!=` only, and a
list has no `size`):

```liquid
{% if site.languages.others %}
<nav class="header__languages" aria-label="Language">
  {% for l in site.languages.all %}
  <a href="{{ l.url }}" hreflang="{{ l.code }}" lang="{{ l.code }}"{% if l.current %} aria-current="true"{% endif %}>{{ l.code | upcase }}</a>
  {% endfor %}
</nav>
{% endif %}
```

## Icons

Icons are, as a rule, **props of type `icon`**, not fixed SVG in the
template — the editor should be able to swap them. Values are always
fully qualified (`lucide:arrow-right`); the project's base set is in
`read_site` (`icon_set`, default `lucide`) and is your first choice.
`search_icons` finds names with a preview; other sets (`tabler:`,
`ph:`, `heroicons:`, `material-symbols:`) are allowed when a specific
icon calls for them.

Output goes through `{{ symbol | icon }}` — at build time this
becomes an inline SVG (`currentColor`, `1em`, `aria-hidden`), so it
inherits text color and size by itself. You control size and color in
the CSS on the surrounding element, never in the value.

Fixed SVG in the template is allowed when it is EXPLICITLY design
that nobody is meant to swap (say, a decorative arrow). Then it does
NOT belong in the schema — a prop with `role: "design"` for a fixed
SVG is a contradiction.
