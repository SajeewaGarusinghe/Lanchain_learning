# 🗄️ Vector Stores in LangChain

## 📚 Overview

Welcome to the **Vector Stores** module! This section covers how to store and retrieve document embeddings efficiently using vector databases - a crucial component for building semantic search and RAG (Retrieval Augmented Generation) applications.

> 💡 **From the Instructor**: *"Two of the most common things that we use which are completely open source are FAISS and Chroma. As we go ahead, when we develop end-to-end projects, we'll be seeing new DBs like Cassandra DB, Astra DB, and Pinecone - which can be hosted in the cloud."*

---

## 🎯 What are Vector Stores?

Vector stores are specialized databases designed to store and search **high-dimensional vectors** (embeddings). They enable **semantic search** - finding documents based on meaning rather than exact keyword matches.

### 🧠 The Big Picture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         VECTOR STORE PIPELINE                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  📄 INDEXING PHASE                                                          │
│  ════════════════                                                           │
│                                                                              │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────────────┐      │
│  │Documents │───▶│  Text    │───▶│Embedding │───▶│   Vector Store   │      │
│  │  (.txt,  │    │ Splitter │    │  Model   │    │  (FAISS/Chroma)  │      │
│  │  .pdf)   │    │          │    │          │    │                  │      │
│  └──────────┘    └──────────┘    └──────────┘    └──────────────────┘      │
│       │               │               │                   │                 │
│       ▼               ▼               ▼                   ▼                 │
│   Raw Text      Chunks of       Numerical           Indexed for            │
│                  Text           Vectors             Fast Search            │
│                                                                              │
│  ════════════════════════════════════════════════════════════════════════   │
│                                                                              │
│  🔍 RETRIEVAL PHASE                                                         │
│  ══════════════════                                                         │
│                                                                              │
│  ┌──────────┐    ┌──────────┐    ┌──────────────────┐    ┌──────────┐      │
│  │  User    │───▶│ Embedding│───▶│  Similarity      │───▶│ Relevant │      │
│  │  Query   │    │  Model   │    │  Search          │    │ Documents│      │
│  └──────────┘    └──────────┘    └──────────────────┘    └──────────┘      │
│       │               │                   │                   │             │
│       ▼               ▼                   ▼                   ▼             │
│   "What is..."   Query Vector     Compare with all      Top K matches      │
│                                   stored vectors                            │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 📂 Notebooks in This Module

| # | Notebook | Description | Key Concepts |
|---|----------|-------------|--------------|
| 1 | [5.1-Faiss.ipynb](5.1-Faiss.ipynb) | 🚀 Facebook AI Similarity Search | Fast similarity search, L2 distance |
| 2 | [5.2-Chroma.ipynb](5.2-Chroma.ipynb) | 🎨 AI-native vector database | Built-in persistence, metadata filtering |

### 📹 Lecture Transcripts
| File | Description |
|------|-------------|
| [Faiss_lecture.txt](Faiss_lecture.txt) | 📝 Complete FAISS tutorial walkthrough |
| [chromadb_lecture.txt](chromadb_lecture.txt) | 📝 Complete Chroma DB tutorial walkthrough |

---

## 🔧 Installation Guide

Before starting, make sure to install the required packages:

```bash
# For FAISS (CPU version - use faiss-gpu for cloud/GPU)
pip install faiss-cpu

# For Chroma (new LangChain integration)
pip install langchain-chroma

# Common dependencies
pip install langchain langchain-community
```

> ⚠️ **Important Note**: In the previous version of Chroma, you would install `chromadb` separately. Now, LangChain has a dedicated library `langchain-chroma` that you should use instead. No need to install chromadb separately!

---

## 🔑 Key Concepts

### 1️⃣ Embeddings

**What**: Numerical representations of text that capture semantic meaning.

```
"I love programming" ──▶ [0.12, -0.34, 0.56, 0.78, ...]
                              │
                              ▼
                        768-4096 dimensions
                        (depending on model)
```

**Why**: Computers can't understand text directly - embeddings allow mathematical comparison of meanings.

> 💡 **Pro Tip**: You can also pass vectors directly instead of text! Use `embeddings.embed_query(query)` to get the vector, then use `similarity_search_by_vector(embedding_vector)` for more control.

### 2️⃣ Text Splitting

**What**: Breaking large documents into smaller chunks.

```
┌────────────────────────────────────────────┐
│           Large Document                    │
│  (10,000 characters)                        │
└────────────────────────────────────────────┘
                    │
                    ▼ Text Splitter
    ┌───────────────┼───────────────┐
    ▼               ▼               ▼
┌────────┐    ┌────────┐    ┌────────┐
│ Chunk 1│    │ Chunk 2│    │ Chunk 3│
│ (1000) │    │ (1000) │    │ (1000) │
└────────┘    └────────┘    └────────┘
```

**Types**:
| Splitter | Best For |
|----------|----------|
| `CharacterTextSplitter` | Simple, fixed-size splitting |
| `RecursiveCharacterTextSplitter` | Preserves document structure |
| `TokenTextSplitter` | Token-aware splitting |

### 3️⃣ Similarity Search

**What**: Finding the most similar vectors to a query.

```
Query: "What is machine learning?"
         │
         ▼ Embed
    [0.2, 0.5, ...]
         │
         ▼ Compare
    ┌────────────────────────────────────┐
    │  Doc1: [0.2, 0.5, ...] → Score: 0.1│ ✅ Best match
    │  Doc2: [0.3, 0.4, ...] → Score: 0.3│
    │  Doc3: [0.8, 0.1, ...] → Score: 0.9│ ❌ Least similar
    └────────────────────────────────────┘
```

### 4️⃣ Retrievers - The Interface Pattern

**What**: A standardized interface that connects vector stores to LLM models.

> 💡 **From the Instructor**: *"Retrievers are like an interface which, whenever we put any kind of query, is connected to the vector store DB. We need to convert the vector store DB into a retriever class. This allows us to easily work with other LangChain methods which largely work with retrievers."*

```
┌─────────────────────────────────────────────────────────────────┐
│                    WHY USE RETRIEVERS?                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ❌ WITHOUT RETRIEVER:                                          │
│  ┌─────────────┐                    ┌─────────┐                 │
│  │ Vector Store│ ──── ✗ ──────────▶ │   LLM   │                 │
│  └─────────────┘  (Can't connect    └─────────┘                 │
│                    directly!)                                    │
│                                                                  │
│  ✅ WITH RETRIEVER:                                             │
│  ┌─────────────┐    ┌───────────┐    ┌─────────┐               │
│  │ Vector Store│───▶│ Retriever │───▶│   LLM   │               │
│  └─────────────┘    └───────────┘    └─────────┘               │
│        │              (Interface)                                │
│        ▼                   │                                     │
│  db.as_retriever()    retriever.invoke(query)                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Key Point**: When working with different LLM models, you cannot directly use the vector store DB. You must first convert it into a retriever!

---

## 🆚 Vector Store Comparison

### FAISS vs Chroma

```
┌─────────────────────────────────────────────────────────────────┐
│                    VECTOR STORE COMPARISON                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────────────┐       ┌─────────────────────┐          │
│  │       FAISS         │       │       CHROMA        │          │
│  │   (Meta AI)         │       │   (Chroma Inc)      │          │
│  ├─────────────────────┤       ├─────────────────────┤          │
│  │ ⚡ Ultra-fast       │       │ 🎯 Developer-friendly│          │
│  │ 🔢 Billions vectors │       │ 💾 Built-in persist │          │
│  │ 🧮 Multiple indexes │       │ 🏷️ Rich metadata    │          │
│  │ 📈 Production-ready │       │ 🔌 Easy setup       │          │
│  └─────────────────────┘       └─────────────────────┘          │
│                                                                  │
│  BEST FOR:                     BEST FOR:                        │
│  • Large-scale search          • Rapid prototyping              │
│  • Performance-critical apps   • Metadata filtering             │
│  • Massive datasets            • Small-medium datasets          │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Feature Matrix

| Feature | FAISS | Chroma |
|---------|:-----:|:------:|
| **Speed** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Ease of Use** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Persistence** | Manual (`save_local()`) | Built-in (`persist_directory`) |
| **Metadata Filtering** | Limited | Rich |
| **Scalability** | Excellent | Good |
| **GPU Support** | ✅ (`faiss-gpu`) | ❌ |
| **Client-Server** | ❌ | ✅ |
| **Internal Storage** | Binary files (`.faiss`, `.pkl`) | SQLite DB (`.sqlite3`) |
| **License** | MIT | Apache 2.0 |

> 💡 **Storage Details**:
> - **FAISS**: Saves as `index.faiss` (vectors) + `index.pkl` (metadata)
> - **Chroma**: Creates a `chroma.sqlite3` database internally - *"Every vector is stored inside this particular DB. This can be hosted anywhere you like!"*

---

## 🔄 Common Workflow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         RAG (Retrieval Augmented Generation)                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   1. LOAD              2. SPLIT            3. EMBED            4. STORE     │
│   ═══════             ═══════════         ═════════           ═══════════   │
│   ┌──────┐            ┌──────────┐        ┌────────┐          ┌─────────┐   │
│   │ PDF  │            │ Chunked  │        │Vectors │          │ Vector  │   │
│   │ TXT  │───────────▶│  Text    │───────▶│ [...]  │─────────▶│  Store  │   │
│   │ Web  │            │          │        │        │          │         │   │
│   └──────┘            └──────────┘        └────────┘          └─────────┘   │
│                                                                     │        │
│                                                                     ▼        │
│   5. RETRIEVE         6. AUGMENT          7. GENERATE                       │
│   ═══════════         ══════════          ═══════════                       │
│   ┌──────────┐        ┌──────────┐        ┌─────────┐                       │
│   │ Relevant │        │ Query +  │        │  LLM    │                       │
│   │   Docs   │◀──────▶│ Context  │───────▶│Response │                       │
│   │          │        │          │        │         │                       │
│   └──────────┘        └──────────┘        └─────────┘                       │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Quick Start Code

### FAISS Example
```python
from langchain_community.vectorstores import FAISS
from langchain_community.embeddings import OllamaEmbeddings

# Create
db = FAISS.from_documents(docs, OllamaEmbeddings())

# Search
results = db.similarity_search("your query")

# Persist
db.save_local("faiss_index")
```

### Chroma Example
```python
from langchain_chroma import Chroma
from langchain_community.embeddings import OllamaEmbeddings

# Create (with persistence)
db = Chroma.from_documents(
    docs, 
    OllamaEmbeddings(),
    persist_directory="./chroma_db"
)

# Search
results = db.similarity_search("your query")

# Load later
db = Chroma(persist_directory="./chroma_db", embedding_function=OllamaEmbeddings())
```

---

## 📊 Understanding Similarity Scores

```
┌───────────────────────────────────────────────────────────────┐
│                    DISTANCE METRICS                            │
├───────────────────────────────────────────────────────────────┤
│                                                                │
│  L2 (Euclidean) Distance - Used by FAISS & Chroma            │
│  ═══════════════════════════════════════════════              │
│                                                                │
│  Score = 0.0   ──▶  🎯 Perfect match (identical)              │
│  Score < 0.5   ──▶  🟢 Very similar                           │
│  Score < 1.0   ──▶  🟡 Somewhat similar                       │
│  Score > 1.5   ──▶  🔴 Not very similar                       │
│                                                                │
│  ⚠️ LOWER score = BETTER match                                │
│                                                                │
│  ────────────────────────────────────────────────────────     │
│                                                                │
│  Cosine Similarity (alternative metric)                       │
│  ════════════════════════════════════════                     │
│                                                                │
│  Score = 1.0   ──▶  🎯 Perfect match                          │
│  Score > 0.8   ──▶  🟢 Very similar                           │
│  Score > 0.5   ──▶  🟡 Somewhat similar                       │
│  Score < 0.5   ──▶  🔴 Not similar                            │
│                                                                │
│  ⚠️ HIGHER score = BETTER match                               │
│                                                                │
└───────────────────────────────────────────────────────────────┘
```

> 💡 **From the Instructor**: *"The returned distance score is L2 distance, which is also called Manhattan distance. Therefore, a lower score is better. Based on that particular information, whichever is the nearest, that will be getting as the first context from that particular text file."*

### Using Similarity Search with Score

```python
# Get documents WITH their similarity scores
docs_and_scores = db.similarity_search_with_score(query)

for doc, score in docs_and_scores:
    print(f"Score: {score:.4f} - {doc.page_content[:50]}...")
```

---

## 🚀 Best Practices

### 1. Chunk Size Selection
```
┌─────────────────────────────────────────────────────────────┐
│  CHUNK SIZE TRADE-OFFS                                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Small Chunks (100-500 chars)                               │
│  ├─ ✅ More precise retrieval                               │
│  ├─ ✅ Less noise in results                                │
│  └─ ❌ May lose context                                     │
│                                                              │
│  Large Chunks (1000-2000 chars)                             │
│  ├─ ✅ More context preserved                               │
│  ├─ ✅ Fewer chunks to search                               │
│  └─ ❌ May include irrelevant info                          │
│                                                              │
│  💡 Recommendation: Start with 500-1000 and adjust          │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 2. Embedding Model Selection

| Model | Dimensions | Speed | Quality | Use Case |
|-------|-----------|-------|---------|----------|
| Ollama (local) | ~4096 | Medium | Good | Privacy, offline |
| OpenAI ada-002 | 1536 | Fast | Excellent | Production |
| HuggingFace | Varies | Medium | Good | Custom needs |

### 3. When to Use Which?

```
┌─────────────────────────────────────────────────────────────┐
│  DECISION TREE: Choosing a Vector Store                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Need to handle billions of vectors?                        │
│  ├─ YES ──▶ Use FAISS                                       │
│  └─ NO                                                       │
│       │                                                      │
│       ▼                                                      │
│  Need rich metadata filtering?                              │
│  ├─ YES ──▶ Use Chroma                                      │
│  └─ NO                                                       │
│       │                                                      │
│       ▼                                                      │
│  Prototyping or small dataset?                              │
│  ├─ YES ──▶ Use Chroma (easier setup)                       │
│  └─ NO ──▶ Use FAISS (better performance)                   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 📚 Additional Resources

- 📖 [LangChain Vector Stores Documentation](https://python.langchain.com/docs/modules/data_connection/vectorstores/)
- 🔗 [FAISS GitHub](https://github.com/facebookresearch/faiss)
- 🎨 [Chroma Documentation](https://docs.trychroma.com/)
- 📺 [Vector Databases Explained (YouTube)](https://www.youtube.com/watch?v=klTvEwg3oJ4)

---

## 🗺️ Vector Store Ecosystem

Beyond FAISS and Chroma, there are many other vector stores you'll encounter:

```
┌─────────────────────────────────────────────────────────────────┐
│                  VECTOR STORE LANDSCAPE                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  🆓 OPEN SOURCE (Local)          ☁️ CLOUD-HOSTED                │
│  ═══════════════════════         ════════════════               │
│  ┌─────────────────────┐         ┌─────────────────────┐        │
│  │ • FAISS (Meta)      │         │ • Pinecone          │        │
│  │ • Chroma            │         │ • Astra DB          │        │
│  │ • Milvus            │         │   (Cassandra-based) │        │
│  │ • Weaviate          │         │ • Qdrant Cloud      │        │
│  │ • Qdrant            │         │ • Weaviate Cloud    │        │
│  └─────────────────────┘         └─────────────────────┘        │
│                                                                  │
│  💡 We'll explore Astra DB and Pinecone in end-to-end projects! │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## ✅ Learning Checklist

- [ ] Understand what embeddings are and why we need them
- [ ] Learn different text splitting strategies
- [ ] Create a FAISS vector store from documents
- [ ] Create a Chroma vector store with persistence
- [ ] Perform similarity search with scores
- [ ] Convert vector stores to retrievers
- [ ] Save and load vector stores from disk
- [ ] Choose the right vector store for your use case

---

## 🎯 Next Steps

After completing this module, you're ready to:

1. **Build a RAG System** - Combine vector stores with LLMs
2. **Explore Other Vector Stores** - Pinecone, Weaviate, Milvus, Astra DB
3. **Implement Hybrid Search** - Combine keyword + semantic search
4. **Add Metadata Filtering** - Filter results by custom attributes
5. **Deploy to Cloud** - Host your vector store on cloud services

---

## ⚠️ Common Gotchas

| Issue | Solution |
|-------|----------|
| 🐢 Embedding is slow locally | Use GPU (`faiss-gpu`) or cloud embedding services |
| 📦 `chromadb` import error | Use `langchain-chroma` instead of `chromadb` directly |
| 🔄 Can't use vector store with LLM | Convert to retriever first with `db.as_retriever()` |
| 💾 Lost data after restart | Enable persistence: `save_local()` for FAISS, `persist_directory` for Chroma |
| ⚡ Need faster search | Consider FAISS with GPU or use approximate nearest neighbor indexes |

---

## 🧪 Practice Exercises

1. **Basic**: Load a text file, split it, create a FAISS index, and search for a query
2. **Intermediate**: Compare results between FAISS and Chroma on the same dataset
3. **Advanced**: Build a simple Q&A system using a retriever + LLM

---

*Happy Learning! 🚀*
