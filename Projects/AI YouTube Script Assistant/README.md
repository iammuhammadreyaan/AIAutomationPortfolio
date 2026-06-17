# AI YouTube Script Assistant

## Overview
An n8n automation that takes a YouTube topic as input and generates a complete video content package — hooks, title, outline, script, description, and tags — using parallel AI chains for maximum speed.

---

## Problem
Content creators and agencies waste hours preparing video assets before filming. Writing hooks, scripts, titles, and tags for every video is a massive time sink with no streamlined solution.

---

## Solution
A webhook-triggered n8n workflow that fires 6 parallel AI generation chains simultaneously, each specialized for one content asset. All outputs are merged and returned as a single clean JSON response in seconds — ready to use immediately.

---

## Tools Used
- n8n
- Webhook Node
- Basic LLM Chain Node (x6, native n8n AI nodes)
- OpenAI / Anthropic (via n8n credentials)
- Merge Node
- Code Node
- Respond to Webhook Node

---

## Workflow Steps
1. Client sends a POST request with a `topic` field to the webhook
2. Input validation checks topic length and returns clean error messages if invalid
3. 6 AI nodes fire in parallel — generating hooks, titles, outline, script, description, and tags
4. Merge node collects all 6 outputs in order
5. Code node parses and assembles everything into one unified response
6. Webhook responds with the complete structured JSON package

---

## Output
A single JSON response containing all 6 video assets — 5 hooks, 5 titles, a full structured outline, complete script with sections, YouTube description with timestamps and hashtags, and primary/secondary/long-tail tags.

---

## Screenshots
Add screenshots of:
- Workflow canvas showing all parallel branches
- Basic LLM Chain node with prompt
- Sample response output

---

## Demo Video
Available on request.

---

## Key Learnings
- Parallel branching cuts generation time by ~80% compared to sequential chains
- Native LLM Chain nodes are far more reliable than HTTP Request nodes for AI calls
- Merge node must be set to **Append** mode to correctly stack multiple parallel outputs
- `$('NodeName').first().json.field` syntax must be used inside Code nodes — n8n expressions don't work there
- Input validation as the first step is essential for any client-facing or sellable workflow
