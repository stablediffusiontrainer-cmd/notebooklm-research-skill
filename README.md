# NotebookLM Research Skill for Claude

A Claude system prompt that turns Claude into a rigorous, multi-session NotebookLM research assistant. Point it at a topic and it will plan, validate, and populate a high-quality NotebookLM notebook — automatically — with up to 50 well-sourced references.

---

## What It Does

- **Builds a structured research plan** before adding a single source, organized into thematic phases and tiered by source quality
- **Prioritizes academic and institutional sources** (peer-reviewed journals, arXiv, government reports, technical standards) over popular web content
- **Validates every URL** before adding it — catching silent 404s, paywalls, and login walls that NotebookLM would otherwise silently accept
- **Tracks paywalled sources separately** with citation counts, author h-indices, and confidence ratings so you can review and add them manually
- **Enforces provider diversity** to avoid over-indexing on any single domain
- **Persists state across sessions** using notes inside the notebook itself — so you can continue in a new conversation just by sharing the notebook URL
- **Maintains detailed session logs** inside the notebook for full auditability of what was added, skipped, or replaced

---

## Prerequisites

Before using this prompt you will need:

1. **A Claude.ai account** — Pro is strongly recommended. The workflow makes heavy use of web search and tool calls, and the multi-session design assumes a reasonably large context window.

2. **A NotebookLM account** — Available free at [notebooklm.google.com](https://notebooklm.google.com).

3. **The NotebookLM MCP server** installed and connected to Claude. This is what allows Claude to create notebooks and add sources on your behalf.
   - Follow the setup instructions at the [NotebookLM MCP server repository](https://github.com/jacob-bd/notebooklm-mcp-cli)
   - Confirm the MCP server appears as a connected tool in your Claude settings before proceeding

---

## How to Use

### First session (new notebook)

1. Copy the full contents of [`prompt.md`](./prompt.md)
2. Paste it as the **system prompt** in your Claude interface, or load it via your MCP client configuration — whichever method your setup supports
3. Tell Claude the topic you want researched. For example:
   > *"Create a notebook on the current state of mRNA vaccine technology."*
4. Claude will create the notebook, share its URL, and present a **Research Plan** for your approval before adding any sources
5. Review the plan, request any changes, then tell Claude to proceed
6. Claude will work through the plan, validating and adding sources in batches, until it reaches the session limit

### Continuing in a new session

When Claude signals that the session limit has been reached:

1. Start a **new conversation** with Claude
2. Load the system prompt again
3. Paste the notebook URL and tell Claude to continue. For example:
   > *"Please continue populating this notebook: [URL]"*
4. Claude will read its working notes from the notebook, confirm the current state, and resume from where it left off

Repeat until the notebook is complete (up to 50 sources).

---

## Output

When the notebook is finished, Claude writes a `_REPORT` note inside the notebook containing:

- A full table of all sources added, with URLs, source type, and tier
- A log of skipped or replaced sources and the reasons
- Paywalled source recommendations with quality metrics for manual review
- All search queries used during research
- Provider distribution across the notebook

The notebook will also contain `_PLAN`, `_PROGRESS`, and `_LOG_SESSION_*` working notes that document the full construction history. These can be deleted once you no longer need them — they are not required for any of NotebookLM's AI features.

---

## Known Limitations

- **MCP server required.** This prompt will not work without the NotebookLM MCP server connected to Claude. It cannot be used with Claude.ai alone.
- **Conservative session limits.** The prompt is designed to save state and stop early rather than risk losing progress to a context overflow. In practice, a full 50-source notebook typically takes **6–8 sessions** — plan accordingly.
- **No use of NotebookLM's native research features.** The prompt deliberately avoids `research_start` / `research_status` due to their dependency on the Claude in Chrome extension. All source discovery is done via Claude's own web search.
- **Paywalled sources are not added automatically.** Claude collects them into a `_PAYWALLED` note with quality metrics, but you must review and add them to the notebook manually.
- **URL validation is not foolproof.** Claude fetches and inspects each URL before adding it, but some paywalls and access restrictions may not be detected until after a source is added. The post-addition audit is designed to catch these.

---

## Contributing

Contributions are welcome. If you find a bug, have a suggestion, or want to propose an improvement to the prompt logic, please open an issue or submit a pull request.

If you test this against a specific topic or MCP server setup and want to share your experience — especially edge cases or failure modes — that's very valuable. Feel free to open an issue with your findings even if you don't have a fix.

---

## License

MIT — see [`LICENSE`](./LICENSE)
