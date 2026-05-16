# Yakazi
### AI-Powered Requirements Elicitation Training Chatbot

Yakazi simulates a software engineering client using GPT-4 and FAISS-based retrieval-augmented generation (RAG), giving software engineering students a realistic, repeatable environment to practise requirements elicitation — the skill of drawing out clear project requirements from a vague or non-technical client.

---

## The Problem

Requirements elicitation is one of the hardest skills to teach in software engineering. Static project briefs don't talk back. Role-playing exercises depend on whoever you can get in a room. Neither replicates the ambiguity, evasiveness, or evolving nature of a real client conversation.

Yakazi addresses this by acting as that client, grounded in a real project brief, intentionally vague, and capable of sustained multi-turn dialogue.

---

## How It Works
<img width="1572" height="925" alt="image" src="https://github.com/user-attachments/assets/9838525f-5938-4297-a8b1-7e57493ca942" />


The system runs three components in parallel:

```
project_brief.txt
      │
      ▼
┌─────────────────┐        ┌──────────────────┐        ┌────────────────┐
│  FAISS Server   │◄──────►│   RAG Service    │◄──────►│  Chatbot UI    │
│  (port 5001)    │        │  Flask (port 5000)│        │   (Tkinter)    │
└─────────────────┘        └──────────────────┘        └────────────────┘
```

1. **FAISS Server** — on startup, loads `project_brief.txt`, splits it into 5 sections, embeds each using OpenAI's embedding model, and stores them in a FAISS index for fast semantic search
2. **RAG Service (Flask)** — receives user queries, retrieves the top-k most relevant brief sections from FAISS, constructs a structured GPT-4 prompt combining the query, retrieved context, and conversation history, then returns a response
3. **Chatbot UI (Tkinter)** — a desktop chat interface with colour-coded message bubbles, a "Yakazi is typing..." indicator, and a one-click conversation summariser

### Key Features

- **RAG-grounded responses** : answers are always anchored to the project brief, not hallucinated
- **Adaptive questioning** : detects vague user queries and asks for clarification instead of guessing, simulating real client behaviour
- **Intentional ambiguity** : a `generate_vague_response()` layer rephrases direct answers into open-ended client-style language, encouraging students to dig deeper
- **Conversation history** : full multi-turn context is maintained and passed to GPT-4 on every exchange
- **Summarisation** : one-click summary of the full conversation, useful for post-session review
- **Swappable project briefs** : replace `project_brief.txt` with any project to simulate a different client

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Language model | OpenAI GPT-4 |
| Vector search | FAISS (Facebook AI Similarity Search) |
| Embeddings | OpenAI `text-embedding-ada-002` |
| Backend | Python / Flask |
| UI | Tkinter |
| API communication | Python `requests` |

---

## Getting Started

### Prerequisites

- Python 3.9+
- An OpenAI API key with GPT-4 access

### Installation

```bash
git clone https://github.com/Zyarix/yakazi.git
cd yakazi
pip install -r requirements.txt
```

### Configuration

Set your OpenAI API key as an environment variable:

```bash
# Windows
set OPENAI_API_KEY=your-api-key-here

# Mac/Linux
export OPENAI_API_KEY=your-api-key-here
```

### Running

```bash
python start.py
```

This will:
1. Start the FAISS server on port 5001 and embed the project brief (~15 seconds)
2. Start the Flask RAG service on port 5000
3. Open the Yakazi chat window

---

## Project Structure

```
yakazi/
├── start.py              # Launches all three components
├── faiss_server.py       # Document ingestion, embedding, and FAISS index
├── rag_service.py        # RAG pipeline, GPT-4 integration, conversation management
├── app.py                # Flask API layer
├── chatbot_ui.py         # Tkinter desktop UI
├── project_brief.txt     # The client's project (swap to change scenario)
└── requirements.txt
```

---

## Example Conversation

```
Yakazi: Hello, I am Yakazi. I have a project I'd like to discuss.
        Ask me anything!

You:    Can you give me an overview of the project?

Yakazi: The project involves building a crew rostering system for a UK-based
        airline. It will handle scheduling, leave requests, and shift swaps —
        though I'd rather not get into all the specifics just yet. What aspect
        are you most interested in?

You:    What are the legal requirements around crew hours?

Yakazi: There are regulations we need to comply with — something to do with
        the Civil Aviation Authority. I'd want the system to handle that
        automatically. Can you tell me more about what you'd need from us
        to make that work?
```

---

## Customising the Project Brief

Replace the contents of `project_brief.txt` with any project description. The FAISS server will re-embed it automatically on next startup. No code changes required.

---

## Architecture Notes

The FAISS server and Flask app run as independent processes to keep the vector search layer separate from the conversational logic. This makes it straightforward to swap FAISS for another vector store (Pinecone, Chroma, etc.) or replace GPT-4 with a different model without touching the UI or RAG logic.

---

## Author

**Ismail Jonkunda Ceesay** — [github.com/Zyarix](https://github.com/Zyarix)
