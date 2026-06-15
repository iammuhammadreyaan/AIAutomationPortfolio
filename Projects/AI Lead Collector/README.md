# AI Lead Command Center

## Overview
AI Lead Command Center is an end-to-end n8n automation that captures incoming leads from a web form, analyzes them using AI, and instantly takes action — qualifying the lead, logging it to a live dashboard, sending a personalized reply, and alerting the business owner in real time if the lead is high-value. It turns a simple "contact form" into an intelligent lead-response system that works 24/7 without any manual effort.

---

## Problem
Most small businesses and freelancers lose potential clients simply because of slow, generic, or disorganized lead handling:

- Leads often wait hours (sometimes days) for a reply, by which point they've already moved on to a competitor.
- Every lead gets the same copy-paste response, regardless of what they actually asked for.
- There's no quick way to tell which leads are urgent and worth dropping everything for, versus leads that can wait.
- Duplicate submissions create messy, cluttered records and confusing duplicate replies.
- Lead information is scattered across emails and form notifications instead of living in one organized place.

For a business owner, this means missed revenue, a disorganized pipeline, and a poor first impression on potential clients.

---

## Solution
This n8n workflow solves the problem by combining a simple lead capture form with an AI-powered decision engine that reacts the moment a lead comes in:

- A Tally form captures lead details (contact info, project needs, budget, and timeline) and instantly triggers the workflow via webhook.
- Before anything else happens, the workflow checks the lead's email against existing records to catch duplicate submissions and respond to them appropriately.
- An AI Agent (running on Groq's Llama 3.3 70B model for near-instant responses) reads the lead's message and returns a structured analysis: a Hot/Warm/Cold score with reasoning, an industry tag, any "power signals" (urgency, budget, deadlines), a short human-readable summary, and a fully personalized reply email.
- A routing step sends the lead down one of three paths — Hot, Warm, or Cold — each handled slightly differently.
- Every lead is logged to a Google Sheets dashboard with all of the AI-generated insights, creating a live, organized view of the entire pipeline.
- The lead receives a personalized email reply written specifically for their inquiry — not a generic template.
- If the lead is scored Hot, the business owner receives an instant alert email with a summary and a direct call to action to follow up immediately.

The result: every lead is acknowledged within seconds, qualified automatically, and the business owner always knows exactly where to focus their attention first.

---

## Tools Used
- n8n (workflow automation/orchestration)
- Tally (lead intake form + webhook trigger)
- Webhooks (instant trigger on form submission)
- AI Agent node powered by Groq (Llama 3.3 70B Versatile)
- Google Sheets (live lead dashboard + duplicate lookup)
- Gmail (personalized auto-replies + owner alerts)

---

## Workflow Steps

1. **Catch Lead Webhook** – Triggers instantly the moment a lead submits the Tally form, capturing their name, email, company, project details, budget, and timeline.

2. **Check Duplicate Lead / Is Duplicate?** – Looks up the submitted email against the existing Google Sheets dashboard. If the email already exists, the lead is sent a polite "we already have your details" notice and the workflow stops there — no duplicate record is created.

3. **AI Lead Brain (AI Agent + Groq)** – For new leads, an AI Agent analyzes the lead's message along with their budget and timeline, and returns a structured JSON response containing:
   - `lead_score` (Hot / Warm / Cold)
   - `score_reason`
   - `industry_tag`
   - `power_signals` (urgency, budget, competitor mentions, deadlines)
   - `lead_summary`
   - `personalized_email`

4. **Parse AI Output** – Converts the AI's JSON response into individual fields so the rest of the workflow can use each piece of data independently.

5. **Route by Lead Score** – A Switch node directs the lead down one of three paths based on its score: Hot, Warm, or Cold.

6. **Log to Sheets (Hot / Warm / Cold)** – Appends a new row to the Google Sheets dashboard containing the lead's contact info, message, budget, timeline, AI-generated score, reasoning, industry tag, power signals, summary, and status.

7. **Send Reply Email (Hot / Warm / Cold)** – Sends the AI-written personalized email back to the lead, with a subject line tone adjusted to match the urgency of the lead (excited for Hot, friendly for Warm, casual for Cold).

8. **Alert Owner – Hot Lead** – For Hot leads only, an additional email is sent directly to the business owner with the lead's contact details, budget, timeline, detected power signals, and AI-generated summary — prompting an immediate follow-up.

---

## Output
- **For the lead:** A personalized, human-sounding reply email arrives in their inbox within seconds of submitting the form — referencing exactly what they asked for, not a generic template. Duplicate submitters instead receive a short acknowledgment letting them know their details are already on file.
- **For the business owner:** A live Google Sheets dashboard automatically fills with every lead, complete with an AI-generated score, reasoning, industry tag, detected power signals, and a quick summary — giving an instant overview of the entire pipeline. Hot leads additionally trigger a direct email alert so the owner knows exactly which leads to prioritize first.

---

## Screenshots
- The full n8n workflow canvas (all nodes connected)
- The Tally form design
- The Webhook trigger / test data
- The AI Lead Brain node output (JSON response)
- The Google Sheets dashboard with logged leads
- An example personalized reply email
- An example "Hot Lead Alert" email

---

---

## Key Learnings
- How to map and work with real-world webhook payloads (Tally), including extracting readable text from multiple-choice fields that send back option IDs instead of labels.
- How to design AI prompts that reliably return clean, structured JSON for use in downstream automation steps.
- How to use an AI Agent node with a Groq-hosted model (Llama 3.3 70B) for fast, low-latency AI responses inside a workflow.
- How to build conditional routing logic with a Switch node to handle different lead types differently.
- How to implement duplicate detection by looking up existing records before creating new ones, avoiding messy data and redundant outreach.
- How to think beyond "collecting data" toward building a system that makes decisions and takes action — the kind of automation that delivers real business value, not just convenience.
