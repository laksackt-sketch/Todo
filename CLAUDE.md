# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A deliberately minimal to-do app: one file, [index.html](index.html), with inline CSS and vanilla JS. No frameworks, no build step, no package manager, no tests, no linter. Keep it that way: new features go into the same file.

## Running

Open `index.html` in a browser. To test saving (localStorage), serve it over http, because `file:` and `data:` URLs (including the Claude desktop browser pane) block localStorage:

```bash
python3 -m http.server 8765
```

## Architecture

- **State**: a `todos` array of `{id, text, done, added, category, due}`. `nextId` is derived from the max saved id on load.
- **Rendering**: `render()` is the single path that updates the UI. It first calls `save('todos', todos)`, then applies the category filter and sort, then rebuilds the `<ul>` from scratch. Any state change (add, edit, delete, toggle) mutates `todos` and then calls `render()`, so persistence comes along with it.
- **Events**: one delegated click listener on the `<ul>` handles edit (the `.edit` button, via three `prompt()` calls with validation), delete (any other button) and checkbox toggle. Adding goes through the form `submit` handler.
- **Persistence**: `load(key, fallback)` / `save(key, value)` wrap localStorage in try/catch so the app still works when storage is blocked. Keys: `todos`, `theme`.
- **Theming**: CSS variables on `:root`, overridden by `:root[data-theme=dark]`. `applyTheme()` sets `documentElement.dataset.theme` and saves it; the initial value falls back to the system `prefers-color-scheme`. Use the variables, not hard-coded colors, for anything new.
- **Categories** are a fixed list (Work, Personal, Others) that is duplicated in three places: the `#category` dropdown, the `#filter` dropdown and the `CATEGORIES` constant used for edit validation. Change all three together.
- **Mobile**: a `@media (max-width: 480px)` block moves the dates onto their own line and enlarges tap targets.

## Mockups

`mockups/` holds screenshots of the app at iPhone (393x852), Galaxy S25 (360x780) and iPad 11" (834x1194) sizes, plus `iphone.html`, `galaxy-s25.html` and `ipad-11.html`, which embed those images as base64 data URIs. Those HTML files have very long lines, so do not Read them in full; use grep or a scripted replace. The images are emulated-viewport screenshots, not real device photos, and they do not update when `index.html` changes.
