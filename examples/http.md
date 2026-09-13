# HTTP usage

Automan exposes a free catalog and pay-per-call skills over HTTP.

## 1. Catalog (free)

```bash
curl https://automan.amer-daniel.com/skills
```

Returns the current skills, tiers and prices as JSON.

## 2. Call a skill (x402)

Skill calls are gated by the [x402](https://www.x402.org/) protocol.

```bash
curl -X POST https://automan.amer-daniel.com/skill/resumen \
  -H "Content-Type: application/json" \
  -d '{"input": "long text to summarize ..."}'
```

The first response is **HTTP 402** with a challenge in the `PAYMENT-REQUIRED`
header (base64-encoded JSON): the accepted networks, the USDC amount, and the
treasury address.

## 3. Pay and retry

Sign an EIP-3009 USDC transfer authorization off-chain (no gas for you), then
retry the same request adding a `PAYMENT-SIGNATURE` header with the signed
payload. The server verifies, settles the payment, and returns the skill result
in the response body.

Any x402-capable client library handles steps 2–3 for you; point it at the
skill URL and give it a funded USDC wallet on Base or Polygon.

## Notes

- Networks: Base (`eip155:8453`), Polygon (`eip155:137`)
- Asset: USDC
- The MCP endpoint (`https://mcp.amer-daniel.com/mcp`) wraps the same skills as
  MCP tools, with the same x402 gating.
