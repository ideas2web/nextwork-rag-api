# Nextwork RAG API

A Retrieval-Augmented Generation (RAG) API built with FastAPI, ChromaDB, and Ollama for intelligent question-answering over a knowledge base.

## Overview

This API provides a simple yet powerful RAG system that allows you to:
- Store documents in a vector database (ChromaDB)
- Query the knowledge base with natural language questions
- Get AI-generated answers based on relevant context
- Dynamically add new content to the knowledge base

## Architecture

The solution consists of three main components:

1. **FastAPI Backend** ([app.py](app.py)) - REST API server with endpoints for querying and adding knowledge
2. **ChromaDB** - Vector database for storing document embeddings and performing similarity search
3. **Ollama** - Local LLM (TinyLlama) for generating contextual answers

## Features

- **Query Endpoint** (`/query`) - Ask questions and get AI-generated answers based on relevant context
- **Add Knowledge Endpoint** (`/add`) - Dynamically add new content to the knowledge base
- **Persistent Storage** - Documents and embeddings are stored in `./db` directory
- **Fast Retrieval** - Uses vector similarity search for finding relevant context

## Prerequisites

- Python 3.8+
- [Ollama](https://ollama.ai/) installed locally
- TinyLlama model pulled in Ollama

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd nextwork-rag-api
```

2. Create a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install fastapi chromadb ollama uvicorn
```

4. Pull the TinyLlama model in Ollama:
```bash
ollama pull tinyllama
```

## Usage

### Initial Setup

1. Add initial data to the knowledge base using [embed.py](embed.py):
```bash
python embed.py
```

This script reads [k8s.txt](k8s.txt) and stores it in ChromaDB.

### Running the API

Start the FastAPI server:
```bash
uvicorn app:app --reload
```

The API will be available at `http://localhost:8000`

### API Endpoints

#### 1. Query Knowledge Base

**POST** `/query`

Ask a question and get an AI-generated answer based on relevant context.

**Parameters:**
- `q` (string) - Your question

**Example:**
```bash
curl -X POST "http://localhost:8000/query?q=What is Kubernetes?" -H "Content-Type: application/json"
```

**Response:**
```json
{
  "answer": "Kubernetes is a container orchestration platform used to manage containers at scale."
}
```

#### 2. Add New Knowledge

**POST** `/add`

Dynamically add new content to the knowledge base.

**Parameters:**
- `text` (string) - The content to add

**Example:**
```bash
curl -X POST "http://localhost:8000/add?text=Docker is a containerization platform" -H "Content-Type: application/json"
```

**Response:**
```json
{
  "status": "success",
  "message": "Content added to knowledge base",
  "id": "6aa42b1e-4e0b-439d-8f71-2799672b98b7"
}
```

## Project Structure

```
nextwork-rag-api/
├── app.py              # Main FastAPI application
├── embed.py            # Script to populate initial knowledge base
├── k8s.txt             # Sample knowledge base content
├── db/                 # ChromaDB persistent storage
│   ├── chroma.sqlite3  # Database file
│   └── ...
└── venv/               # Python virtual environment
```

## How It Works

1. **Document Storage**: Documents are stored in ChromaDB with automatic embedding generation
2. **Query Processing**: When a query is received:
   - ChromaDB performs similarity search to find the most relevant document
   - The retrieved context is passed to TinyLlama
   - TinyLlama generates a contextual answer
3. **Response**: The AI-generated answer is returned to the user

## API Documentation

Once the server is running, visit:
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

## Development

### Adding Initial Data

Modify [k8s.txt](k8s.txt) with your knowledge base content, then run:
```bash
python embed.py
```

### Using Different Models

To use a different Ollama model, modify [app.py](app.py):
```python
answer = ollama.generate(
    model="your-model-name",  # e.g., "llama2", "mistral"
    prompt=f"Context:\n{context}\n\nQuestion: {q}\n\nAnswer clearly and concisely:"
)
```

## Database

The ChromaDB database is stored in the `./db` directory and persists between restarts. To reset the database, simply delete the `./db` directory.

## Tech Stack

- **FastAPI** - Modern, fast web framework for building APIs
- **ChromaDB** - Open-source embedding database for vector search
- **Ollama** - Run large language models locally
- **TinyLlama** - Compact 1.1B parameter language model

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is open source and available under the MIT License.
