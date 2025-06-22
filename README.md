
AI Chatbot with Ollama, n8n, and PGVector

This project is a local AI chatbot built using:

- n8n for workflow automation  
- Ollama to run local large language models (LLMs) like Mistral or LLaMA2  
- PostgreSQL with PGVector for storing chat memory and document embeddings  
- External tools like a calculator and SerpAPI for added functionality

 What It Does

- Agent 1 handles real-time chat:
  - Generates responses using Ollama
  - Stores and retrieves context from PGVector
  - Uses tools like SerpAPI and calculator to enhance answers

- Agent 2 handles document input:
  - On form submission, it creates embeddings with Ollama
  - Stores the data in PGVector for use during future chats

The entire system runs locally using Docker Desktop.
 AI Agent Workflow Automation with Ollama, n8n, and PGVector

This project is a powerful local Generative AI automation system built with [Ollama](https://ollama.com/), [n8n](https://n8n.io/), and [PostgreSQL + PGVector](https://github.com/pgvector/pgvector), all running in Docker. It includes a multi-agent workflow that enables:

- Real-time chat with local LLMs
- Memory storage and vector embeddings using PGVector
- External tool integrations like calculators and web search
- A document ingestion pipeline that automatically embeds knowledge on form submission

 Project Overview

 Agent 1: AI Chat Agent

- Trigger: Listens for chat messages
- Model: Uses Ollama with a local LLM (e.g., `mistral`)
- Memory: Saves chat context in PostgreSQL with PGVector
- Tools Integrated:
  - Calculator (math evaluation)
  - SerpAPI (real-time search)
  - Embeddings via Ollama
- Goal: Respond contextually and intelligently to user queries

 Agent 2: Document Embedder

- Trigger: On form submission
- Function:*
  - Accepts user input or documents
  - Generates vector embeddings using Ollama
  - Stores the document + embeddings into PGVector
- Use Case: Feed knowledge into the chatbot memory
![Embed Workflow](./assets/agent2-workflow.png)

 Tech Stack

-------------------------------------------------------
| Tool         | Role                                 |
|--------------|--------------------------------------|
|  Docker      | Containerization                     |
|  Ollama      | Local LLMs (e.g., Mistral)           |
|  n8n         | Low-code automation and workflows    |
|  PostgreSQL  | Database for memory + embeddings     |
|  PGVector    | Vector storage and similarity search |
|  SerpAPI     | Live web search                      |
|  Calculator  | On-demand evaluation                 |
-------------------------------------------------------


Setup Instructions
1.
N8N Setup
Documentation
Official N8N Docker Docs: https://docs.n8n.io/hosting/installation/docker/#prerequisites
Step 1: Create N8N Data Volume
docker volume create n8n_data
Step 2: Run N8N Container
docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
Access N8N: http://localhost:5678

2.
N8N RAG Agent Setup
Step 1: Create Local Network
Create a dedicated network for all services to communicate:

docker network create srm
Step 2: Create PostgreSQL Database with Vector Support
docker run --name pgvector-container -e POSTGRES_USER=myuser -e POSTGRES_PASSWORD=mypassword -e POSTGRES_DB=mydatabase -p 5432:5432 -d ankane/pgvector
Step 3: Connect Services to Network
Connect both N8N and PostgreSQL containers to the shared network:

//Connect N8N container to network
docker network connect srm {your_n8n_container_name}

//Connect PostgreSQL container to network
docker network connect srm {your_postgres_container_name}
Note: Replace {your_n8n_container_name} and {your_postgres_container_name} with the actual container names from your setup.

Troubleshooting
Common Issues
Docker Engine Not Running: Restart your computer
WSL Issues: Run wsl --update
Port Conflicts: Make sure ports 3000, 5678, and 5432 are not in use by other applications
Container Connection Issues: Verify containers are connected to the srm network using docker network inspect srm
Service URLs
Open WebUI: http://localhost:3000
N8N: http://localhost:5678
PostgreSQL: localhost:5432

Network Architecture
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────────┐
│   Open WebUI    │    │       N8N        │    │   PostgreSQL +     │
│  (Port 3000)    │    │   (Port 5678)    │    │     pgvector       │
│                 │    │                  │    │   (Port 5432)      │
└─────────────────┘    └──────────────────┘    └─────────────────────┘
                                │                          │
                                └──────────┬───────────────┘
                                          │
                                   ┌──────▼──────┐
                                   │  srm network│
                                   └─────────────┘
This setup creates a complete environment for running Open WebUI and N8N with RAG (Retrieval-Augmented Generation) capabilities using PostgreSQL with vector extensions.
