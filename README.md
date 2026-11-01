# golf-mcp releases

This repository holds the published wheels of `golf-mcp`. `golf-mcp` runs golf CAD scripts in Python
for an agent. It is a command line, `golf`, and an MCP server with the same commands.

Each release has two kinds of wheel:

- `golf_mcp-<version>-py3-none-any.whl`: the launcher. It gives the `golf` command.
- `golf_mcp_<platform>-<version>-py3-none-<tag>.whl`: one wheel for each platform. It holds the
  `golf` executable, the `golf_py` extension and the worker.

The launcher lists each platform wheel with its release URL and an environment marker. pip and uv
download only the wheel for your platform.

## Latest release

Read [latest.json](https://github.com/designonce/golf-mcp/releases/download/latest/latest.json)
for the latest published version, including release candidates. This stable URL is updated when a
release is published. The manifest includes:

- `version` and `tag`: the release version and GitHub tag.
- `launcher`: the launcher wheel URL to use with uv or pip.
- `wheels`: all wheel URLs for that release.
- `install`: ready-to-run installation commands.

Use the manifest instead of copying a version-pinned URL from an old example. To pin a particular
release, keep its `launcher` URL; the platform dependencies will come from that same release.

## Platforms

| Platform       | Wheel tag               |
| -------------- | ----------------------- |
| Linux x86_64   | `manylinux_2_39_x86_64` |
| Windows x86_64 | `win_amd64`             |

Python 3.11 or newer is necessary.

## Install

First resolve the latest launcher URL (POSIX shell; Python 3.11+ is required):

```sh
golf_wheel_url="$(python3 -c 'import json, urllib.request; print(json.load(urllib.request.urlopen("https://github.com/designonce/golf-mcp/releases/download/latest/latest.json"))["launcher"])')"
```

Install `golf` as a command with uv:

```sh
uv tool install "$golf_wheel_url"
```

To replace an existing tool installation with the latest release:

```sh
uv tool install --force "$golf_wheel_url"
```

Or with pip, into the active environment:

```sh
python3 -m pip install --upgrade "$golf_wheel_url"
```

To run it once without installing it, use `uvx --from <URL> golf`:

```sh
uvx --from "$golf_wheel_url" golf --help
```

On Windows, open `latest.json` and use its `launcher` URL directly in the same uv or pip command.

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
      "command": "golf",
      "args": ["serve"]
    }
  }
}
```

This uses the `golf` command installed above. Refresh its installation from `latest.json` when
upgrading.

For a uvx-based configuration, use `"command": "uvx"` and
`"args": ["--from", "<launcher URL from latest.json>", "golf", "serve"]`. Replace the placeholder
with the manifest's actual `launcher` URL and refresh it when upgrading. MCP settings do not expand
shell variables such as `$golf_wheel_url`.
