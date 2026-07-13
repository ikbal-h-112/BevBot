# BevBot — AI Lead Generation Chatbot for Beverage Distributors

An intelligent website chatbot built on n8n that answers visitor questions from a company knowledge base (RAG) and captures qualified leads into a Google Sheet — fully automated, running 24/7.

![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-orange)
![Claude](https://img.shields.io/badge/AI-Claude%20by%20Anthropic-blueviolet)
![Gemini](https://img.shields.io/badge/Embeddings-Google%20Gemini-blue)
![Google Sheets](https://img.shields.io/badge/CRM-Google%20Sheets-green)

---

## What It Does

1. A visitor opens the chat and asks a question ("Do you deliver to my area?", "What brands do you carry?").
2. The agent retrieves the answer from a loaded knowledge base (RAG) — it never guesses or hallucinates.
3. When the visitor shows interest, the bot naturally asks for their name and email.
4. The lead is saved to a Google Sheet, categorized by type (Retailer, Supplier, Career, Other).
5. The bot confirms the capture and tells the visitor the team will follow up.

No human intervention needed. Works after hours, weekends, holidays.

---

## Architecture

```
Chat Trigger → Summit Agent (Claude AI)
                    ├── Claude Brain (Anthropic Claude Sonnet)
                    ├── Memory (conversation context)
                    ├── Knowledge Lookup (RAG via in-memory vector store)
                    │       └── Embeddings (Google Gemini)
                    └── save_lead (Google Sheets tool)

Load Knowledge (run once) → Knowledge Doc → Store Knowledge
                                                ├── Embeddings (Google Gemini)
                                                ├── Document Loader
                                                └── Text Splitter
```

| Component | Technology | Role |
|-----------|-----------|------|
| Orchestration | n8n (self-hosted) | Workflow engine, chat widget, webhook hosting |
| AI Brain | Anthropic Claude Sonnet | Understands questions, decides actions, generates responses |
| Embeddings | Google Gemini | Converts knowledge text into searchable vectors for RAG |
| Knowledge Base | n8n In-Memory Vector Store | Stores and retrieves business FAQ chunks |
| Lead Storage | Google Sheets | Lightweight CRM — one row per captured lead |
| Chat Frontend | n8n Chat Widget | Embeddable chat bubble for any website |

**RAG:** the company FAQ is chunked, embedded, and stored in a vector store. Every answer is grounded in retrieved company information, not the model's general training.

**Agent with tools:** Claude decides when to search the knowledge base (`Knowledge Lookup`) and when to write a lead (`save_lead`), guided by the system prompt.

---

## Why It Pays For Itself

One new retail account (a bar, restaurant, or store) generates thousands in recurring orders over its lifetime. If the bot captures even one account per quarter that would otherwise have bounced from an unanswered website, it covers its cost many times over. Transferable to any B2B business with a knowledge base and inbound traffic.

---

## Setup

**Prerequisites:** n8n (self-hosted or cloud), Anthropic API key, Google Gemini API key (free tier), Google Sheets OAuth2 credentials.

1. Import `workflow.json` into n8n (Menu → Import from File).
2. Connect credentials: **Claude Brain** → Anthropic; **Embeddings (insert/query)** → Gemini; **save_lead** → Google Sheets OAuth2.
3. Create a Google Sheet with headers: `Name | Email | Interest` and point the **save_lead** node at it.
4. Edit the **Summit Knowledge Doc** node with your business FAQ (see `knowledge-template.txt`).
5. Run **Load Knowledge (run once)** to populate the vector store.
6. Open the chat and test.

**Deployment:** for a public website embed, n8n needs a public HTTPS URL — Cloudflare Tunnel for quick/free (`cloudflared tunnel --url http://localhost:5678`), or n8n Cloud / a VPS for production. The Chat Trigger node generates the embed code.

---

## Cost

| Item | Monthly Cost |
|------|-------------|
| Claude API (100 chats/mo) | ~$1–3 |
| Gemini Embeddings | ~$0.01 |
| n8n hosting | $0–50 (free if self-hosted) |
| Google Sheets | Free |
| **Total** | **~$1–53/mo** |

---

## Customizing For Another Business

1. Replace the FAQ text in the Knowledge Doc node (use `knowledge-template.txt` as the skeleton).
2. Update the agent's system prompt with the business name and lead types.
3. Adjust the Google Sheet columns if different lead fields are needed.
4. Re-run **Load Knowledge** after any FAQ change.

---

## Project Structure

```
bevbot/
├── workflow.json          # n8n workflow (import this)
├── knowledge-template.txt # FAQ document skeleton
└── README.md
```

## License

MIT

---

Built by [Ikbal Hossain](mailto:ikbalhossain0476@gmail.com) using n8n, Anthropic Claude, and Google Gemini.
