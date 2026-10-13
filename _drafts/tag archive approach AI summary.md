# Project: Tag-based "Topics" pages for academicpages site

## Goal

Publications and talks tagged in their YAML frontmatter (e.g. `tags: [street-design]`) should automatically appear as **clickable title-only links** under a topic heading on a `/tags/` page. Heading should read **"Street Design"** (each word capitalized). Menu item labeled **"Topics"**. No manual page creation — adding a tag to a file and pushing should be enough.

## Site setup

- Repo: `juergenmarklembcke/juergenmarklembcke.github.io`, branch `master`
- Template: AcademicPages (fork of Minimal Mistakes)
- Collections in `_config.yml`: `teaching`, `publications`, `portfolio`, `talks`, `posts` — all `output: true`
- Archives config uses Minimal Mistakes' built-in method:
    
    yaml
    
    Copy
    
    ```
    category_archive:
      type: liquid
      path: /categories/
    tag_archive:
      type: liquid
      path: /tags/
    ```
    
- The `jekyll-archives:` block is **commented out**, and `jekyll-archives` is **not** in `plugins:` or `whitelist:` → so archive pages are **not auto-generated**; the "liquid" method only creates links, the page itself must exist.
- Publications live in `_publications/`, talks in `_talks/`. Publication frontmatter fields: `title`, `collection`, `category` (`books` / `manuscripts`), `permalink`, `excerpt`, `date`, `venue`, `catalogue_entry`, `buy_paper`, `paperurl`, `citation`, `tags`.
- Tags already added manually via Obsidian Properties panel to several publications (e.g. `street-design`).

## ✅ Completed successfully

1. **Tag field added** to publication files in Obsidian — `tags:` appears as an editable multi-value property; tags entered lowercase-hyphenated (`street-design`).
2. **`_data/navigation.yml`** edited — added `- title: "Topics"` / `url: /tags/`. **Menu item now shows correctly.**
3. **`_pages/tag-archive.html`** exists (was already in repo) with `layout: archive`, `permalink: /tags/`. Originally grouped `site.posts` by tags.
4. **`/tags/` page loads online** at `https://juergenmarklembcke.github.io/tags/` — no 404, menu works.
5. Tag detection works: the heading area showed **"Street design"**, proving Jekyll found the tag on publications.

## ❌ Current stuck point

The page renders **raw markdown as plain text** instead of HTML:

`## - [Verkehrsverlagerung durch Förderung...](/publication/Verkehrsverlagerung...) - [Entwicklung eines...]`

Problems visible:

- `##` shown literally (heading not rendered)
- `[title](url)` shown literally (links not rendered)
- Everything collapsed onto one continuous paragraph instead of separate list items
- Capitalization fix (`split: '-' | map: 'capitalize' | join: ' '`) never got a chance to show

**Root cause:** Liquid output inside the loop isn't being passed through Kramdown. Indentation from `{% for %}` / `{% if %}` tags breaks markdown block recognition, so Kramdown treats it all as one inline paragraph.

## Approaches tried and failed

|#|Approach|Result|
|---|---|---|
|1|Original `tag-archive.html` using `{% include group-by-array collection=site.posts field="tags" %}` + `{% include archive-single.html %}`|Showed sample blog posts, not publications|
|2|Same includes but `collection=site.publications` then `site.talks`|Heading "Street design" appeared, **no items listed** — `archive-single.html` doesn't know how to render publications|
|3|`where_exp: "pub", "pub.tags contains tag"` with `map: "tags" \| join: "," \| split: "," \| uniq \| sort`|**Liquid syntax errors**: "Expected end_of_string but found pipe"; plus `undefined method 'gsub' for {...hash...}` — `tag` arrived as an object, not a string|
|4|Manual tag collection with `push` / `unless contains` loops, `<h2>` + `- [{{ pub.title }}]({{ pub.url }})`|Page rendered but **all raw markdown, single paragraph**|
|5|Replaced `<h2>` with `## {{ ... }}`, added blank lines around headings|Still raw markdown, `##` visible literally|
|6|`{{ ... \| prepend: '## ' \| markdown }}` for heading, bare `- [..](..)` for items|**Still raw markdown** (current state)|

## Ideas not yet tried

- Build the whole list into a variable and pass it through `| markdown` once
- Use pure HTML instead of markdown: `<h2>...</h2>` and `<ul><li><a href="{{ pub.url }}">{{ pub.title }}</a></li></ul>` — avoids Kramdown entirely (most likely to work)
- Strip indentation from Liquid tags so markdown blocks start at column 0
- Check whether `compress_html` in `_config.yml` is collapsing whitespace/newlines (it's set to `clippings: all`)

## Also noted, unresolved

- `_pages/publications.html` throws its own Liquid warning: _"Expected end_of_string but found pipe in `post in site.publications | where: "category", "books" | reversed`"_ — pre-existing issue, unrelated to tags, but worth fixing later.
- Earlier suggestion of per-tag template at `permalink: /tags/:name/` was created as `_pages/tag.html` but the working solution ended up being the single `/tags/` index page listing everything. That extra file may be redundant/conflicting and could be deleted.

---

Paste this back whenever you want to continue. My strong suspicion for the next attempt is **approach 7: pure HTML tags** (`<h2>`, `<ul>`, `<li>`, `<a>`) since that sidesteps the markdown-processing problem completely rather than fighting it.