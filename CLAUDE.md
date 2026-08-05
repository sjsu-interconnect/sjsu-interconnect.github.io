# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Jekyll static site for the SJSU **interconnect lab** (Prof. Genya Ishigaki). Content is Markdown + Liquid templating; publications are rendered from BibTeX via the `jekyll-scholar` plugin. Published at https://sjsu-interconnect.github.io/.

## Commands

```bash
bundle install            # install gems (first time)
bundle exec jekyll serve  # local dev server with live reload at http://localhost:4000
bundle exec jekyll build  # build into _site/
```

There are no tests, linters, or a JS/npm toolchain — this is a content site.

## Deployment

`jekyll-scholar` is a non-whitelisted plugin, so GitHub Pages **cannot** build this site. The compiled output in `_site/` is committed to the repo and served directly. **After changing content, run `bundle exec jekyll build` and commit the regenerated `_site/` along with the source.** `_site/` is listed in `.gitignore` but is already tracked, so changes to it must be staged explicitly. Forgetting this means the live site won't reflect source edits.

## Architecture

- **Top-level `*.markdown` / `*.md`** (e.g. `index.markdown`, `members.markdown`, `research.markdown`, `teaching.markdown`, `publication.markdown`) are the main pages. Each declares its URL via `permalink:` front matter and `layout: page` or `layout: default`.
- **Collections** (configured in `_config.yml`) drive the multi-page sections. Each `_<name>/` dir holds one file per item:
  - `_members/` — people pages (most members are just bullet entries in `members.markdown`; only some have a dedicated page).
  - `_courses/` — per-course pages; newer terms are linked PDFs dropped in the collection, older ones are full Markdown pages.
  - `_resource/`, `_recollection/`, `_projects/` (`output: false`).
- **Publications** come from `_bibliography/*.bib` (`conferences.bib`, `journals.bib`, `presentations.bib`). `publication.markdown` renders them with `{% bibliography --file journals --file conferences %}`. Sorting/style is set under `scholar:` in `_config.yml` (APA, by `year,type,month`, descending).
- **`_layouts/bib.html`** is the custom jekyll-scholar entry template (`bibliography_template: bib`). It controls author formatting and which fields appear (doi, `myurl`, `arxiv`, etc.). Edit this to change how each citation line looks.
- **Theme** is `minima` (a gem). There is no local override of minima's layouts/includes — `_includes/` is empty and `_layouts/` only contains `bib.html`. To customize theme markup, copy the file from the minima gem into `_layouts/`/`_includes/` first.

## Conventions

- **BibTeX entries require a custom `type` field**: `type={C}` for conference, `type={J}` for journal, etc. `bib.html` prints this as the `[C]`/`[J]` prefix. Optional custom fields `myurl` and `arxiv` render as extra links.
- Member/course lists frequently use `{% comment %} ... {% endcomment %}` to hide entries (e.g. members whose dedicated page isn't ready, prospective-student blurbs). Toggling visibility usually means moving content in/out of these comment blocks rather than deleting it.
- Course pages mix two styles: full Markdown pages (older terms, with a `permalink`) and PDF syllabi linked from `teaching.markdown` (recent terms). Match the surrounding pattern for the term you're editing.
