# 📚 LangChain Embeddings - Complete Learning Guide

Welcome to the **Embeddings Module** of the LangChain learning series! This guide provides a comprehensive overview of text embeddings and how to use them with LangChain.

---

## 🗺️ Module Overview

```mermaid
graph TB
    subgraph "📖 4-Embeddings Module"
        A[4.1 OpenAI Embeddings] --> D[Vector Stores]
        B[4.2 Ollama Embeddings] --> D
        C[4.3 HuggingFace Embeddings] --> D
        D --> E[Similarity Search]
        E --> F[RAG Applications]
    end
    
    style A fill:#10a37f,color:#fff
    style B fill:#ff6b6b,color:#fff
    style C fill:#ffcc00,color:#000
    style D fill:#6c5ce7,color:#fff
    style E fill:#00b894,color:#fff
    style F fill:#0984e3,color:#fff
```

---

## 🎯 What Are Embeddings?

**Embeddings** transform text into numerical vectors that capture semantic meaning. Similar texts have similar vectors, enabling powerful semantic search and AI applications.

```mermaid
flowchart LR
    subgraph Input
        T1["🔤 'Hello World'"]
        T2["🔤 'Hi Everyone'"]
        T3["🔤 'Buy groceries'"]
    end
    
    subgraph "Embedding Model"
        E[("🧠 Neural Network")]
    end
    
    subgraph "Vector Space"
        V1["📍 [0.12, -0.45, ...]"]
        V2["📍 [0.11, -0.44, ...]"]
        V3["📍 [0.87, 0.23, ...]"]
    end
    
    T1 --> E --> V1
    T2 --> E --> V2
    T3 --> E --> V3
    
    V1 -.->|"Close (Similar)"| V2
    V1 -.->|"Far (Different)"| V3
```

---

## 📓 Notebooks in This Module

| # | Notebook | Provider | Cost | Description |
|---|----------|----------|------|-------------|
| 1 | [4.1-embedding.ipynb](4.1-embedding.ipynb) | 🟢 **OpenAI** | 💰 Paid | Cloud-based, highest quality embeddings |
| 2 | [4.2-ollamaemnedding.ipynb](4.2-ollamaemnedding.ipynb) | 🦙 **Ollama** | 🆓 Free | Local, private embedding generation |
| 3 | [4.3-huggingface.ipynb](4.3-huggingface.ipynb) | 🤗 **HuggingFace** | 🆓 Free | Open-source sentence transformers |

---

## 🏗️ Architecture Overview

```mermaid
graph TB
    subgraph "📄 Documents"
        DOC[Raw Text Documents]
    end
    
    subgraph "📝 Processing"
        LOAD[Document Loader]
        SPLIT[Text Splitter]
    end
    
    subgraph "🧠 Embedding Options"
        OAI[OpenAI API]
        OLL[Ollama Local]
        HF[HuggingFace]
    end
    
    subgraph "🗃️ Storage"
        VS[(Vector Store<br/>ChromaDB)]
    end
    
    subgraph "🔍 Retrieval"
        QUERY[User Query]
        SEARCH[Similarity Search]
        RESULTS[Relevant Chunks]
    end
    
    DOC --> LOAD --> SPLIT
    SPLIT --> OAI & OLL & HF
    OAI & OLL & HF --> VS
    QUERY --> OAI & OLL & HF
    VS --> SEARCH
    QUERY -.-> SEARCH
    SEARCH --> RESULTS
    
    style OAI fill:#10a37f,color:#fff
    style OLL fill:#ff6b6b,color:#fff
    style HF fill:#ffcc00,color:#000
    style VS fill:#6c5ce7,color:#fff
```

---

## 📊 Provider Comparison

### Feature Matrix

| Feature | OpenAI | Ollama | HuggingFace |
|---------|:------:|:------:|:-----------:|
| **Cost** | 💰 Paid | 🆓 Free | 🆓 Free |
| **Privacy** | ☁️ Cloud | 🏠 Local | 🏠 Local |
| **Quality** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Speed** | ⚡⚡⚡⚡ | ⚡⚡ | ⚡⚡⚡ |
| **Offline** | ❌ | ✅ | ✅ |
| **Setup** | API Key | Install App | pip install |

### Embedding Dimensions

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'pie1': '#10a37f', 'pie2': '#ff6b6b', 'pie3': '#ffcc00', 'pie4': '#6c5ce7'}}}%%
pie showData
    title Embedding Dimensions by Model
    "OpenAI (3072)" : 3072
    "Ollama mxbai (1024)" : 1024
    "HuggingFace mpnet (768)" : 768
    "HuggingFace MiniLM (384)" : 384
```

---

## 🔧 Quick Start Guide

### 1️⃣ OpenAI Embeddings (Best Quality)

```python
from langchain_openai import OpenAIEmbeddings

# Initialize
embeddings = OpenAIEmbeddings(model="text-embedding-3-large")

# Embed text
vector = embeddings.embed_query("Your text here")
```

### 2️⃣ Ollama Embeddings (Free & Local)

```python
from langchain_community.embeddings import OllamaEmbeddings

# Initialize (requires Ollama installed)
embeddings = OllamaEmbeddings(model="mxbai-embed-large")

# Embed text
vector = embeddings.embed_query("Your text here")
```

### 3️⃣ HuggingFace Embeddings (Open Source)

```python
from langchain_huggingface import HuggingFaceEmbeddings

# Initialize
embeddings = HuggingFaceEmbeddings(model_name="all-MiniLM-L6-v2")

# Embed text
vector = embeddings.embed_query("Your text here")
```

---

## 🔄 The RAG Pipeline

```mermaid
sequenceDiagram
    participant U as 👤 User
    participant E as 🧠 Embeddings
    participant V as 🗃️ Vector Store
    participant L as 🤖 LLM
    
    Note over U,L: 📥 Indexing Phase
    U->>E: Load Documents
    E->>E: Split into Chunks
    E->>E: Generate Embeddings
    E->>V: Store Vectors
    
    Note over U,L: 🔍 Query Phase
    U->>E: Ask Question
    E->>E: Embed Query
    E->>V: Similarity Search
    V->>E: Return Top K Results
    E->>L: Query + Context
    L->>U: Generated Answer
```

---

## 📈 Learning Path

```mermaid
graph LR
    A[📚 Start Here] --> B[4.1 OpenAI<br/>Learn Basics]
    B --> C[4.2 Ollama<br/>Local Models]
    C --> D[4.3 HuggingFace<br/>Open Source]
    D --> E[🎯 Build RAG App]
    
    style A fill:#e74c3c,color:#fff
    style B fill:#10a37f,color:#fff
    style C fill:#ff6b6b,color:#fff
    style D fill:#ffcc00,color:#000
    style E fill:#0984e3,color:#fff
```

### Recommended Order:

1. **[4.1-embedding.ipynb](4.1-embedding.ipynb)** - Start here to understand embedding fundamentals with OpenAI
2. **[4.2-ollamaemnedding.ipynb](4.2-ollamaemnedding.ipynb)** - Learn about local, free alternatives
3. **[4.3-huggingface.ipynb](4.3-huggingface.ipynb)** - Explore the open-source ecosystem

---

## 🎓 Key Concepts

### Embedding Methods

| Method | Purpose | Example Use |
|--------|---------|-------------|
| `embed_query()` | Single text | Search queries |
| `embed_documents()` | Multiple texts | Document indexing |

### Text Splitting Parameters

| Parameter | Description | Typical Value |
|-----------|-------------|---------------|
| `chunk_size` | Max characters per chunk | 500-1000 |
| `chunk_overlap` | Shared chars between chunks | 50-100 |

### Vector Store Operations

| Operation | Description |
|-----------|-------------|
| `from_documents()` | Create store from docs |
| `similarity_search()` | Find similar chunks |
| `add_documents()` | Add new documents |

---

## 📦 Dependencies

```bash
# Core
pip install langchain langchain-community

# OpenAI
pip install langchain-openai

# HuggingFace
pip install langchain-huggingface sentence-transformers

# Vector Store
pip install chromadb

# Environment
pip install python-dotenv
```

---

## 🔗 Additional Resources

- 📖 [LangChain Documentation](https://python.langchain.com/docs/modules/data_connection/text_embedding/)
- 🧠 [OpenAI Embeddings Guide](https://platform.openai.com/docs/guides/embeddings)
- 🦙 [Ollama Embedding Models](https://ollama.com/blog/embedding-models)
- 🤗 [HuggingFace Sentence Transformers](https://huggingface.co/sentence-transformers)
- 📊 [MTEB Leaderboard](https://huggingface.co/spaces/mteb/leaderboard)

---

## ✅ Summary Checklist

After completing this module, you should be able to:

- [ ] Explain what embeddings are and why they're useful
- [ ] Use OpenAI embeddings with LangChain
- [ ] Set up local embeddings with Ollama
- [ ] Use HuggingFace sentence transformers
- [ ] Load and split documents for embedding
- [ ] Store embeddings in ChromaDB
- [ ] Perform similarity search on embedded documents
- [ ] Choose the right embedding provider for your use case

---

<div align="center">

**Happy Learning! 🚀**

*Created as part of the LangChain Learning Series*

</div>
