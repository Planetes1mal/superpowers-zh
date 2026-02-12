# Superpowers

<p align="left">
  <a href="README.md">简体中文</a> |
  <a href="README.en.md">English</a>
</p>

Superpowers is a complete software development workflow for your coding agents, built on top of a set of composable "skills" and some initial instructions that make sure your agent uses them.

## How it works

It starts from the moment you fire up your coding agent. As soon as it sees that you're building something, it *doesn't* just jump into trying to write code. Instead, it steps back and asks you what you're really trying to do. 

Once it's teased a spec out of the conversation, it shows it to you in chunks short enough to actually read and digest. 

After you've signed off on the design, your agent puts together an implementation plan that's clear enough for an enthusiastic junior engineer with poor taste, no judgement, no project context, and an aversion to testing to follow. It emphasizes true red/green TDD, YAGNI (You Aren't Gonna Need It), and DRY. 

Next up, once you say "go", it launches a *subagent-driven-development* process, having agents work through each engineering task, inspecting and reviewing their work, and continuing forward. It's not uncommon for Claude to be able to work autonomously for a couple hours at a time without deviating from the plan you put together.

There's a bunch more to it, but that's the core of the system. And because the skills trigger automatically, you don't need to do anything special. Your coding agent just has Superpowers.


## Sponsorship

If Superpowers has helped you do stuff that makes money and you are so inclined, I'd greatly appreciate it if you'd consider [sponsoring my opensource work](https://github.com/sponsors/obra).

Thanks! 

- Jesse


## Installation

**Note:** Installation differs by platform. Claude Code has a built-in plugin system. Codex and OpenCode require manual setup.

### Claude Code (via Plugin Marketplace)

In Claude Code, register the marketplace first:

```bash
/plugin marketplace add obra/superpowers-marketplace
```

Then install the plugin from this marketplace:

```bash
/plugin install superpowers@superpowers-marketplace
```

### Verify Installation

Start a new session and ask Claude to help with something that would trigger a skill (e.g., "help me plan this feature" or "let's debug this issue"). Claude should automatically invoke the relevant superpowers skill.

### Codex

Tell Codex:

```
Fetch and follow instructions from https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.codex/INSTALL.md
```

**Detailed docs:** [docs/README.codex.md](docs/README.codex.md)

### OpenCode

Tell OpenCode:

```
Fetch and follow instructions from https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.opencode/INSTALL.md
```

**Detailed docs:** [docs/README.opencode.md](docs/README.opencode.md)

## The Basic Workflow

1. **brainstorming** - Activates before writing code. Refines rough ideas through questions, explores alternatives, presents design in sections for validation. Saves design document.

2. **using-git-worktrees** - Activates after design approval. Creates isolated workspace on new branch, runs project setup, verifies clean test baseline.

3. **writing-plans** - Activates with approved design. Breaks work into bite-sized tasks (2-5 minutes each). Every task has exact file paths, complete code, verification steps.

4. **subagent-driven-development** or **executing-plans** - Activates with plan. Dispatches fresh subagent per task with two-stage review (spec compliance, then code quality), or executes in batches with human checkpoints.

5. **test-driven-development** - Activates during implementation. Enforces RED-GREEN-REFACTOR: write failing test, watch it fail, write minimal code, watch it pass, commit. Deletes code written before tests.

6. **requesting-code-review** - Activates between tasks. Reviews against plan, reports issues by severity. Critical issues block progress.

7. **finishing-a-development-branch** - Activates when tasks complete. Verifies tests, presents options (merge/PR/keep/discard), cleans up worktree.

**The agent checks for relevant skills before any task.** Mandatory workflows, not suggestions.

## What's Inside

### Skills Library

**Testing**

| Skill                                                        | Description                                                  | Files                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [test-driven-development](skills/test-driven-development/SKILL.md) | Use when implementing any feature or bugfix, before writing implementation code | [SKILL.md](skills/test-driven-development/SKILL.md) <br />[testing-anti-patterns.md](skills/test-driven-development/testing-anti-patterns.md) |

**Debugging**

| Skill                                                        | Description                                                  | Files                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [systematic-debugging](skills/systematic-debugging/SKILL.md) | Use when encountering any bug, test failure, or unexpected behavior, before proposing fixes | [SKILL.md](skills/systematic-debugging/SKILL.md) <br />[root-cause-tracing.md](skills/systematic-debugging/root-cause-tracing.md) <br />[defense-in-depth.md](skills/systematic-debugging/defense-in-depth.md) <br />[condition-based-waiting.md](skills/systematic-debugging/condition-based-waiting.md) <br />[CREATION-LOG.md](skills/systematic-debugging/CREATION-LOG.md) <br />[test-academic.md](skills/systematic-debugging/test-academic.md) <br />[test-pressure-1.md](skills/systematic-debugging/test-pressure-1.md) <br />[test-pressure-2.md](skills/systematic-debugging/test-pressure-2.md) <br />[test-pressure-3.md](skills/systematic-debugging/test-pressure-3.md) |
| [verification-before-completion](skills/verification-before-completion/SKILL.md) | Use when about to claim work is complete, fixed, or passing, before committing or creating PRs | [SKILL.md](skills/verification-before-completion/SKILL.md)   |

**Collaboration**

| Skill                                                        | Description                                                  | Files                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [brainstorming](skills/brainstorming/SKILL.md)               | You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior | [SKILL.md](skills/brainstorming/SKILL.md)                    |
| [writing-plans](skills/writing-plans/SKILL.md)               | Use when you have a spec or requirements for a multi-step task, before touching code | [SKILL.md](skills/writing-plans/SKILL.md)                    |
| [executing-plans](skills/executing-plans/SKILL.md)           | Use when you have a written implementation plan to execute in a separate session with review checkpoints | [SKILL.md](skills/executing-plans/SKILL.md)                  |
| [dispatching-parallel-agents](skills/dispatching-parallel-agents/SKILL.md) | Use when facing 2+ independent tasks that can be worked on without shared state or sequential dependencies | [SKILL.md](skills/dispatching-parallel-agents/SKILL.md)      |
| [subagent-driven-development](skills/subagent-driven-development/SKILL.md) | Use when executing implementation plans with independent tasks in the current session | [SKILL.md](skills/subagent-driven-development/SKILL.md) <br />[implementer-prompt.md](skills/subagent-driven-development/implementer-prompt.md) <br />[spec-reviewer-prompt.md](skills/subagent-driven-development/spec-reviewer-prompt.md) <br />[code-quality-reviewer-prompt.md](skills/subagent-driven-development/code-quality-reviewer-prompt.md) |
| [requesting-code-review](skills/requesting-code-review/SKILL.md) | Use when completing tasks, implementing major features, or before merging to verify work meets requirements | [SKILL.md](skills/requesting-code-review/SKILL.md) <br />[code-reviewer.md](skills/requesting-code-review/code-reviewer.md) |
| [receiving-code-review](skills/receiving-code-review/SKILL.md) | Use when receiving code review feedback, before implementing suggestions | [SKILL.md](skills/receiving-code-review/SKILL.md)            |
| [using-git-worktrees](skills/using-git-worktrees/SKILL.md)   | Use when starting feature work that needs isolation from current workspace or before executing implementation plans | [SKILL.md](skills/using-git-worktrees/SKILL.md)              |
| [finishing-a-development-branch](skills/finishing-a-development-branch/SKILL.md) | Use when implementation is complete, all tests pass, and you need to decide how to integrate the work | [SKILL.md](skills/finishing-a-development-branch/SKILL.md)   |

**Meta**

| Skill                                                  | Description                                                  | Files                                                        |
| ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [writing-skills](skills/writing-skills/SKILL.md)       | Use when creating new skills, editing existing skills, or verifying skills work before deployment | [SKILL.md](skills/writing-skills/SKILL.md) <br />[anthropic-best-practices.md](skills/writing-skills/anthropic-best-practices.md) <br />[persuasion-principles.md](skills/writing-skills/persuasion-principles.md) <br />[testing-skills-with-subagents.md](skills/writing-skills/testing-skills-with-subagents.md) <br />[examples/CLAUDE_MD_TESTING.md](skills/writing-skills/examples/CLAUDE_MD_TESTING.md) |
| [using-superpowers](skills/using-superpowers/SKILL.md) | Use when starting any conversation - establishes how to find and use skills | [SKILL.md](skills/using-superpowers/SKILL.md)                |

## Philosophy

- **Test-Driven Development** - Write tests first, always
- **Systematic over ad-hoc** - Process over guessing
- **Complexity reduction** - Simplicity as primary goal
- **Evidence over claims** - Verify before declaring success

Read more: [Superpowers for Claude Code](https://blog.fsck.com/2025/10/09/superpowers/)

## Contributing

Skills live directly in this repository. To contribute:

1. Fork the repository
2. Create a branch for your skill
3. Follow the `writing-skills` skill for creating and testing new skills
4. Submit a PR

See `skills/writing-skills/SKILL.md` for the complete guide.

## Updating

Skills update automatically when you update the plugin:

```bash
/plugin update superpowers
```

## License

MIT License - see LICENSE file for details

## Support

- **Issues**: https://github.com/obra/superpowers/issues
- **Marketplace**: https://github.com/obra/superpowers-marketplace
