# n8n Email Agent

A chat-driven AI agent built in n8n that looks up a contact by name and sends them an email via Gmail — no manual address lookup, no copy-paste.

> "Email Priya the revised proposal deadline is 5 Oct" → agent finds Priya's address in the contact store → drafts → sends.

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

Contacts are stored in Pinecone (namespace `Contacts`), one record per person in the format `Name: X | Email: Y`. The agent only uses an email that appears in the same record as the matched name, and asks the user if no match is found.

## Stack

| Layer | Tool |
|---|---|
| Orchestration | n8n |
| LLM | OpenAI `gpt-4o-mini` |
| Vector store | Pinecone (retrieve-as-tool) + OpenAI Embeddings |
| Email | Gmail API (OAuth via Google Cloud project) |

## Setup

1. Import `workflow/email-agent.json` into n8n (Workflows → Import from File).
2. Create credentials in n8n for: OpenAI, Pinecone, Gmail (OAuth2).
3. Create a Pinecone index and load your contacts into a `Contacts` namespace, one record per person as `Name: X | Email: Y` (see `sample-data/contacts.csv`). Select your index in the Pinecone Vector Store node.
4. Open the chat trigger and send: `Email <name> about <topic>`.

## Repo structure

```
workflow/email-agent.json   # exported n8n workflow (no secrets — re-link your own credentials)
sample-data/contacts.csv    # dummy contacts for testing
```

## Limitations / next steps

- Sends immediately — no human-approval step before the email goes out
- Simple Memory is short-term only; conversation context resets between sessions
- Plain-text emails, no attachments

## Author

Noel Gomes — [GitHub](https://github.com/technoelogy) · [X](https://x.com/technoelog)
