# Automan

**Automan is an autonomous AI agent that sells its skills as paid services and gets paid per call with [x402](https://www.x402.org/) micropayments (USDC).** It is reachable as an **MCP server** and over plain **HTTP**.

- 🌐 Landing / docs: **https://automan.amer-daniel.com/**
- 🔌 MCP endpoint: `https://mcp.amer-daniel.com/mcp` (streamable-http)
- 📇 Machine-readable catalog: `https://automan.amer-daniel.com/skills`
- 💸 Payment: x402 (HTTP 402), USDC on **Base** (`eip155:8453`) and **Polygon** (`eip155:137`), gasless for the caller (EIP-3009)

This repository is the **public documentation and MCP manifest** for the service. The agent itself (its reasoning, governance and treasury) runs privately; this repo is how clients and agents discover and connect to it.

---

## What it does

Automan reasons with local models and sells discrete tasks — summarize, translate, extract data, analyze, write code, generate reports — to other agents and to humans. Every call is settled in USDC via x402. The caller only needs USDC; signing is off-chain (EIP-3009), so there is **no gas** for the caller.

## Skills & pricing

| Skill | Tier | Price (USDC) | What it does |
|---|---|---|---|
| `resumen` | 1 | 0.05 | Summarize a document or article |
| `traduccion` | 1 | 0.08 | Translate text between languages |
| `extraccion_datos` | 1 | 0.12 | Extract / structure data into JSON or CSV |
| `responder` | 1 | 0.05 | Answer a question with mini-research |
| `correccion` | 1 | 0.05 | Proofread and edit text |
| `copy_corto` | 1 | 0.07 | Short copy (bio, description, post) |
| `clasificar` | 1 | 0.03 | Classify / tag items |
| `snippet_codigo` | 2 | 0.80 | Write a small code snippet or script |
| `analisis_datos` | 2 | 1.50 | Analyze a dataset with pandas |
| `grafico` | 2 | 0.70 | Generate a chart / visualization |
| `microservicio_mcp` | 3 | 2.50 | Expose a pay-per-call MCP tool |
| `reporte` | 3 | 3.50 | Produce a full structured report |

Prices are the live list at time of writing; the authoritative, always-current catalog is `GET https://automan.amer-daniel.com/skills`.

## Connect over MCP

Point any MCP client (Claude Desktop, Cursor, or your own agent) at:

```
https://mcp.amer-daniel.com/mcp
```

The free `automan_info` tool describes the server; every skill tool runs a task and is x402-gated. See [`examples/claude_desktop_config.json`](examples/claude_desktop_config.json).

## Connect over HTTP

```bash
# Catalog (free)
curl https://automan.amer-daniel.com/skills

# Run a skill (returns HTTP 402 with an x402 challenge; retry signed to settle)
curl -X POST https://automan.amer-daniel.com/skill/resumen \
  -H "Content-Type: application/json" \
  -d '{"input": "long text to summarize ..."}'
```

Full flow in [`examples/http.md`](examples/http.md).

## Payment (x402)

Automan speaks the [x402](https://www.x402.org/) protocol. A skill call returns **HTTP 402** with a challenge in the `PAYMENT-REQUIRED` header (base64 JSON). The caller signs an EIP-3009 USDC transfer authorization off-chain and retries with a `PAYMENT-SIGNATURE` header; the server verifies, settles, and returns the result.

- Networks: Base (`eip155:8453`), Polygon (`eip155:137`)
- Asset: USDC
- Treasury (receiving address): `0xcCbE6739D9dbA5cD104ec596eB7eB4E063dC4cc6`

See [`mcp.json`](mcp.json) for a machine-readable manifest.

## Status

Live and experimental. Feedback and integration questions welcome via issues.

## License

The contents of this repository (documentation and manifest) are released under the [MIT License](LICENSE). This license covers the docs/manifest only, not the hosted service.
