# Frontend

The ladder for how you render the UI. Start at server-rendered HTML; climb only
when a stated requirement forces it.

## server-rendered-html

Plain HTML rendered on the server, served as full-page loads.

- **Use when:** pages are mostly content and forms; full-page navigation is
  acceptable; little to no client-side interactivity.
- **Climb when:** you need partial page updates or interactivity without full
  reloads — sorting, inline edits, live-filtering, dynamic fragments.
- **Cost:** none beyond your server templates. No build step, no JS toolchain,
  no client state to reason about.
- **Rationale template:** `"chose server-rendered HTML — {content-and-forms UI},
  {full-page loads fine}, {no client interactivity}"`

## html-plus-htmx

Server-rendered HTML with small sprinkles of behavior (HTMX/Alpine) for partial
updates.

- **Use when:** you need partial updates and modest interactivity but the server
  still owns the rendering and state; a handful of dynamic fragments.
- **Climb when:** the UI is genuinely app-like — heavy client state, offline,
  complex routing, or rich components that the server cannot drive cleanly.
- **Cost:** a small JS dependency and a few extra endpoints returning fragments;
  no full build pipeline, server stays the source of truth.
- **Rationale template:** `"chose HTML + HTMX — {partial updates needed},
  {server owns state}, {no app-like UI}"`

## spa-framework

A client-side single-page application framework (React, Vue, Svelte).

- **Use when:** the UI is a stateful application — rich client state, complex
  client routing, offline support, or heavily interactive components.
- **Climb when:** (top of this ladder) — native/mobile shells or micro-frontends
  are a separate, later decision driven by stated needs.
- **Cost:** a build toolchain, a JS bundle to ship and maintain, and client state
  to manage. Real maintenance burden — check it against the ceiling.
- **Rationale template:** `"chose SPA framework — {rich client state},
  {complex client routing}, {app-like interactivity}"`
