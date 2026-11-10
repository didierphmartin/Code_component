# SynergyAI code components

Code components used by [SynergyAI](https://synergyaichat.com) workflows. A code component is a reusable code node:
a Python function `run(input, call_tool)` that does one deterministic step, leaves by one of its named exits, and
calls MCP server tools through `call_tool(server, tool, args)`. Each file here is one component, as SynergyAI
exports it.

| File | What it does | MCP servers | Exits |
|---|---|---|---|
| `Outlook_On_dropped_email.json` | Start option: waits for an email dragged into an Outlook folder and passes it on as a document package; moves it to a "done" folder once the workflow has finished with it | Outlook | `dropped_mail`, `no_mail`, `error` |
| `PDF_convert_document.json` | Turns the package's document into a PDF (its PDF rendition, or the file itself when it already is one); attachments pass through | none | `converted`, `error` |
| `SharePoint_move.json` | Uploads the PDF and attachments to a SharePoint folder, one subfolder per item | SharePoint, Outlook | `moved`, `error` |

The MCP servers they call are in [didierphmartin/mcps](https://github.com/didierphmartin/mcps) (`outlook_mcp`,
`sharepoint_mcp`).

## The file format

| Field | Meaning |
|---|---|
| `name`, `description` | the component's name and what it does |
| `code` | the Python source; it defines `run(input, call_tool)` |
| `exits` | the names of the ways out of the node; `run` returns `{"route": <exit>, "output": ...}` |
| `mcp_servers` | the servers (by name) whose tools `call_tool` may use |
| `timeout_s` | how long one run may take |
| `args` | the node's settings (`name`, `default`, `required`, `description`), used as `{placeholders}` |
| `provides` | the values the component adds for later nodes |
| `start_trigger` | for a Start option only: the server tool the workflow waits on before each run |

`id` is the component's number in the database it was exported from; an import gives it a new one.
