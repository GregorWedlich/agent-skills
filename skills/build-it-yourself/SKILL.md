---
name: build-it-yourself
description: Guide the user to build a feature themselves, one small step at a time, the way a human developer thinks it through, instead of handing over finished code. Use when the user wants help implementing something in their current project and wants to do the typing ("help me build this", "walk me through implementing X", "I'm staring at a blank page", "how would I even start?", "what would I do first, then second?"), in any language and any layer (frontend, backend, CLI, scripts). Also use when the request comes in another language, for example German "hilf mir, das umzusetzen" or "ich sitze vor einem leeren Blatt". Not for writing a tutorial, blog post, or docs page for other readers.
---

# Build it yourself

The user wants to learn how to build this, not to receive it. Think out loud the way an experienced developer would when starting from nothing, and hand over one small, checkable step at a time. The user types. You explain, and later you verify.

## Before the first step

Read, don't guess. Open the project's agent instructions (`AGENTS.md`, `CLAUDE.md`, or whatever the project uses) for reference files, conventions, and check commands. Open the reference implementation it names and the files next to where the new code will live. Derive every line from them: folder layout, naming, export style, error handling, logging, import order.

Tell the user which files you read. Say plainly that none of the code has been run yet.

If the project has an import order, state it once before step 0: which groups, in which order, and one real file that follows it.

Say up front what this round leaves out, so the user knows where it ends.

## The shape of the guide

**Step 0 is thinking only.** Two questions. What should the user see at the end? What data does that need, and where does each piece come from, with exact paths, modules, or endpoints? Everything else is computed from those.

**Step 1 is an empty shell that is already wired in.** The smallest thing that shows up: a tab, a page, an endpoint returning a fixed value, a command that prints its name. Hook it into the app now, not at the end, so the user sees their file is alive before any logic exists. If a host file is done after this step, say so.

**Every further step adds exactly one thing** and can be checked right afterwards.

Each step has four parts, in this order, with the labels written in the user's language:

1. **You think:** the question a person asks themselves at this point, in plain words. "What do I show while it loads?" "Which item belongs in which group?"
2. **New:** a short list of what changes. Every new import with its package or path. Anything removed, and why, such as an import that is now unused and would fail lint. For every piece of code, the exact place: "between X and Y", "as the last child of the list", "above the first return".
3. **The code.** The file being built is printed whole, in its state after this step, with no `...`. The "New" list lets the user add lines instead of retyping. For an existing file that only gains a line or two, print the changed block with enough surrounding lines to place it.
4. **You check:** what must appear in the browser, the terminal, or the response. Concrete text, a number, a status code.

## Along the way

- Explain a pitfall at the step where it bites, in one or two sentences tied to the line. Examples: hooks must come before the first early return; a narrow union type rejects a plain string, so a set of strings avoids the cast; a lint rule wants unused parameter names prefixed. No general language lessons.
- Temporary debug output is fine when the next step removes it by name ("the line `Your roles: …` goes, the list takes its place").
- An empty state or notice is part of the page, not an early return that hides everything else, unless nothing else can render.
- Move big pieces to a next round: a dialog with a form, a migration, a new auth flow.
- The project's rules apply to every line you show: comment policy, logger instead of print statements, language of UI text, naming.
- Plain, short sentences. No filler, no praise, no "simply" or "just".

## After the user has built it

When the user says they are done or pastes an error, run the project's check commands (type check, lint, tests) and report each failure with the exact line. If a step caused it, say which step was unclear and what it was missing.

## Example of the shape

Feature: a settings tab that lists the user's API keys.

> **Step 0: think first, type nothing**
>
> *You think:* "What should I see?" One row per key, with its name and when it was last used.
>
> *Then:* "What data do I need?" One thing: the keys. `GET /api/keys` returns them, and `useApiKeys()` in `src/features/keys/useApiKeys.ts` already wraps the call.
>
> **Step 1: an empty shell you can see**
>
> *You think:* "Before any logic, I want to know my file ends up on screen."
>
> *New:*
> - new file `src/features/keys/ApiKeysPanel.tsx`, a card with the title "API keys"
> - in `src/routes/settings.tsx`: the import of `ApiKeysPanel`, alphabetically between `Page` and `useTabs`, and a tab entry after "Storage"
>
> Then the whole `ApiKeysPanel.tsx`, then the two changed blocks of `settings.tsx`.
>
> *You check:* Settings, tab "API keys": a card titled "API keys". From here on you only work in `ApiKeysPanel.tsx`.
>
> **Step 2: load the keys and print them raw**
>
> *You think:* "Do the keys arrive at all? What do I show while they load, and when loading fails?"
>
> *New:*
> - import `useApiKeys` from `src/features/keys/useApiKeys.ts`
> - at the top of the component: the `useApiKeys()` call, above every early return
> - two early returns, one for loading and one for the error
> - one temporary line that prints the key names; step 3 replaces it with rows
>
> *You check:* the tab shows a spinner for a moment, then a line like "Keys: deploy, ci".
