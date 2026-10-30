# Getting into Claude Code (no terminal required)

The build happens in Claude Code. You don't need the scary terminal.

## Easiest: the Code tab in the Claude desktop app

You very likely already have the Claude desktop app. At the top there are tabs: **Chat · Cowork · Code**. Click **Code**. That *is* Claude Code, built into the app.

1. Open the **Code** tab.
2. Choose **Local** and pick (or create) a folder for your project.
3. You're in. You can now ask Claude to build, and it can create files, run things, and use skills like `/idea-plan-launch`.

That's the whole on-ramp for most people. No install, no command line.

## For power users: the terminal + superpowers

If you're comfortable in a terminal, install Claude Code as the CLI. The terminal is also the only place the **superpowers** plugin reliably works — it adds the brainstorm → plan → test-driven workflow that makes builds much more reliable.

- Install Claude Code: see https://docs.claude.com/claude-code
- Install the superpowers plugin from its official marketplace (follow the current instructions in the superpowers repo: https://github.com/obra/superpowers). The flow is roughly: add the marketplace, then install the plugin, then restart Claude Code.

If you're in the Code tab and superpowers isn't available, that's expected — just follow the same discipline manually: brainstorm the design first, write a short plan, then build and test in small steps.

## What's a CLAUDE.md?

A `CLAUDE.md` is a short file in your project that tells Claude (and the next human) what the project is and how to work in it. This skill helps you generate one at the end — it's also what you hand to the AI team. Don't worry about it up front.

## You can't break anything

Building in Claude Code on your own folder is safe to experiment in. You're not touching the live server: nothing reaches it until your app is on the deploy pipeline and you merge a pull request into `main`.
