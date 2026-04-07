# 🧠 Multi-Modal RAG Telegram Assistant

An enterprise-grade, self-hosted Telegram AI Assistant built on n8n. This project features a dual-lane conversational interface (Text & Voice) powered by Groq's Whisper API, backed by a Pinecone Vector Database, and features an asynchronous, multimodal data ingestion pipeline capable of reading complex PDFs, PPTs, and handwritten notes via LlamaParse and Google Drive OAuth2.

## 🏗️ System Architecture

This project is separated into two distinct n8n workflows to ensure production stability:

### 1. The Conversational Engine (Telegram Bot)
* **The Bridge:** Persistent Ngrok tunnel exposing the local n8n instance.
* **The Router:** Dynamic conditional routing separating text messages from voice notes.
* **The Ears:** Custom JavaScript payload formatting that intercepts Telegram `.oga` files and formats them into standard `.ogg` files.
* **The Translator:** Groq API (`whisper-large-v3`) for near-instant speech-to-text transcription.
* **The Brain:** n8n AI Agent connected to a Pinecone Vector Store, utilizing RAG (Retrieval-Augmented Generation) to answer queries based on custom knowledge.

### 2. The Multimodal Ingestion Pipeline
* **Cloud Acquisition:** Google Drive API (via OAuth2) for securely pulling targeted documents.
* **The Vision Parser:** LlamaParse API asynchronous polling. Physically "looks" at complex files (scanned handwritten notes, PPTs, graphs) and translates them into machine-readable Markdown.
* **The Splitter:** Recursive Character Text Splitter (1000 chunk size / 100 overlap).
* **The Database:** Pushes chunked vector embeddings directly into the Pinecone index.

## ⚙️ Prerequisites

To run this project locally, you will need:
* **Node.js** (for running `npx n8n`)
* **Ngrok** (for the webhook tunnel)
* **API Keys:**
  * Telegram Bot Token (via BotFather)
  * Groq API Key (for Whisper-large-v3)
  * Pinecone API Key & Index Host URL
  * LlamaParse API Key
  * Google Cloud Project Client ID & Secret (for Drive OAuth2)

## 🚀 Quick Start / Boot Sequence

> **⚠️ Version Note:** This project relies on n8n version `2.9.4`. Later versions (2.10.2 - 2.15.0) introduced a core bug in the `multipart/form-data` proxy that breaks binary audio uploads to Cloudflare-protected APIs like Groq. 

### Step 1: Establish the Bridge
Open your first terminal and start your persistent Ngrok tunnel:
```bash
ngrok http --domain=YOUR-CUSTOM-NAME.ngrok-free.dev 5678
