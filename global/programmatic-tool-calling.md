---
publish: true
---

[[claude-code-architecture]]

# Programmatic Tool Calling

## Concept

Programmatic tool calling = executing tools through code rather than through Claude's native tool-use flow. Instead of Claude requesting tools one at a time with each result returning to context, Claude writes code that calls multiple tools, processes outputs, and controls what enters the context window.

Anthropic reports 98.7% token reduction in workflows using this pattern.

**Use cases:**
- Orchestrating multi-step workflows
- Building agents that call tools in loops
- Processing large datasets without context overflow
- Creating tool pipelines with error handling

**Official sources:**
→ [Programmatic Tool Calling - Claude Docs](https://platform.claude.com/docs/en/agents-and-tools/tool-use/programmatic-tool-calling)
→ [Advanced Tool Use - Anthropic Engineering](https://www.anthropic.com/engineering/advanced-tool-use)

## Options

### Code Mode (UTCP)

Plug-and-play library enabling agents to call MCP and UTCP tools via code execution. The most popular option with universal tool calling abstraction.

**How it works:** Framework-agnostic approach - agents write code that calls tools, reducing token usage through in-environment processing.

**Status:** Active, well-maintained. 1,100+ stars.

→ [universal-tool-calling-protocol/code-mode](https://github.com/universal-tool-calling-protocol/code-mode)

### Open PTC Agent

Open source implementation specifically designed for Programmatic Tool Calling (PTC) pattern with MCP.

**How it works:** Purpose-built for the PTC pattern - code execution with direct MCP tool access.

**Status:** Active. 440+ stars.

→ [Chen-zexi/open-ptc-agent](https://github.com/Chen-zexi/open-ptc-agent)

### MCP Code Execution Mode

Python runtime that can load MCP servers and call their tools programmatically. Runs in isolated rootless containers.

**How it works:** Pass `servers=["server_name"]` parameter to load MCPs dynamically, then use `session.call_tool()` to invoke tools.

**Status:** Experimental. Observed reliability issues with long execution times and tool availability.

→ [elusznik/mcp-server-code-execution-mode](https://github.com/elusznik/mcp-server-code-execution-mode)

### Claude Agent SDK

Anthropic's official SDK for building agentic applications. Provides programmatic control over tool calling with proper error handling, streaming, and multi-turn orchestration.

**How it works:** Define tools as Python functions, create an agent, and call `agent.run()` with tool execution handled automatically or manually.

**Status:** Production-ready. Official Anthropic support.

→ [Anthropic Agent SDK Documentation](https://docs.anthropic.com/en/docs/agents-and-tools/claude-agent-sdk)
→ [GitHub: anthropics/anthropic-sdk-python](https://github.com/anthropics/anthropic-sdk-python)

### Direct MCP Client

Use MCP client libraries to connect directly to MCP servers and call tools without Claude in the loop.

**How it works:** Instantiate an MCP client, connect to server, call tools via JSON-RPC protocol.

**Status:** Low-level approach. Useful for testing or non-Claude integrations.

→ [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)
→ [MCP Specification](https://spec.modelcontextprotocol.io/)

### FastMCP Programmatic Usage

FastMCP servers can be called programmatically by importing them as Python modules and invoking tool functions directly.

**How it works:** Import the FastMCP app, access registered tools, call them as async functions.

**Status:** Works well for testing FastMCP servers you control.

→ [FastMCP Documentation](https://gofastmcp.com)
