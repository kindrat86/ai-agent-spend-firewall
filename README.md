# AI Agent Spend Firewall

Pre-spend firewall for autonomous AI agents. One API call returns APPROVED, BLOCKED, or FLAGGED before any money moves.

## The Problem

Your AI agent can spend money autonomously. At 2 AM. Without asking.

- Claude Code buys compute from an unknown vendor → $6,200
- An MCP server retries a rate-limited purchase 40 times → $4,000
- A coding agent tips an API into an overage tier → $2,200

Provider caps don't help. They only limit per-provider spending and notify you after the fact.

## The Solution

A spend firewall that sits in front of every transaction across every merchant, decides in real time before money moves, and keeps one audit log for everything.

**Three decisions, defined:**

| Decision | Meaning |
|---|---|
| APPROVED | Within caps, velocity rules, and merchant allowlist. Proceed. |
| BLOCKED | Exceeds hard limit, hits banned merchant, or breaks a rule. Stop. No override. |
| FLAGGED | Edge case. Unusual pattern. New merchant. Held for human review. |

**Rules you configure:**
- Per-transaction amount caps
- Daily/weekly/monthly velocity limits
- Merchant allowlists and blocklists
- Category-based spending limits (compute, API, SaaS, etc.)
- Time-of-day restrictions

## Architecture

```
Your Agent → Spend Firewall → APPROVED/BLOCKED/FLAGGED → Proceed or Stop
                 ↑
          Your Rules (caps, velocity, merchants, categories)
```

## Integration

**Native MCP support.** Any MCP-compatible client (Claude Code, Cursor, Windsurf) can use it:

```json
{
  "mcpServers": {
    "spend-firewall": {
      "url": "https://sipi.bot/api/mcp"
    }
  }
}
```

**HTTP API for any agent:**

```bash
curl -X POST https://sipi.bot/v1/transactions/evaluate \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"amount": 6200, "merchant": "unknown-gpu.ru", "category": "compute"}'

# Response: {"decision": "BLOCKED", "reason": "Merchant not on allowlist"}
```

## Resources

- [sipi.bot](https://sipi.bot) — Free tier: 5,000 checks/month. No credit card.
- [How it compares to Stripe Radar](https://sipi.bot/vs/stripe-radar) — Provider caps vs agent firewall
- [Integration guides](https://sipi.bot/integrations) — MCP, HTTP, Python SDK

## License

MIT
