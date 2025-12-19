# 📚 Data Transformation in LangChain - Text Splitters Guide

> **Module 3.3**: Learn how to transform and split documents for optimal LLM processing

---

## 🎯 What You'll Learn

This module covers **Text Splitters** - essential tools for breaking down large documents into smaller, manageable chunks that LLMs can process effectively.

---

## 📊 The Big Picture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        LangChain Data Pipeline                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   📥 DATA SOURCES          🔪 TEXT SPLITTERS         💾 STORAGE             │
│                                                                              │
│   ┌──────────────┐        ┌──────────────────┐      ┌──────────────┐        │
│   │   PDF Files  │───────▶│                  │      │              │        │
│   └──────────────┘        │                  │      │   Vector     │        │
│   ┌──────────────┐        │   Split into     │      │   Store      │        │
│   │  Text Files  │───────▶│   Chunks         │─────▶│              │        │
│   └──────────────┘        │                  │      │  (Chroma,    │        │
│   ┌──────────────┐        │   + Overlap      │      │   FAISS,     │        │
│   │  Web Pages   │───────▶│                  │      │   Pinecone)  │        │
│   └──────────────┘        └──────────────────┘      └──────────────┘        │
│   ┌──────────────┐                                                          │
│   │  JSON/APIs   │                                                          │
│   └──────────────┘                                                          │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 🤔 Why Do We Need Text Splitters?

### The Problem:
- LLMs have **context window limits** (e.g., 4K, 8K, 128K tokens)
- Large documents exceed these limits
- Processing entire documents is **expensive** and **slow**

### The Solution:
- Split documents into **smaller chunks**
- Add **overlap** to preserve context
- Store chunks for **efficient retrieval**

```
┌─────────────────────────────────────────────────────────────────┐
│                    Without Splitting                             │
│                                                                  │
│   ┌──────────────────────────────────────────────────────────┐  │
│   │            LARGE DOCUMENT (50,000 tokens)                │  │
│   │                      ❌ TOO BIG!                         │  │
│   └──────────────────────────────────────────────────────────┘  │
│                              ↓                                   │
│                         LLM FAILS                                │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                     With Splitting                               │
│                                                                  │
│   ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐       │
│   │Chunk 1 │ │Chunk 2 │ │Chunk 3 │ │Chunk 4 │ │Chunk 5 │       │
│   │ 500    │ │ 500    │ │ 500    │ │ 500    │ │ 500    │       │
│   │tokens  │ │tokens  │ │tokens  │ │tokens  │ │tokens  │       │
│   └────────┘ └────────┘ └────────┘ └────────┘ └────────┘       │
│       ↓          ↓          ↓          ↓          ↓             │
│                    ✅ LLM CAN PROCESS!                          │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📋 Available Text Splitters

| Notebook | Splitter | Best For | Key Feature |
|----------|----------|----------|-------------|
| [3.3](3.3-RecuriveCharactertextsplitter.ipynb) | RecursiveCharacterTextSplitter | 🌟 General text | Tries multiple separators |
| [3.4](3.4-CharacterTextsplitter.ipynb) | CharacterTextSplitter | Simple splits | Single separator |
| [3.5](3.5-HTMLtextsplitter.ipynb) | HTMLHeaderTextSplitter | Web pages | Preserves HTML structure |
| [3.6](3.6-RecursiveJsonSplitter.ipynb) | RecursiveJsonSplitter | JSON/API data | Maintains JSON validity |

---

## 🔪 Splitter Comparison

```
┌───────────────────────────────────────────────────────────────────────────┐
│                    CHOOSING THE RIGHT SPLITTER                             │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│   What type of data do you have?                                          │
│                                                                            │
│   ┌─────────────┐                                                         │
│   │  Plain Text │──────▶ RecursiveCharacterTextSplitter ⭐ (Recommended)  │
│   │  (.txt)     │        or CharacterTextSplitter (simple)                │
│   └─────────────┘                                                         │
│                                                                            │
│   ┌─────────────┐                                                         │
│   │    HTML     │──────▶ HTMLHeaderTextSplitter                           │
│   │  Web Pages  │        (Preserves header hierarchy)                     │
│   └─────────────┘                                                         │
│                                                                            │
│   ┌─────────────┐                                                         │
│   │    JSON     │──────▶ RecursiveJsonSplitter                            │
│   │  API Data   │        (Maintains JSON structure)                       │
│   └─────────────┘                                                         │
│                                                                            │
│   ┌─────────────┐                                                         │
│   │    PDF      │──────▶ Load with PyPDFLoader → then                     │
│   │  Documents  │        RecursiveCharacterTextSplitter                   │
│   └─────────────┘                                                         │
│                                                                            │
└───────────────────────────────────────────────────────────────────────────┘
```

---

## 🔄 Understanding Chunk Overlap

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         CHUNK OVERLAP EXPLAINED                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│   Without Overlap (BAD ❌):                                              │
│   ┌────────────────────┐┌────────────────────┐                          │
│   │      Chunk 1       ││      Chunk 2       │                          │
│   │ "The quick brown"  ││ "fox jumps over"   │                          │
│   └────────────────────┘└────────────────────┘                          │
│                         ↑                                                │
│                    Context LOST!                                         │
│                                                                          │
│   With Overlap (GOOD ✅):                                                │
│   ┌──────────────────────────┐                                          │
│   │        Chunk 1           │                                          │
│   │ "The quick brown fox"    │                                          │
│   └──────────────────────────┘                                          │
│                    ┌──────────────────────────┐                         │
│                    │        Chunk 2           │                         │
│                    │ "brown fox jumps over"   │                         │
│                    └──────────────────────────┘                         │
│                    ↑─────────────↑                                       │
│                      OVERLAP                                             │
│                   (Context preserved!)                                   │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 📖 Detailed Notebooks

### 1️⃣ [RecursiveCharacterTextSplitter](3.3-RecuriveCharactertextsplitter.ipynb) ⭐

**The #1 recommended splitter for general text.**

```
Separator hierarchy:
"\n\n" (paragraphs) → "\n" (lines) → " " (words) → "" (characters)
```

**When to use:**
- ✅ General purpose text splitting
- ✅ Unknown or varied text structure
- ✅ When semantic meaning matters

---

### 2️⃣ [CharacterTextSplitter](3.4-CharacterTextsplitter.ipynb)

**Simple splitting on a single character.**

```python
CharacterTextSplitter(separator="\n\n")  # Split on paragraphs only
```

**When to use:**
- ✅ Text with known, consistent structure
- ✅ When you want control over exact split points
- ✅ Simpler use cases

---

### 3️⃣ [HTMLHeaderTextSplitter](3.5-HTMLtextsplitter.ipynb)

**Structure-aware splitting for HTML content.**

```
HTML Document:
├── <h1>Main Topic</h1>
│   └── <h2>Subtopic</h2>
│       └── <h3>Detail</h3>
              ↓
Chunk metadata: {"Header 1": "Main Topic", "Header 2": "Subtopic", "Header 3": "Detail"}
```

**When to use:**
- ✅ Web pages and documentation
- ✅ When you need to preserve header hierarchy
- ✅ For better retrieval with context

---

### 4️⃣ [RecursiveJsonSplitter](3.6-RecursiveJsonSplitter.ipynb)

**Intelligent splitting for JSON data.**

```
Large JSON → Split into valid JSON chunks
             (maintains structure)
```

**When to use:**
- ✅ API responses
- ✅ Configuration files
- ✅ Any structured JSON data

---

## 🎓 Key Parameters to Know

| Parameter | Description | Typical Values |
|-----------|-------------|----------------|
| `chunk_size` | Maximum characters per chunk | 500 - 2000 |
| `chunk_overlap` | Shared characters between chunks | 10-20% of chunk_size |
| `separator` | Character(s) to split on | `"\n\n"`, `"\n"`, `" "` |
| `separators` | List of separators (recursive) | `["\n\n", "\n", " ", ""]` |

---

## 🔗 Common Pipeline

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    TYPICAL RAG PIPELINE                                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│   1. LOAD              2. SPLIT              3. EMBED           4. STORE │
│                                                                          │
│   ┌─────────┐         ┌─────────┐         ┌─────────┐        ┌────────┐ │
│   │ Document│         │  Text   │         │Embedding│        │ Vector │ │
│   │ Loader  │────────▶│Splitter │────────▶│  Model  │───────▶│ Store  │ │
│   └─────────┘         └─────────┘         └─────────┘        └────────┘ │
│                                                                          │
│   PyPDFLoader         Recursive            OpenAI             Chroma    │
│   TextLoader          Character            HuggingFace        FAISS     │
│   WebBaseLoader       TextSplitter         Cohere             Pinecone  │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 💡 Best Practices

1. **Start with RecursiveCharacterTextSplitter** - It works well for most cases

2. **Choose chunk_size based on your LLM** - Consider token limits

3. **Use 10-20% overlap** - Balances context preservation vs redundancy

4. **Match splitter to data type** - HTML → HTMLHeaderTextSplitter, JSON → RecursiveJsonSplitter

5. **Test and iterate** - Different chunk sizes affect retrieval quality

---

## 📚 Quick Reference

```python
# General text
from langchain_text_splitters import RecursiveCharacterTextSplitter
splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)

# Simple splitting
from langchain_text_splitters import CharacterTextSplitter
splitter = CharacterTextSplitter(separator="\n\n", chunk_size=500)

# HTML content
from langchain_text_splitters import HTMLHeaderTextSplitter
splitter = HTMLHeaderTextSplitter(headers_to_split_on=[("h1", "Header 1"), ("h2", "Header 2")])

# JSON data
from langchain_text_splitters import RecursiveJsonSplitter
splitter = RecursiveJsonSplitter(max_chunk_size=300)
```

---

## 🚀 Next Steps

After mastering text splitters, continue with:
- **Embeddings** - Convert text to vectors
- **Vector Stores** - Store and retrieve documents
- **Retrieval** - Find relevant chunks for your queries

---

## 📖 Resources

- [LangChain Text Splitters Documentation](https://python.langchain.com/docs/how_to/#text-splitters)
- [Chunking Strategies Guide](https://www.pinecone.io/learn/chunking-strategies/)

---

*Happy Learning! 🎉*
