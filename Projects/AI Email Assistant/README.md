# AI Lead Qualification & Follow-Up System for Real Estate

## Overview
An end-to-end n8n automation that captures incoming real estate leads from a web form, uses AI to instantly qualify each lead as **Hot**, **Warm**, or **Cold**, sends a personalized follow-up email tailored to that lead's level of interest, and logs every lead into a centralized Google Sheet for the sales team to track.

---

## Problem
Real estate agencies generate leads through Facebook Ads, Google Ads, and landing pages — but most teams take hours (or days) to respond. By the time someone follows up, the lead has often already gone cold or contacted a competitor. Manually reading every form submission, deciding how serious the lead is, and writing a personalized response simply doesn't scale once lead volume increases.

---

## Solution
This n8n workflow removes the delay and the manual work entirely:

- The moment a prospect submits a lead form, the workflow is triggered instantly
- An AI agent reads the lead's budget, timeline, and intent, and scores them as Hot, Warm, or Cold based on a defined qualification logic
- Based on that score, the lead automatically receives one of three tailored emails — including a direct booking link for hot leads
- Every lead, along with its score and details, is logged into a Google Sheet that acts as a simple CRM for the sales team

The result: leads are engaged within seconds, and the team always has an up-to-date, scored list of prospects to follow up on.

---

## Tools Used
- **n8n** – core workflow automation
- **Webhooks** – instant lead capture
- **Tally.so** – lead intake form
- **Groq (Llama 3.3 70B)** – AI lead qualification & personalized reply generation via LangChain AI Agent node
- **Gmail** – automated email delivery
- **Google Sheets** – lead tracking / lightweight CRM
- **Calendly** – appointment booking for hot leads

---

## Workflow Steps
1. **Trigger** – A Webhook node receives lead data the instant a prospect submits the Tally intake form (name, email, phone, budget, timeline, intent, location)
2. **Data Extraction** – A Set node cleans and structures the raw webhook payload into usable fields
3. **AI Qualification** – A Groq-powered AI Agent analyzes the lead's budget and timeline, returns a score (Hot/Warm/Cold), a reason for the score, and a personalized reply snippet — all as structured JSON
4. **Parsing** – A second Set node parses the AI's JSON output and merges it back with the original lead details
5. **Routing** – A Switch node routes the lead down one of three paths based on its score
6. **Action** – Each path sends a tailored email via Gmail:
   - **Hot** → personalized email + direct Calendly booking link
   - **Warm** → personalized email + invitation to reply with questions
   - **Cold** → polite acknowledgment email for future follow-up
7. **Output / Logging** – Each branch logs the lead's full details and score into a Google Sheet for the sales team

---

## Output
- **Hot Lead Example:** Lead receives an email referencing their specific budget/location/intent, plus a "Book your free call here" Calendly link
- **Warm Lead Example:** Lead receives a personalized email encouraging them to reply with questions, kept warm for future follow-up
- **Cold Lead Example:** Lead receives a friendly acknowledgment email, logged for long-term nurture
- **Google Sheet "Leads" tab:** Every submission appears as a new row with Date, Name, Email, Phone, Budget, Timeline, Looking To, Location, and AI-assigned Score — giving the sales team an instant, sorted view of who to prioritize

---

## Screenshots

![Preview](./Screenshots/1.png)
![Preview](./Screenshots/2.png)

---

---

## Key Learnings
- How to capture and structure data from a third-party form (Tally) via webhooks in n8n
- Using AI Agents with Groq's Llama 3.3 70B model to perform structured reasoning tasks (lead scoring) and return clean, parseable JSON
- Designing prompts that enforce strict output formats so downstream nodes can reliably parse AI responses
- Building conditional logic with Switch nodes to create personalized, branching automation paths
- Connecting multiple branches back to a shared Google Sheet for unified lead tracking
- Designing an automation around a real, validated market need (sourced directly from live freelance job postings) rather than building speculatively
