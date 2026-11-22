# Lessons

This repository only contains lessons for the Angular Workshop.
To understand how to structure lessons, in order to create and update them correctly, refer to [lessons.md](./docs/lessons.md).

# Slides

Currently, no slidev slides are planned in this repository.
Slides live at Google Slides.
Topics are spread among different presentations.

Use `./.claude/skills/google-slides-workshop-teacher` to learn how slides are updated.

# Commit Conventions

This repo enforces [Conventional Commits](https://www.conventionalcommits.org/) via a Husky `commit-msg` hook
(commitlint) plus a GitHub Actions backstop. Run `npm install` once to activate the local hook. Details and
examples: [commit-conventions.md](./docs/commit-conventions.md).

# Sub-Modules

## Sample Solution

The `sample-solution` directory (a git submodule pointing to `git@github.com:workshops-de/angular-advanced-workshop.git`) contains the sample solutions for the lessons' tasks. Each commit in that submodule is the solution to one task.

## Slidev

The `slidev-slides` Contain the first version of the advanced Angular workshop. They have contents about structuring the code, state management with Signal Store, as well as runtime configuration. These slides are here as a source for creating the Google Slides.


<!-- nx configuration start-->
<!-- Leave the start & end comments to automatically receive updates. -->

## General Guidelines for working with Nx

- For navigating/exploring the workspace, invoke the `nx-workspace` skill first - it has patterns for querying projects, targets, and dependencies
- When running tasks (for example build, lint, test, e2e, etc.), always prefer running the task through `nx` (i.e. `nx run`, `nx run-many`, `nx affected`) instead of using the underlying tooling directly
- Prefix nx commands with the workspace's package manager (e.g., `pnpm nx build`, `npm exec nx test`) - avoids using globally installed CLI
- You have access to the Nx MCP server and its tools, use them to help the user
- For Nx plugin best practices, check `node_modules/@nx/<plugin>/PLUGIN.md`. Not all plugins have this file - proceed without it if unavailable.
- NEVER guess CLI flags - always check nx_docs or `--help` first when unsure

## Scaffolding & Generators

- For scaffolding tasks (creating apps, libs, project structure, setup), ALWAYS invoke the `nx-generate` skill FIRST before exploring or calling MCP tools

## When to use nx_docs

- USE for: advanced config options, unfamiliar flags, migration guides, plugin configuration, edge cases
- DON'T USE for: basic generator syntax (`nx g @nx/react:app`), standard commands, things you already know
- The `nx-generate` skill handles generator discovery internally - don't call nx_docs just to look up generator syntax


<!-- nx configuration end-->