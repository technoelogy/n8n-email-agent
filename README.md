# n8n Email Agent

A chat-driven AI agent built in n8n that looks up a contact by name and sends them an email via Gmail — no manual address lookup, no copy-paste.

> "Email Priya the revised proposal deadline is 5 Oct" → agent finds Priya's address in the contact store → drafts → sends.

<!-- TODO: add a 20–30s GIF or screenshot of the chat + the sent email here -->
![demo](docs/demo.gif)

## What it does

- Takes a plain-English instruction in the n8n chat
- Resolves the recipient's name to an email address via a Pinecone vector search over a contacts list
- Drafts the email with an OpenAI chat model
- Sends it through Gmail
- Keeps short-term conversation context (Simple Memory), so follow-ups like "send the same to Rahul" work

## Architecture

```
Chat Trigger
   └── AI Agent
        ├── Chat model: OpenAI
        ├── Memory: Simple Memory
        └── Tools
             ├── Pinecone Vector Store — contact lookup (name → email)
             └── Gmail — send message
```

<!-- TODO: confirm how contacts get into Pinecone (e.g. Google Sheets → embeddings → Pinecone) and add that flow here -->

## Stack

| Layer | Tool |
|---|---|
| Orchestration | n8n (self-hosted, Docker) |
| LLM | OpenAI (model: `TODO`) |
| Vector store | Pinecone (index: `TODO`, embedding model: `TODO`) |
| Email | Gmail API (OAuth via Google Cloud project) |

## Setup

1. Import `workflow/email-agent.json` into n8n (Workflows → Import from File).
2. Create credentials in n8n for: OpenAI, Pinecone, Gmail (OAuth2).
3. Create a Pinecone index and load your contacts (name, email, company, notes).
4. Open the chat trigger and send: `Email <name> about <topic>`.

## Repo structure

```
workflow/email-agent.json   # exported n8n workflow (credentials stripped)
docs/demo.gif               # demo recording
docs/architecture.png       # canvas screenshot
sample-data/contacts.csv    # dummy contacts for testing
```

## Limitations / next steps

- TODO: e.g. no human-approval step before send
- TODO: e.g. single sender account; no attachments yet

## Author

Noel Gomes — [GitHub](https://github.com/technoelogy) · [X](https://x.com/technoelog)
