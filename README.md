# ctfy-decision-os

This repository is the **Tracer Project** for the **Codetechify Decision-Lifecycle OS**.

The goal is to build and validate the core **RAG-Backbone** (the “Tracer”), which consists of:

- **VaultWell (Simulated):** Hand-crafted JSON files representing the “Source of Truth”.
- **Storage:** Convex (SoR Database) & Qdrant (Vector Store).
- **Indexer (Write-Path):** n8n workflows.
- **Query Engine (Read-Path):** FlowiseAI chains.
- **LLM Service:** Ollama (running on host).
- **Cockpit (Minimal):** Next.js app.

---

## 🧱 Project Structure

This is a **Level-0 Monorepo** (a single repo containing all project assets).

```
/ctfy-decision-os
│
├── docker-compose.yml        # Project-specific services (Qdrant, n8n, etc.)
│
├── /genesis-block/           # Hand-crafted JSON SoR files (Task 1.2)
│
├── /nextjs-cockpit/          # The Next.js application
│   ├── /convex/              # Convex schema and functions (Task 1.3)
│   ├── package.json          # <-- Modify the 'dev' script here
│   ├── .env.local            # <-- Will contain Convex/Flowise keys
│
├── /n8n-workflows/           # JSON exports of n8n workflows
├── /flowise-brains/          # JSON exports of FlowiseAI chains
│
├── /qdrant-storage/          # Persistent volume data
├── /n8n-data/                # Persistent volume data
├── /flowise-data/            # Persistent volume data
├── /postgres-data/           # Persistent volume data
│
└── README.md
```

---

## ⚙️ Prerequisites

Before you begin, ensure you have the following installed on your workstation:

- **git**
- **Node.js** (v18+ recommended)
- **pnpm** (we will use pnpm for this project)
- **Docker & Docker Compose**

It is also assumed you have a global **Ollama** service running on `http://localhost:11434`.

---

## 🚀 Get Started: Step-by-Step

### 1️⃣ Clone & Setup Folders

```bash
# Clone your repository
git clone <your-repo-url>
cd ctfy-decision-os

# Create directories for Docker persistent volumes
mkdir qdrant-storage n8n-data flowise-data postgres-data

# Create folders for project structure
mkdir genesis-block nextjs-cockpit n8n-workflows flowise-brains
```

---

### 2️⃣ Infrastructure: Docker Services

Copy the content from the `docker-compose.yml` artifact (created earlier) into a file in the root of this project.

Then, start all project-specific services:

```bash
# Start all services
docker-compose up -d

# Verify containers are running
docker-compose ps
# You should see: qdrant_project, n8n_project, flowise_project, postgres_project
```

---

### 3️⃣ Application: Next.js & Convex Setup

```bash
# Navigate into the cockpit directory
cd nextjs-cockpit

# Initialize a new Next.js project
npx create-next-app@latest .

# Install dependencies
pnpm install

# Install Convex
pnpm add convex

# Initialize Convex (links folder to new Convex project)
npx convex init
```

---

### 4️⃣ Application Configuration

#### A. Configure Next.js Port (⚠️ Critical)

Your global services use ports 3000/3001, so change the default port.

In `/nextjs-cockpit/package.json`, modify the **dev** script:

```json
"scripts": {
  "dev": "next dev -p 3005",
  "build": "next build",
  "start": "next start",
  "lint": "next lint"
}
```

#### B. Create `.env.local` File

Create `/nextjs-cockpit/.env.local`:

```bash
# Convex URL (from your Convex dashboard)
NEXT_PUBLIC_CONEX_URL=https://<your-project-name>.convex.cloud

# Flowise Project URL (from docker-compose.yml)
NEXT_PUBLIC_FLOWISE_URL=http://localhost:3001
```

---

### 5️⃣ Running the Full System

You need **three terminals** running simultaneously.

#### Terminal 1 – Root: Docker

```bash
cd /path/to/ctfy-decision-os
docker-compose up
```

#### Terminal 2 – Cockpit: Convex Dev

```bash
cd nextjs-cockpit
npx convex dev
```

#### Terminal 3 – Cockpit: Next.js App

```bash
cd nextjs-cockpit
pnpm run dev
```

---

## 🌐 Access Ports (Quick Reference)

| Service       | Host Port               | Internal Port | Notes                            |
|----------------|-------------------------|----------------|-----------------------------------|
| Next.js App    | http://localhost:3005   | -              | Main UI                           |
| FlowiseAI      | http://localhost:3001   | 3000           | Project-specific RAG chains       |
| n8n            | http://localhost:5683   | 5678           | Project-specific ETL workflows    |
| Qdrant         | http://localhost:6334   | 6333           | Project-specific vector store     |
| Postgres       | http://localhost:5433   | 5432           | DB for n8n/Flowise                |
| Ollama         | http://localhost:11434  | -              | Global service (on Host)          |

---

© 2025 **Codetechify Project** – Decision-Lifecycle OS Prototype