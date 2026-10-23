# 01 — AI Lead Qualifier & CRM Automation

**Problem:** Website enquiries sit in an inbox for hours. Hot leads go cold, and sales waste time on junk enquiries.

**Solution:** Every form submission is scored by AI the second it arrives. Hot leads (score ≥ 70) are saved to the CRM sheet and alerted to the sales Slack channel instantly; every other lead is still logged with its score and reason, so nothing is lost.

## Flow
`Lead Form Webhook → Normalise Lead → AI Score & Qualify (OpenAI) → Parse AI Result → Hot Lead? → CRM Sheet + Slack alert / Lead log`

The AI returns strict JSON: `{score, category (hot/warm/cold), reason, follow_up_email}` — the follow-up email draft is generated in the same call, so a rep can send it in one click.

## Setup
1. Import `workflow.json` into n8n.
2. Create a Header Auth credential for OpenAI (`Authorization: Bearer <your key>`) and select it in the *AI Score & Qualify* node.
3. Connect Google Sheets (create a sheet with columns: name, email, company, message, budget, score, category, reason, follow_up_email, received_at) in both Sheets nodes, and a Slack credential + `#sales` channel in the Slack node.
4. Activate the workflow and POST a lead to the webhook URL.

## Sample input
```json
POST /webhook/lead-form
{ "name": "Priya Shah", "email": "priya@finlytic.com", "company": "Finlytic (40 staff)",
  "budget": "$2,000/month", "message": "We need a customer dashboard built this quarter, our current tool is too slow. Can we talk this week?" }
```
Expected: score in the 80s, category `hot`, a Slack alert, and a personalised follow-up draft in the sheet.

## Resume bullet
Built an AI lead-qualification automation in n8n + OpenAI that scores inbound leads 0–100 in real time, routes hot leads to sales via Slack, and logs every lead with an AI-drafted follow-up to a Google Sheets CRM.

## Interview talking points
- Why JSON mode matters: the workflow branches on `score`, so the AI output had to be machine-readable, not prose — one malformed reply would break routing, so parsing is isolated in a Code node.
- Scoring is a prompt-design problem: fit, budget clarity, urgency and intent — explain how you'd tune the weights per business.
- Extension ideas: dedupe by email, enrich with Clearbit/Apollo, push hot leads into HubSpot instead of Sheets.
