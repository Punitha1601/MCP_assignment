# Hello MCP -- Python MCP Server

A beginner-friendly Model Context Protocol (MCP) server built with
Python and the official MCP Python SDK. It exposes two tools that an
MCP-compatible client, such as VS Code Copilot, can call:

-   `get_greeting(name)`: Returns a greeting for the supplied name.
-   `add_numbers(a, b)`: Adds two integers and returns the result.

## 1. Project structure

``` text
hello-mcp/
├── server.py
├── README.md
└── requirements.txt
```

## 2. Requirements

-   Python 3.10 or newer
-   Visual Studio Code
-   An MCP-compatible client (for example, VS Code Copilot with MCP
    support)
-   Internet access for installing the Python package

Check your Python version:

``` bash
python --version
```

## 3. Installation

Open a terminal in the project folder and create a virtual environment
(recommended).

### Windows

``` powershell
python -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install "mcp[cli]"
```

Create a `requirements.txt` file containing:

``` text
mcp[cli]
```

To install dependencies later:

``` bash
python -m pip install -r requirements.txt
```

## 4. Server code

Save the following code as `server.py`:

``` python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("Hello MCP")


@mcp.tool()
def get_greeting(name: str) -> str:
    """Return a greeting for a student."""
    return f"Hello, {name}! Your first MCP tool works."


@mcp.tool()
def add_numbers(a: int, b: int) -> int:
    """Add two numbers."""
    return a + b


if __name__ == "__main__":
    mcp.run()
```

## 5. Run the server

With the virtual environment activated, run:

``` powershell
python server.py
```

The server uses standard input/output (stdio) as its MCP transport. When
launched by an MCP client, it is normally started by that client; do not
expect it to behave like a normal web server with a browser URL.

## 6. Configure VS Code

Add an MCP server entry to the workspace configuration file:

`.vscode/mcp.json`

Example configuration for Windows:

``` json
{
  "servers": {
    "helloMcp": {
      "type": "stdio",
      "command": "C:\\full\\path\\to\\hello-mcp\\.venv\\Scripts\\python.exe",
      "args": [
        "C:\\full\\path\\to\\hello-mcp\\server.py"
      ]
    }
  }
}
```

Replace both example paths with the actual absolute paths on your
computer. Using the virtual environment's Python executable helps ensure
VS Code uses the same environment in which `mcp[cli]` was installed.

After saving the configuration:

1.  Open the Command Palette in VS Code.
2.  Find the MCP server controls (the exact command names may vary by VS
    Code version).
3.  Start or restart `helloMcp`.
4.  Check the MCP server output/log for startup errors.
5.  Open Copilot Chat and allow it to use the MCP tools if prompted.

## 7. Test the tools

Ask the MCP client:

**Test 1 -- Greeting**

``` text
Use the get_greeting tool with name = "Punitha".
Show the result.
```

Expected result:

``` text
Hello, Punitha! Your first MCP tool works.
```

**Test 2 -- Addition**

``` text
Use the add_numbers tool with a = 10 and b = 20.
Show the result.
```

Expected result:

``` text
30
```

## 8. Troubleshooting

### Error: `Pending response rejected since connection got disposed`

This is usually a client-side message indicating that the MCP connection
was closed while a request was still waiting for a response. It is often
a symptom of the server process stopping or failing to launch, rather
than the underlying cause.

Check the MCP server output/log for the first Python error that appears
before the connection-disposed message.

Try these steps:

1.  Confirm that `server.py` runs without an import error.

2.  Install the SDK into the exact Python environment configured in
    `.vscode/mcp.json`:

    ``` powershell
    C:\full\path\to\hello-mcp\.venv\Scripts\python.exe -m pip install --upgrade "mcp[cli]"
    ```

3.  Verify the import:

    ``` powershell
    C:\full\path\to\hello-mcp\.venv\Scripts\python.exe -c "from mcp.server.fastmcp import FastMCP; print('MCP import OK')"
    ```

4.  Check that the `command` and `args` paths in `.vscode/mcp.json` are
    correct.

5.  Ensure the server does not print debugging messages to standard
    output (`stdout`). For an stdio MCP server, protocol messages use
    stdout; send diagnostic messages to standard error (`stderr`)
    instead.

6.  Restart the MCP server from VS Code, then retry the tool call.

### Error: `ModuleNotFoundError: No module named 'server.fastmcp'`

The import path is incorrect for the official MCP Python SDK. Use:

``` python
from mcp.server.fastmcp import FastMCP
```

Do not use:

``` python
from server.fastmcp import FastMCP
```

### Error: `No module named 'mcp'`

Install the package in the Python environment used by the MCP
configuration:

``` powershell
python -m pip install "mcp[cli]"
```

If VS Code uses a different interpreter, select the correct interpreter
or update the `command` path in `.vscode/mcp.json`.

### The server starts but Copilot cannot find the tools

-   Confirm that `helloMcp` is running.
-   Check the MCP server logs for startup or configuration errors.
-   Restart the server after changing `server.py` or `mcp.json`.
-   Confirm that the client has permission to use MCP tools.

## 9. Important notes

-   The Python process must remain running while the client is using the
    MCP server.
-   Do not close the terminal or stop the server process while testing a
    manually launched server.
-   For stdio transport, avoid `print()` debugging statements in the
    server code because they can interfere with protocol communication.
-   The exact VS Code MCP configuration and UI can vary by version. If
    the example configuration is rejected, consult the MCP configuration
    format supported by your installed VS Code version.

## 10. Learning outcome

This project demonstrates how to:

-   Create an MCP server using Python.
-   Expose Python functions as MCP tools with `@mcp.tool()`.
-   Accept typed tool arguments.
-   Return results to an MCP-compatible client.
-   Connect a local Python MCP server to VS Code.

------------------------------------------------------------------------

**Project name:** Hello MCP\
**Language:** Python\
**Protocol:** Model Context Protocol (MCP)\
**Tools:** `get_greeting`, `add_numbers`
