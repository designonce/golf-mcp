# golf-mcp releases

This repository holds the published wheels of `golf-mcp`. `golf-mcp` runs golf CAD scripts in Python
for an agent. It is an MCP server and a command line with the same commands.

Each release has two kinds of wheel:

- `golf_mcp-<version>-py3-none-any.whl`: the launcher. It gives the `golf` command.
- `golf_mcp_<platform>-<version>-py3-none-<tag>.whl`: one wheel for each platform. It holds the
  `golf` executable, the `golf_py` extension and the worker.

The launcher lists each platform wheel with its release URL and an environment marker. pip and uv
download only the wheel for your platform.

## Platforms

| Platform       | Wheel tag               |
| -------------- | ----------------------- |
| Linux x86_64   | `manylinux_2_39_x86_64` |
| Windows x86_64 | `win_amd64`             |

Python 3.11 or newer is necessary.

## Run with uv

Use the URL of the launcher wheel of an exact release:

```sh
uvx --from https://github.com/designonce/golf-mcp/releases/download/v<version>/golf_mcp-<version>-py3-none-any.whl golf --help
```

Do not use `uvx --with <wheel> golf`. That command gets a package named `golf` from PyPI.

To start a project in the current folder and run a script:

```sh
golf init
golf run part.py
golf render
```

## Use as an MCP server

```json
{
  "mcpServers": {
    "golf": {
      "command": "uvx",
      "args": [
        "--from",
        "https://github.com/designonce/golf-mcp/releases/download/v<version>/golf_mcp-<version>-py3-none-any.whl",
        "golf",
        "serve"
      ]
    }
  }
}
```

## Install with pip

```sh
pip install https://github.com/designonce/golf-mcp/releases/download/v<version>/golf_mcp-<version>-py3-none-any.whl
```
