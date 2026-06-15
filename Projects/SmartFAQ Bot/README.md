# SmartFAQ Bot — AI-Powered FAQ & Support Automation

## Overview
SmartFAQ Bot is an n8n automation that turns Telegram into a 24/7 AI-powered customer support agent. It answers frequently asked questions using a Groq-hosted LLM (Llama 3), remembers conversation history across sessions using Google Sheets, and automatically escalates conversations to a human support team via email when a user needs more help than the bot can provide.

This project is designed to be a plug-and-play FAQ assistant for any business — SaaS, e-commerce, agencies, or service providers — with the FAQ knowledge base and escalation logic fully customizable.

---

## Problem
Most small businesses and solo founders can't afford a 24/7 support team, yet customers expect instant answers to repetitive questions (pricing, refund policy, how-to questions, etc.). Without automation:

- Support requests pile up outside business hours
- The same FAQs get answered manually over and over
- Customers who need real human help often get stuck in chatbot loops with no way out
- There's no record of what was discussed with a returning customer

---

## Solution
SmartFAQ Bot solves this with a single n8n workflow that:

- Listens for incoming Telegram messages in real time
- Pulls the user's previous conversation history from Google Sheets so the AI has full context (persistent memory across sessions)
- Sends the conversation + a custom system prompt to Groq's Llama 3 model to generate a fast, accurate FAQ response
- Detects escalation intent (keywords like "human", "agent", "support") and instantly routes the conversation to a separate escalation flow
- Logs every escalation to a dedicated Google Sheet and emails the support team with full context, while reassuring the user a human is on the way
- Saves every exchange back to memory so the next message continues the conversation naturally

---

## Tools Used
- **n8n** (self-hosted, via ngrok tunnel for webhook access)
- **Telegram Bot API** — chat interface and trigger
- **Groq (Llama 3 - 8B)** — LLM for generating FAQ responses, via n8n's LangChain "Basic LLM Chain" + "Groq Chat Model" nodes
- **Google Sheets** — persistent session memory (`memory` tab) and escalation logging (`escalations` tab)
- **Gmail** — automated email alerts to the support team on escalation

---

## Workflow Steps

**Main flow:**
1. **Telegram Trigger** — fires on every incoming message from a user
2. **Get Memory (Google Sheets)** — retrieves all prior messages for that chat ID to rebuild conversation context
3. **IF Node (Escalation Check)** — scans the message for escalation keywords (`human`, `agent`, `support`, `help me`, `real person`, `speak to someone`) using OR logic

**If no escalation is needed (FALSE branch):**

4. **Basic LLM Chain + Groq Chat Model** — generates a response using a system prompt, the retrieved memory, and the new user message
5. **Save User Message** and **Save Bot Reply (Google Sheets)** — appends both the user's message and the AI's reply to the `memory` tab with chat ID, role, message, and timestamp
6. **Telegram Send Message** — delivers the AI-generated answer back to the user

**If escalation is needed (TRUE branch):**

4. **Log to Escalations Sheet (Google Sheets)** — records chat ID, name, message, timestamp, and status (`open`) in the `escalations` tab
5. **Send Gmail** — emails the support team an HTML-formatted alert with the user's details and message
6. **Telegram Send Message** — confirms to the user that a human will follow up shortly

---

## Output
- **In Telegram:** The user receives either an instant AI-generated FAQ answer or a confirmation message that a human agent has been notified
- **In Google Sheets:**
  - `memory` tab fills with a running log of every conversation (role, message, timestamp) per chat ID, enabling persistent memory
  - `escalations` tab fills with a queue of open support tickets, ready for the team to action
- **In Gmail:** Support team receives a formatted email for every escalation with the user's name, Telegram username, chat ID, message, and time

---

## Screenshots
- Full workflow canvas (both branches)
- Telegram Trigger node configuration
- Basic LLM Chain + Groq Chat Model connection
- IF node escalation conditions
- Google Sheets `memory` and `escalations` tabs with sample data
- Telegram conversation showing an FAQ answer and an escalation response
- Sample escalation email in inbox

---

---

## Key Learnings
- **n8n node naming matters** — spaces or special characters in node names can break expression references (`$('Node Name')`); keep names clean and consistent
- **LangChain nodes use a separate connection system** — AI sub-nodes (like Groq Chat Model) connect via a dedicated purple connector, not the standard gray data connector
- **Building a `messages` array for LLM APIs via raw HTTP requests is fragile in n8n** — switching to n8n's native LangChain nodes (Basic LLM Chain + Groq Chat Model) avoided JSON/array formatting errors entirely
- **Google Sheets auto-formula detection** can turn expression outputs (like chat IDs and timestamps) into `#ERROR!` or `#NAME?` — fixing this requires setting affected columns to **Plain Text** format in Sheets
- **Always enable "Always Output Data"** on lookup nodes (like Get Memory) so the workflow doesn't halt for first-time users with no history
- **Telegram fields like `username` aren't always present** — always provide a fallback (e.g. `first_name`) to avoid `undefined` values breaking downstream nodes
- **Local n8n instances need a tunnel (ngrok)** to receive Telegram webhooks, since Telegram requires a public HTTPS endpoint
