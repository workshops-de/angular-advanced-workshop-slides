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

The `sample-solution` directory (in the `angular-workshop` repository, added there as a git submodule pointing to `git@github.com:workshops-de/angular-workshop.git`) contains the sample solutions for the lessons' tasks. Each commit in that submodule is the solution to one task.

## Slidev

The `slidev-slides` Contain the first version of the advanced Angular workshop. They have contents about structuring the code, state management with Signal Store, as well as runtime configuration. These slides are here as a source for creating the Google Slides.
