# golf-mcp releases

This repository holds the published wheels of `golf-mcp`. `golf-mcp` runs golf CAD scripts in Python
for an agent. It is a command line, `golf`, and an MCP server with the same commands.

Each release has two kinds of wheel:

- `golf_mcp-<version>-py3-none-any.whl`: the launcher. It gives the `golf` command.
- `golf_mcp_<platform>-<version>-py3-none-<tag>.whl`: one wheel for each platform. It holds the
  `golf` executable, the `golf_py` extension and the worker.

The launcher lists each platform wheel with its release URL and an environment marker. pip and uv
download only the wheel for your platform.

## Versions

A release's tag and its wheels write a prerelease version differently:

| Release    | Tag           | Version in the wheel names |
| ---------- | ------------- | -------------------------- |
| 0.1.0-rc.1 | `v0.1.0-rc.1` | `0.1.0rc1`                 |
| 1.0.0      | `v1.0.0`      | `1.0.0`                    |

The examples below use the launcher wheel of `v0.1.0-rc.1`:

```
https://github.com/designonce/golf-mcp/releases/download/v0.1.0-rc.1/golf_mcp-0.1.0rc1-py3-none-any.whl
```

For another release, copy the URL of its `golf_mcp-…-py3-none-any.whl` from the release's assets.

## Platforms

| Platform       | Wheel tag               |
| -------------- | ----------------------- |
| Linux x86_64   | `manylinux_2_39_x86_64` |
| Windows x86_64 | `win_amd64`             |

Python 3.11 or newer is necessary.

## Install

Install `golf` as a command with uv:

```sh
uv tool install https://github.com/designonce/golf-mcp/releases/download/v0.1.0-rc.1/golf_mcp-0.1.0rc1-py3-none-any.whl
```

Or with pip, into the active environment:

```sh
pip install https://github.com/designonce/golf-mcp/releases/download/v0.1.0-rc.1/golf_mcp-0.1.0rc1-py3-none-any.whl
```

To run it once without installing it, use `uvx --from <URL> golf`:

```sh
uvx --from https://github.com/designonce/golf-mcp/releases/download/v0.1.0-rc.1/golf_mcp-0.1.0rc1-py3-none-any.whl golf --help
```

Do not use `uvx --with <wheel> golf`. That command gets a package named `golf` from PyPI.

## Use

Start a project in the current folder, then run a script and draw its parts:

```sh
golf init
golf run part.py --label first
golf render
```

`golf init` writes `golf_project.json`, the skills for agents and a section in `AGENTS.md` that lists
them. The skills go in `.agents/skills/`: `golf-cad` (modelling), `golf-review` (reviewing a design)
and `golf-report` (a report page with a 3D walkthrough). If the project has a `.claude/` folder,
each skill is also linked into `.claude/skills/` for Claude Code. `--skills-dir` writes them to
another folder.

`golf docs` lists the documentation, and `golf docs <section>` prints a section as markdown.
`golf export glb` writes `assembly.glb`, every part in one file, as well as a file for each part.

## Use as an MCP server

The MCP server keeps the current parts loaded between commands. It is useful when an agent runs many
versions of a design. For Claude Code, put this in `.mcp.json` at the project's root. Other clients
take the same `mcpServers` entry in their MCP settings.

```json
{
  "mcpServers": {
    "golf": {
      "command": "uvx",
      "args": [
        "--from",
        "https://github.com/designonce/golf-mcp/releases/download/v0.1.0-rc.1/golf_mcp-0.1.0rc1-py3-none-any.whl",
        "golf",
        "serve"
      ]
    }
  }
}
```

If `golf` is installed, use `"command": "golf"` and `"args": ["serve"]`.
