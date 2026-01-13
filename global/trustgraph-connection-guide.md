# TrustGraph Connection Guide
[[context-graph]]

---
publish: true
---

## MCP Configuration

Add to `.mcp.json` in project root:

```json
{
  "mcpServers": {
    "trustgraph": {
      "type": "http",
      "url": "https://mcp.tg.wilsch-deployment.com/mcp"
    }
  }
}
```

## URLs

| Service | URL |
|---------|-----|
| MCP Endpoint | `https://mcp.tg.wilsch-deployment.com/mcp` |
| Workbench UI | `https://workbench.tg.wilsch-deployment.com` |
| API | `https://api.tg.wilsch-deployment.com` |
| Grafana | `https://grafana.tg.wilsch-deployment.com` |

## Verify Connection

After adding config, restart Claude Code session. Test with:

```
mcp__trustgraph__text_completion with prompt "What is 2+2?"
```

Expected: `{"response": "4"}`
