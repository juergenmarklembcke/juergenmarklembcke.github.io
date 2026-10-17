**1. `_config.yml` — Declare the `_tags` collection**

- **Problem:** Jekyll ignored the `_tags/` folder entirely because it wasn't declared as a known collection. No pages were built, so `/tags/street-design/` 404'd.
- **Fix:** Added a `tags` entry under `collections:`:
    
    yaml
    
    Copy
    
    ```
    tags:
      output: true
      permalink: /tags/:name/
    ```
    
- **Result:** Jekyll now processes every file in `_tags/` and generates corresponding pages.

**2. `_tags/street-design.md` — Fix permalink URL**

- **Problem:** The permalink had `permalink: /tags/#street-design/` — the `#` character is illegal in URLs and caused 404s.
- **Fix:** Changed to:
    
    yaml
    
    Copy
    
    ```
    permalink: /tags/street-design/
    ```
    
- **Result:** The topic page is reachable at the correct URL and lists all matching publications/talks.

**3. `_includes/author-profile.html` — Sidebar tag menu**

- **Goal:** Display all tags from every publication and talk as clickable links in the sidebar, below the Email link.
- **Code added:** A loop that collects all unique tags across `site.publications` and `site.talks`, then renders them as `#tagname` links:
    
    html
    
    Copy
    
    ```
    {% assign all_tags = "" | split: "" %}
    {% for pub in site.publications %}
      {% for tag in pub.tags %}
        {% unless all_tags contains tag %}
          {% assign all_tags = all_tags | push: tag %}
        {% endunless %}
      {% endfor %}
    {% endfor %}
    {% for talk in site.talks %}
      {% for tag in talk.tags %}
        {% unless all_tags contains tag %}
          {% assign all_tags = all_tags | push: tag %}
        {% endunless %}
      {% endfor %}
    {% endfor %}
    {% if all_tags.size > 0 %}
      {% for tag in all_tags %}
        <li style="margin-top: 0.5em;"><a href="{{ base_path }}/tags/{{ tag }}/" style="color: #4169E1;">#{{ tag }}</a></li>
      {% endfor %}
    {% endif %}
    ```
    
- **Result:** Sidebar shows clickable `#street-design` (and any future tags) that navigate to the correct topic pages.

**4. `_includes/tags_list.html` — Fix grey tag links at bottom of publication pages**

- **Problem:** Minimal Mistakes' built-in tag renderer prepended `#` to tag links when `tag_archive.type` was `liquid`, producing `/tags/#street-design` which never loaded.
- **Fix:** Changed `path_type` from `"#"` to `"/"`:
    
    liquid
    
    Copy
    
    ```
    {% when "liquid" %}
      {% assign path_type = "/" %}
    ```
    
- **Result:** The grey "Tags: street-design" links at the bottom of each publication page now correctly point to `/tags/street-design/`.

**5. `_publications/*.md` — Tags already present**

- No changes needed here. Tags like `tags: [street-design]` were already added via Obsidian and were correctly picked up by Jekyll once the collection was declared.

---

**How it all works together now:**

When you add a new publication with `tags: [new-topic]`:

1. The tag automatically appears in the **sidebar** (collected from all publications/talks)
2. The grey tag link at the **bottom of the publication page** works correctly
3. You create `_tags/new-topic.md` with the same content structure, and it appears as a browsable page listing all matching publications

No further config changes are needed for new topics.