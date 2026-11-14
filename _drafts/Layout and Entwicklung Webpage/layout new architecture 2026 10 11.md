# Site Architecture — How It Works

## The Core Logic Change

**Academic Pages default logic:** Each menu point maps to a separate Jekyll _collection_ (a folder). Publications live in `_publications/`, talks in `_talks/`, portfolio in `_portfolio/` — each with its own YAML schema and its own archive page querying `site.talks`, `site.portfolio`, etc.

**Your amended logic:** One collection (`_publications/`) holds **all** publication-type notes. Menu points are created by **filtering on the `category:` field**, not by folders or collections. This means one source of truth, no duplicated notes, and consistent fields everywhere.

`One folder (_publications/)     └── filtered by category: → four menu points`

---

## Files Changed / Created on GitHub

### Navigation (the menu)

|File|What changed|
|---|---|
|`_data/navigation.yml`|Four entries added under `main:` — Authored Publications `/authored-publications/`, Collaborative Publications `/collaborative-publications/`, Presentations `/talks/`, Projects `/projects/`. Guide kept.|

### Archive pages (in `_pages/`)

|File|Query logic|Shows|
|---|---|---|
|`authored-publications.html`|`site.publications` where `category == "books"`|Authored works|
|`collaborative-publications.html`|`site.publications` where `category == "manuscripts"`|Supervised & collaborative works|
|`talks.html`|`site.publications` where `category == "presentations"`|Presentations (was originally `site.talks`)|
|`projects.html`|No query — free-form content|Manually written page|

Note: `talks.html` keeps its filename and permalink `/talks/` but no longer reads from the `talks` collection.

### Templates (includes & layouts)

|File|Status|Purpose|
|---|---|---|
|`_includes/archive-single-presentation.html`|**New**|Renders presentations as title + excerpt + citation + download link only. No venue, no date.|
|`_includes/archive-single.html`|**Fixed**|Unclosed `{% if %}` at line 97 closed. Handles empty `venue` gracefully (shows year only).|
|`_layouts/single.html`|**Fixed**|Two bugs: `{% endunless %}` used where `{% endif %}` belonged; `</header>` sat inside an unclosed `{% elsif %}` block.|
|`_includes/archive-single-talk.html`|Untouched|Original theme file, now unused but harmless.|

---

## Files Created in Obsidian

|Item|Location|Notes|
|---|---|---|
|Presentation template note|Your templates folder|Same YAML as publication template, only `category: presentations`. Must start with `---` on **line 1** or Properties panel won't render.|
|Presentation notes|`_publications/`|Regular publication notes with `category: presentations` and empty `venue`.|

---

## The Category System (add new types here)

To add a fifth menu point later, you need three things:

1. A new note in `_pages/` with the filter pattern:

liquid

Copy

```
{% for post in site.publications reversed %}
  {% if post.category == "your-new-category" %}
    {% include archive-single.html %}
  {% endif %}
{% endfor %}
```

2. A matching entry in `_data/navigation.yml`
3. Notes in `_publications/` using that `category:` value

---

## Field Reference (publication YAML)

|Field|Behaviour on site|
|---|---|
|`title`|Linked heading|
|`category`|Determines which menu page it appears on|
|`collection`|Always `publications`|
|`permalink`|URL path|
|`excerpt`|Short description under title|
|`date`|Year shown (hidden on presentations)|
|`venue`|Renders as "Published in _[venue]_, [year]" — blank omits it|
|`catalogue_entry`|Shown as clickable "Catalogue entry:" link|
|`buy_paper` / `paperurl`|Download/Buy links|
|`citation`|"Recommended citation:" text|

The Projects page is one single, continuous document. You keep writing in the same file:

markdown

Copy

```
## Project One

Description here. Link to [Wochenmärkte](/publication/2003-10-01-wochenmaerkte).

## Project Two

Another description. Another [link](/publication/some-other-note).

## Project Three

And so on...
```

Each `##` heading becomes a section title on the website. They all appear stacked on one `/projects/` page, in the order you write them.

**If it ever gets too long**, you can split it later into separate pages (e.g. `/projects/urban-development/`, `/projects/governance/`) and add dropdown submenus — but that's an optimization for much later. For now, one file is simpler and works perfectly.