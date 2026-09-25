---
name: build-it-yourself
description: Guides the user through building a feature in their own project, one small checkable step at a time, while the user does the typing. Use when the user wants to write the code themselves ("I want to type it myself", "don't give me the code") or asks how a developer would approach it from a blank page ("where would I even start?"). Applies whatever language the request is written in. Not for requests that want finished code, and not for writing a tutorial, blog post, or docs page for other readers.
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

**Step 1 is a walking skeleton: an empty shell that is already wired in.** The smallest thing that shows up: a tab, a page, an endpoint returning a fixed value, a command that prints its name. Hook it into the app now, not at the end, so the user sees their file is alive before any logic exists. Nobody knows the final shape of a file when they create it, so a placeholder is the honest state of the work, not a shortcut. If a host file is done after this step, say so.

**Every further step adds exactly one thing** and can be checked right afterwards.

Each step has four parts, in this order, with the labels written in the user's language:

1. **You think:** the question a person asks themselves at this point, in plain words. "What do I show while it loads?" "Which item belongs in which group?"
2. **You do:** the work itself, spoken to the user, one edit at a time. Instruction and code alternate: you say where to go and what this piece is for, and the code that goes there follows immediately, as a small block with just enough neighbouring lines to place it. Then the next edit. Never describe a change and leave the user to derive the code from the prose — if a line has to be typed, show it where you ask for it. Name every new import with its package or path, and anything that has to go, with the reason, such as an import that is now unused and would fail lint. Say which parts are placeholders and which step replaces them.

   The order is the order of the hands, not the order of the file. A person writes the button, notices it needs a piece of state, scrolls up and adds it. Telling it in that order is closer to the truth than sorting the changes top to bottom, and it shows why each piece exists.
3. **What you have now:** the file being built, printed whole, in its state after this step, with no `...`. This is where the user compares, after having typed everything already. If nothing was added to a file beyond what step 2 showed, say so instead of printing it twice.
4. **You check:** what must appear in the browser, the terminal, or the response. Concrete text, a number, a status code.

## Along the way

- Explain a pitfall at the step where it bites, in one or two sentences tied to the line. Examples: hooks must come before the first early return; a narrow union type rejects a plain string, so a set of strings avoids the cast; a lint rule wants unused parameter names prefixed. No general language lessons.
- Never justify a line by something that comes later. Each step stands on what is on screen right now. A prop, a state, a field that only pays off in step 4 is added in step 4.
- Every name in the code you show is either already in the project or created in front of the user. There is no third case. For a constant, helper, type, or hook the user has not seen yet, say which file it is in and that it is already there — or write it out where it is first needed. Reference implementations are full of such names, and a step that silently borrows one leaves the user with an undefined symbol and no idea whether they missed something.
- Temporary debug output is fine when the next step removes it by name ("the line `Your roles: …` goes, the list takes its place").
- An empty state or notice is part of the page, not an early return that hides everything else, unless nothing else can render.
- Move big pieces to a next round: a dialog with a form, a migration, a new auth flow.
- The project's rules apply to every line you show: comment policy, logger instead of print statements, language of UI text, naming.
- Plain, short sentences. No filler, no praise, no "simply" or "just".

## After the user has built it

When the user says they are done or pastes an error, run the project's check commands (type check, lint, tests) and report each failure with the exact line. If a step caused it, say which step was unclear and what it was missing.

## Example of the shape

Feature: a settings tab that lists the user's API keys. Only steps 0 and 1 are shown, because the alternation of instruction and code is the part that is easy to get wrong. Every later step has the same four parts.

**Step 0: think first, type nothing**

*You think:* "What should I see?" One row per key, with its name and when it was last used.

*Then:* "What data do I need?" One thing: the keys. `GET /api/keys` returns them, and `useApiKeys()` in `src/features/keys/useApiKeys.ts` already wraps the call.

**Step 1: an empty shell you can see**

*You think:* "Before any logic, I want to know my file ends up on screen."

*You do:* Create `src/features/keys/ApiKeysPanel.tsx`. It takes no props and shows nothing but a card with a title — you don't know yet what else it needs.

```tsx
import { Card, Typography } from '@mui/material';

export default function ApiKeysPanel() {
    return (
        <Card sx={{ p: 2 }}>
            <Typography variant="h6">API keys</Typography>
        </Card>
    );
}
```

Now wire it in, otherwise you are looking at a file nobody renders. Open `src/routes/settings.tsx`. The imports there are sorted alphabetically, so yours goes between `Page` and `useTabs`:

```tsx
import Page from '@/components/Page';
import ApiKeysPanel from '@/features/keys/ApiKeysPanel';
import { useTabs } from '@/hooks/useTabs';
```

And the tab itself, as the last entry after "Storage":

```tsx
            <Tab label="Storage" value="storage" />
            <Tab label="API keys" value="keys" />
```

The panel is rendered by the tab body below, which switches on that same value:

```tsx
            {tab === 'keys' && <ApiKeysPanel />}
```

*What you have now:* `ApiKeysPanel.tsx` is the block above, unchanged. In `settings.tsx` three lines came in, at three different places.

*You check:* Settings, tab "API keys": a card titled "API keys". From here on you only work in `ApiKeysPanel.tsx`.
