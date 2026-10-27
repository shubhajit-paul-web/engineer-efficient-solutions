# Production Algorithms & DSA

A language-agnostic skill that helps coding agents choose, implement, and verify suitable algorithms and data structures for real applications.

## What it does

- Derives decisions from the actual requirements, input sizes, and project constraints.
- Checks algorithm preconditions, correctness, termination, and consequential edge cases.
- Accounts for hidden CPU work, peak memory, database access, I/O, and concurrency.
- Uses focused tests, independent reference implementations, and measurements where appropriate.
- Keeps straightforward tasks lightweight and recognizes conflicting or infeasible requirements.

Designed for meaningful data processing, search, sorting, deduplication, aggregation, scheduling, dependencies, traversal, streaming, performance investigations, and related correctness bugs. It adapts to the existing language, runtime, and stack. Agent activation depends on the host's skill support and configuration; automatic invocation is enabled in the included metadata.

The core instructions load first. Three targeted references provide additional guidance only when relevant. This is an instruction package, with no runtime dependencies, executable CLI, or installation hooks. Agent output still requires appropriate engineering review.

## Install from GitHub

After this repository is public, replace `shubhajit-paul-web` with its owner:

```sh
npx skills add shubhajit-paul-web/engineer-efficient-solutions --skill engineer-efficient-solutions
```

Select the target agent when prompted. Use `-g` for installation across projects. The current Skills CLI requires Node.js 22.20 or newer; the skill's guidance is not restricted to JavaScript.

For local installation from the extracted repository:

```sh
npx skills add . --skill engineer-efficient-solutions
```

Run the local command from this repository's root, or replace `.` with its absolute path from another project. Keep the references alongside `SKILL.md`.

## Use

Relevant coding tasks can activate the skill automatically on supporting hosts. To request it explicitly, use your agent's skill invocation syntax; for example, `$engineer-efficient-solutions` in Codex.

Example requests:

- "Review this deduplication job for correctness and memory growth."
- "Optimize this endpoint while preserving result order and duplicate handling."
- "Choose a suitable structure for incremental top-k results with these constraints."

## Package contents

| Path                                                                            | Purpose                                                       |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| `skills/engineer-efficient-solutions/SKILL.md`                                  | Activation criteria and engineering workflow                  |
| `skills/engineer-efficient-solutions/references/algorithm-selection.md`         | Pattern selection, preconditions, and common traps            |
| `skills/engineer-efficient-solutions/references/runtime-and-data-boundaries.md` | Runtime semantics, memory, databases, concurrency, and caches |
| `skills/engineer-efficient-solutions/references/verification.md`                | Boundary cases, correctness checks, and performance evidence  |
| `skills/engineer-efficient-solutions/agents/openai.yaml`                        | Optional agent metadata with implicit invocation enabled      |

## Publish on GitHub and share through skills.sh

1. Extract the source archive and open its `engineer-efficient-solutions` folder.
2. Review the MIT license and its copyright attribution.
3. Create an empty public GitHub repository named `engineer-efficient-solutions` under your account.
4. Run these commands after replacing `shubhajit-paul-web`:

```sh
git init -b main
git add .
git commit -m "Initial release"
git remote add origin https://github.com/shubhajit-paul-web/engineer-efficient-solutions.git
git push -u origin main
```

Users can then install through the GitHub command above. skills.sh discovery and ranking use installation telemetry; directory visibility is not guaranteed to appear immediately. A separate npm publication is not needed for GitHub-based installation.

## Publish on npm (optional)

Use an npm account or organization scope you control. Replace `YOUR_NPM_USERNAME` with that scope, using lowercase where required. The default unscoped package name has not been reserved or checked for availability.

```sh
npm pkg set name="@YOUR_NPM_USERNAME/engineer-efficient-solutions"
npm login
npm pack --dry-run --ignore-scripts
npm publish --access public --ignore-scripts
```

Complete npm's authentication prompts, including two-factor authentication when required. Add your author and GitHub repository metadata to `package.json` before publishing if desired. Commit any packaging changes to GitHub.

Installing the npm package places the skill files in `node_modules`; register them with the agent separately. From a consumer project, after the scoped package is published:

```sh
npm install --save-dev @YOUR_NPM_USERNAME/engineer-efficient-solutions
npx skills add ./node_modules/@YOUR_NPM_USERNAME/engineer-efficient-solutions --skill engineer-efficient-solutions
```

## Contribute

Include a concrete task, observed failure, and expected behavior with proposed changes. Keep the core concise, preserve relative reference links, and check meaningful boundary cases when changing algorithm guidance. Avoid unsupported performance claims and claims that the skill eliminates the need for human judgment.

## License

MIT. See [LICENSE](LICENSE).

## Publishing references

- [Skills repository layout](https://github.com/vercel-labs/skills/blob/main/docs/creating-skills.md)
- [Skills CLI commands](https://github.com/vercel-labs/skills/blob/main/docs/commands.md)
- [skills.sh documentation](https://www.skills.sh/docs)
- [Publishing a public scoped npm package](https://docs.npmjs.com/creating-and-publishing-scoped-public-packages/)
