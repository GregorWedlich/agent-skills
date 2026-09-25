# agent-skills

Skills for coding agents that read `SKILL.md` files, such as Claude Code and opencode.

## build-it-yourself

You want to build a feature yourself and don't know where to start. Instead of finished code, the agent walks you through it the way a developer would think it through: first what you want to see and where the data comes from, then an empty shell that already shows up, then one small step at a time. Each step has the question you'd ask yourself, what's new and exactly where it goes, the whole file in its current state, and what you should see when you check. You type, the agent explains, and once you're done it runs the project's checks.

It works for any language and any layer. The agent reads the project's own instructions (`AGENTS.md`, `CLAUDE.md`) and reference files before it writes a step.

```bash
npx skills add https://github.com/GregorWedlich/agent-skills --skill build-it-yourself
```

## License

MIT
