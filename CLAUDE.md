# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A deliberately minimal to-do app: one file, `index.html`, with inline CSS and JS. No frameworks, no build step, no dependencies, no tests, no linter. Keep it that way unless asked otherwise.

## Running and testing

- Open `index.html` directly in a browser, or serve the folder (e.g. `python3 -m http.server 8765`) and visit `http://127.0.0.1:8765/`.
- Test over a local server, not a `data:`/snapshot URL: `localStorage` is per origin and is blocked in the browser pane's `data:` snapshots, so persistence can't be verified there.
- Testing on a localhost origin writes to the real saved list (`todo-tasks`) and theme (`todo-theme`) for that origin. Remove any test tasks and restore the theme afterwards. Stop the server when done.
- For mobile checks, emulate 393×852 (iPhone) and 360×780 (Galaxy S25) and confirm there is no horizontal overflow.

## Architecture (`index.html`)

- **State**: `tasks` is an array of `{id, text, done, category, due}`. `nextId` is max id + 1. `editingId` marks the row currently in edit mode.
- **Single render path**: every change mutates `tasks`/`editingId` and calls `render()`, which calls `save()` (writes `localStorage['todo-tasks']`), then rebuilds the whole `<ul>` with DOM APIs and `textContent` (never `innerHTML` with user text).
- **Events**: one delegated `click` listener on the list handles delete, edit and checkbox. `keydown` (Enter saves, Escape cancels) and `focusout` handle editing; `focusout` ignores focus moving between fields of the same row. `finishEdit(save, li)` commits or cancels.
- **Categories**: fixed list `CATEGORIES` (Work, Personal, Other), filled by `fillCategories()` for both the add form and the edit row. An empty "Category" option means none. A saved category not in the list (legacy free text) is kept as an extra option rather than dropped.
- **Theme**: `data-theme` on `<html>` drives CSS variables (`:root` light, `:root[data-theme=dark]`). The choice is stored in `localStorage['todo-theme']`; with no saved choice it follows `prefers-color-scheme`. All storage access is wrapped in try/catch so the app still works when storage is unavailable.
- **Mobile constraints**: inputs/selects use `font-size: 16px` (prevents iOS Safari zoom on focus), and buttons have a 36px minimum tap size. Preserve both.

## Mock-ups

`mockups/mockup.html` shows `iphone-393x852.jpg` and `galaxy-s25-360x780.jpg` (screenshots of the app at those sizes) inside phone frames. The images are static: re-take them if the app's look changes.
