# n8n Threat Intelligence Agent

> Automated threat intel pipeline — ingests live CVEs from NVD, 
> aggregates security RSS feeds, uses Gemini AI to summarize and 
> prioritize findings, and delivers a daily digest to Gmail.

## Overview

Built to reduce manual threat intel triage time. Instead of 
checking multiple sources daily, this workflow runs automatically 
every morning and delivers a structured digest covering the most 
critical CVEs and security news from the past 24 hours.

## Architecture

Schedule Trigger (daily)

↓

NVD API (CVE feed) ──→ Merge → Gemini AI Agent → Gmail Digest

RSS Security Feed ──↗

## Tech Stack

- [n8n](https://n8n.io/) — self-hosted workflow automation
- [Google Gemini](https://aistudio.google.com/) — AI summarization (free tier)
- [NVD API](https://nvd.nist.gov/developers/vulnerabilities) — NIST CVE data
- RSS feeds — security news aggregation
- Gmail — digest delivery

## What it produces

A daily email digest containing:
- Top 5 most critical CVEs with severity, affected products, 
  and MITRE ATT&CK technique mapping where applicable
- Top 3 security news summaries
- Actionable intelligence formatted for quick triage

## Setup

### Prerequisites
- n8n self-hosted (Node.js v20 LTS)
- Google Gemini API key (free at aistudio.google.com)
- Gmail OAuth2 credentials (Google Cloud Console)

### Installation

**1. Install n8n**
```bash
# Install nvm-windows first, then:
nvm install 20.19.0
nvm use 20.19.0
npm install -g n8n
n8n start
```

**2. Import the workflow**
- Open n8n at `http://localhost:5678`
- Go to Workflows → Import
- Upload `workflow.json` from this repo

**3. Configure credentials**
- Add your Gemini API key to the Google Gemini Chat Model node
- Connect Gmail via OAuth2 (see Google Cloud Console setup below)

**4. Activate**
- Toggle the workflow to Active in the top right
- It will run daily at your configured time

### Google Cloud Console Setup (Gmail OAuth)
1. Create a project at console.cloud.google.com
2. Enable the Gmail API
3. Create OAuth2 credentials (Web Application type)
4. Add `http://localhost:5678/rest/oauth2-credential/callback` 
   as an authorized redirect URI
5. Add your Gmail as a test user under Audience settings

## Troubleshooting

| Issue | Cause | Fix |
|---|---|---|
| `503 Service Unavailable` on Gemini | Free tier rate limit | Wait 10 min and retry, or switch to `gemini-1.5-flash` |
| Gmail OAuth `access_denied` | Not added as test user | Add your email under Google Cloud → Audience → Test Users |
| n8n command not found | Wrong Node version | Use nvm to switch to Node 20 LTS |
| n8n bin missing after install | Node 26 incompatibility | Downgrade to Node 20.19.0 via nvm-windows |

## Roadmap

- [ ] Add MITRE ATT&CK technique mapping per CVE
- [ ] Slack/Teams delivery option  
- [ ] CISA KEV feed integration
- [ ] Severity scoring layer
- [ ] Auto-generate draft Sigma rules per CVE

## License

MIT
