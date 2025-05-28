# 🧊 TundrAI – Documentation Chatbot Project

Short description: TundrAI is a self-hosted, open-source AI chatbot that answers user questions based on your project's documentation using local LLMs and vector search.

## 🧭 Project Overview

TundrAI is a **fully open-source, self-hosted AI-powered chatbot** that answers user questions based on documentation content. It will be written in **TypeScript**, with a **React frontend** and a **Node.js backend**, and will use a **self-hosted inference server running open-source LLMs**.

While the primary use case is to support a specific software product, the project is designed to be **documentation-agnostic** — it should work with any set of supported documents and can be used across different software projects or use cases.

---

## 🔧 Tech Stack

| Layer        | Technology              |                         |
| ------------ | ----------------------- | ----------------------- |
| Frontend     | React                   |                         |
| Backend      | NestJS + TypeScript     |                         |
| Inference    | Ollama (or similar)     |                         |
| Embeddings   | sentence-transformers   |                         |
| Vector Store | Qdrant or Chroma        |                         |
| Container    | Docker + Docker Compose | Docker + Docker Compose |

Kubernetes readiness is a goal, but Kubernetes configuration will not be included in this version.

---

## 🧠 Core Workflow

### Step 1: Ingest Documentation

* Supported formats: **PDF**, **Markdown**, **HTML**, and **plain text**
* Documents are parsed and split into chunks
* Chunks are embedded (converted into numerical vectors)
* Embeddings are stored in a vector database (e.g., Qdrant)

### Step 2: Handle User Questions

* User types a question in the chat UI
* The backend embeds the question
* The vector DB retrieves the most similar chunks from the docs

### Step 3: Generate Answer

* The backend builds a prompt using the question + top document chunks
* The prompt is sent to a local inference server (LLM)
* The answer is returned to the user

---

## 📂 Document Ingestion Workflow

* All documentation files to be processed are placed in a dedicated directory, e.g., `./data/docs/`
* This path is configurable via `.env`, e.g., `DOCS_FOLDER_PATH=./data/docs/`
* A `POST /ingest` API endpoint is exposed by the backend
* When called, it scans the folder, parses the documents, generates embeddings, and indexes them into the vector DB
* Optional: A `?refresh=true` query param can trigger a full re-ingestion

### Versioning

* Document versioning via Git is optional but recommended
* We may store the documentation in a separate Git repo and mount or sync it into the `data/docs/` folder
* This allows change tracking and enables automated re-ingestion on updates via hooks or pipelines

---

## 🧰 Components & Microservices

* `frontend/`: React-based UI
* `backend/`: NestJS API
* `inference-server/`: Containerized LLM (e.g., via Ollama)
* `vector-db/`: Qdrant or equivalent

---

## 🔐 Authentication & Access

* User authentication is **configurable**
* Chatbot can be used **publicly or privately**, depending on a setting in a `.env` or config file

---

## 🌍 Language Support

* MVP will support **English and French**
* Future support for additional languages is planned

---

## ⚙️ Configuration & Admin Controls

* MVP: Admin settings via `.env` or config files
* Future: Web-based admin panel to manage:

  * Document ingestion
  * Chat behavior (strict RAG or general answers)
  * Language settings
  * Logs and analytics

---

## ⚙️ Prompt Behavior (RAG Mode)

* The chatbot's behavior can be set to:

  * **Strict RAG** (only answer from docs)
  * **Fallback Mode** (try to generalize if no answer is found)
* This is configurable via `.env` or future admin UI

---

## 🗂️ Deployment

* Fully Dockerized with a `docker-compose.yml`
* No GitLab CI/CD or Kubernetes config included, but future integration with Azure is planned

---

## 🧩 Architecture Diagrams

### High-Level Architecture

```
+-------------+     HTTPS     +--------------+     gRPC/HTTP     +-------------------+
|             | ------------> |              | ---------------> |                   |
|   Frontend  |               |   Backend    |                  |  Inference Server |
| (React App) | <------------ | (Node/TS)    | <--------------- | (Ollama + Model)  |
|             |     API       |              |     LLM Output   |                   |
+-------------+               +--------------+                  +-------------------+
                                    |
                                    | Embedding & Search
                                    v
                            +---------------+
                            | Vector DB     |
                            | (Qdrant etc.) |
                            +---------------+
```

### RAG Flow

```
        [User] Asks: "How do I reset my password?"
               |
               v
      +------------------+
      |     Backend      |
      +------------------+
               |
               | Generate question embedding
               v
      +------------------+
      |   Embedding Model|
      +------------------+
               |
               | Search in vector DB
               v
      +------------------+
      |   Vector DB       |
      +------------------+
               |
               | Return top-k document chunks
               v
      +--------------------------+
      | Build prompt w/ context |
      +--------------------------+
               |
               | Send prompt
               v
      +----------------------+
      |  LLM Inference Server|
      +----------------------+
               |
               | AI Answer
               v
           [Frontend UI]
```

---

## 🚧 MVP Scope

* Ingest PDFs, Markdown, HTML, and text
* English and French support
* Answer user questions using RAG
* Basic `.env`/config-based setup
* Dockerized deployment

---

## 🎯 Future Enhancements

* Admin web panel
* Realtime logging and feedback loop
* Multi-user support and roles
* Analytics dashboard
* DOCX and EPUB support
* Automatic re-ingestion on file change
* Azure deployment pipelines
