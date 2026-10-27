# Field Guides

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A working collection of practical, tested guides — the kind of documentation I wish existed the first time I needed it. Written from real troubleshooting, deployments, and systems work, not copied and reformatted from somewhere else.

## Why this exists

Most of what ends up here starts as a personal note: cleaning up a laptop before handing it off, working through a deployment issue at a client site, setting something up after migrating off a default toolchain. I write it down properly once, so nobody — including future me — has to solve it twice.

## Guides

| Guide | Description | Category |
|---|---|---|
| [Windows Laptop Cleanup](guides/windows-laptop-cleanup.md) | Remove personal data from a Windows machine before transferring ownership, without breaking the install | System Administration |

More guides get added as I write them.

## How these are written

Every guide here follows the same rules:

- **State the *why*, not just the *how*.** A command with no explanation is a script, not documentation.
- **Every step says what it does, and whether it's safe.** No silent side effects.
- **Tested before it's published.** If a guide says a command is safe, I've run it myself.

## Repo structure

```
.
├── README.md
├── LICENSE
└── guides/
    └── windows-laptop-cleanup.md
```

## Feedback

Found an error, or a better way to do something in here? Open an issue — this collection gets more useful the more people put it through its paces.

## License

MIT — see [LICENSE](LICENSE). Use it, adapt it, ship it in your own docs; no attribution required, though it's always appreciated.

## About

Maintained by Mark Coxen — [s99.co.za](https://s99.co.za) · [linkedin.com/in/mark-coxen](https://linkedin.com/in/mark-coxen)
