# Scoop bucket — n24q02m

**Scoop bucket for n24q02m's command-line tools, with every manifest pinned to its upstream's latest stable release.**

[![CI](https://github.com/n24q02m/scoop-bucket/actions/workflows/ci.yml/badge.svg)](https://github.com/n24q02m/scoop-bucket/actions/workflows/ci.yml)
[![License: Apache-2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)
[![Renovate](https://img.shields.io/badge/renovate-enabled-brightgreen.svg)](https://renovatebot.com)
[![Scoop](https://img.shields.io/badge/Scoop-bucket-5C2D91)](https://scoop.sh)

```pwsh
scoop bucket add n24q02m https://github.com/n24q02m/scoop-bucket
scoop install skret
```

The `skret` manifest is auto-updated on each upstream release by [goreleaser](https://goreleaser.com/) — do not edit by hand.

The other manifests install the release the registry serves, with every dependency pinned by hash. Python tools install the PyPI wheel into a private [uv](https://docs.astral.sh/uv/) virtualenv from `locks/<name>-<version>.txt`, the release tag's `uv.lock` exported with hashes; a dependency that ships only an sdist builds with the hashed backends in `locks/<name>-<version>.build.txt`, and every step runs with `--require-hashes --only-binary :all:`. Node tools install the npm tarball and run `npm ci --omit=dev --ignore-scripts` against `locks/<name>-<version>.package-lock.json`, resolved from the published `package.json` as of its publish time.

The `Bump` workflow (`bump.yml`, run by `workflow_dispatch` with a `package` and an optional `version`) runs [`scripts/bump.py`](https://github.com/n24q02m/homebrew-tap/blob/main/scripts/bump.py) from the Homebrew tap, which rewrites the version, URL and checksums from the registry and regenerates the lock files, then opens a pull request and merges it once its checks pass. CI checks every manifest against the registry and its lock files, warns about any manifest behind the registry latest, installs every manifest on Windows and runs `--version`.

## Available manifests

| Name | Description | Upstream |
|------|-------------|----------|
| skret | Cloud-provider secret manager CLI | [n24q02m/skret](https://github.com/n24q02m/skret) |
| wet | Streamable HTTP/stdio MCP server and CLI for web extraction and search | [n24q02m/wet](https://github.com/n24q02m/wet) |
| mnemo | Episodic and semantic memory MCP server and CLI | [n24q02m/mnemo](https://github.com/n24q02m/mnemo) |
| crg | Code review and AST symbol graph MCP server and CLI | [n24q02m/crg](https://github.com/n24q02m/crg) |
| telegram | Telegram client and bot MCP server and CLI | [n24q02m/telegram](https://github.com/n24q02m/telegram) |
| email | Unified IMAP and SMTP email MCP server and CLI | [n24q02m/email](https://github.com/n24q02m/email) |
| notion | Notion workspace and document MCP server and CLI | [n24q02m/notion](https://github.com/n24q02m/notion) |
| workspace | Google Workspace MCP server and CLI | [n24q02m/workspace](https://github.com/n24q02m/workspace) |

## License

Apache-2.0.
