# Contributing to LiquidWalls

Thank you for taking the time to contribute. LiquidWalls is a proprietary project
maintained by Adisoft. External contributions are welcome in the forms described
below, but all submitted code must be compatible with the project licence.

---

## Table of contents

1. [Code of conduct](#code-of-conduct)
2. [What you can contribute](#what-you-can-contribute)
3. [Reporting a bug](#reporting-a-bug)
4. [Suggesting a feature](#suggesting-a-feature)
5. [Submitting a pull request](#submitting-a-pull-request)
6. [Coding standards](#coding-standards)
7. [Commit messages](#commit-messages)
8. [Legal](#legal)

---

## Code of conduct

All participants are expected to follow the [Code of Conduct](CODE_OF_CONDUCT.md).
Disrespectful or harassing behaviour will result in your contributions being
closed without review.

---

## What you can contribute

| Type | Welcome? |
|---|---|
| Bug reports | Yes — always |
| Feature requests | Yes — open a discussion first |
| Documentation fixes | Yes |
| Typo / grammar fixes | Yes |
| Performance improvements | Yes, with benchmarks |
| New wallpaper categories or assets | Please open an issue first |
| New third-party SDK integrations | No — contact Adisoft directly |
| Translations / localisation strings | Yes — see below |

---

## Reporting a bug

1. Search [existing issues](../../issues) to avoid duplicates.
2. Open a new issue using the **Bug report** template.
3. Include:
   - Android version and device model
   - LiquidWalls version (Settings → About → Version)
   - Steps to reproduce
   - Expected vs actual behaviour
   - Logcat output if available (filter by `LiquidWalls`)
4. Do **not** include personal data or keystore files in your report.

---

## Suggesting a feature

1. Open a new issue using the **Feature request** template.
2. Describe the problem you want to solve, not just the solution.
3. If the request is accepted, Adisoft will label it `planned` and it may be
   included in a future release or delegated to the community.

---

## Submitting a pull request

### Before you start

- Open an issue or comment on an existing one to discuss the change. This saves
  everyone time if the direction needs to change.
- For anything beyond a trivial fix, wait for a maintainer to label it
  `accepting-prs` before investing significant effort.

### Steps

1. Fork the repository.
2. Create a branch named `fix/<short-description>` or `feat/<short-description>`.
3. Make your changes (see [Coding standards](#coding-standards)).
4. Run a debug build locally to confirm the app compiles:
   ```bash
   bash scripts/build-debug-apk.sh
   ```
5. Push your branch and open a pull request against `main` using the PR template.
6. Respond to review feedback promptly. PRs with no activity for 30 days may be
   closed.

### What happens next

- A maintainer will review within 7 business days.
- If approved, Adisoft merges and credits you in `CHANGELOG.md`.
- If declined, the issue will be explained in the review comments.

---

## Coding standards

- **Language:** Kotlin only. No Java in new files.
- **UI:** Jetpack Compose. No XML layouts in new screens.
- **Style:** Follow the existing code style. Enable `ktlint` locally if available.
- **Architecture:** MVVM + Repository pattern. Keep ViewModels free of Android
  framework imports other than `androidx.lifecycle`.
- **No hardcoded strings** visible to users — add them to `strings.xml`.
- **No hardcoded IDs or keys** — use `liquidwalls.properties` + `BuildConfig`.
- **Thread safety:** All database and network calls must be done off the main
  thread. Use Kotlin coroutines (`Dispatchers.IO`).
- **Memory:** Avoid holding Context references in long-lived objects.
- **Imports:** Do not use wildcard imports (`import foo.bar.*`) in new files.

---

## Commit messages

Follow the [Conventional Commits](https://www.conventionalcommits.org/) format:

```
<type>(<scope>): <short summary>

[optional body]

[optional footer]
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`

Examples:
```
fix(search): handle null color metadata gracefully
feat(categories): add Underwater category with 12 wallpapers
docs(readme): update build instructions for AAB
```

Commit messages must be in English.

---

## Legal

By submitting a pull request you agree that:

1. Your contribution is your original work.
2. You grant Adisoft a perpetual, irrevocable, worldwide, royalty-free licence to
   use, modify, and distribute your contribution as part of LiquidWalls under any
   licence Adisoft chooses, including proprietary licences.
3. You will not hold Adisoft liable for any use of your contribution.

If you are contributing on behalf of an employer, ensure you have the right to
assign the above licence.
