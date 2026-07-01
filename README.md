
# Adaptive Corrective RAG with Knowledge Refinement

An **Adaptive Retrieval-Augmented Generation (RAG)** system inspired by the **Corrective Retrieval-Augmented Generation (CRAG)** paper. This project implements a retrieval quality evaluation pipeline that dynamically decides whether to rely on internal knowledge, augment it with web search, or replace it entirely based on the relevance of retrieved documents.

Unlike traditional RAG pipelines that always trust retrieved documents, this system evaluates retrieval quality before generation, performs knowledge refinement at the sentence level, and integrates external web knowledge when retrieval quality is insufficient.

---

## Features

* Adaptive retrieval quality evaluation using an LLM-based retrieval evaluator
* Three-stage routing:

  * **Correct** → Use internal knowledge only
  * **Incorrect** → Replace with web knowledge
  * **Ambiguous** → Combine internal and external knowledge
* Knowledge refinement through sentence decomposition and LLM-based relevance filtering
* Automatic query rewriting for optimized web search
* Hybrid knowledge generation using both vector database retrieval and live web search
* Workflow orchestration using LangGraph
* Structured outputs with Pydantic for reliable routing decisions

---

## Architecture

```
                     User Question
                           │
                           ▼
                  Vector Database Retrieval
                           │
                           ▼
              Retrieval Quality Evaluation
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
     Correct          Ambiguous         Incorrect
        │                  │                  │
        │          Rewrite Query        Rewrite Query
        │                  │                  │
        │             Web Search        Web Search
        │                  │                  │
        └──────────────┬───┴──────────────────┘
                       ▼
             Knowledge Refinement
        (Sentence Decomposition + Filtering)
                       │
                       ▼
              Final Answer Generation
```

---

## Workflow

### 1. Retrieve Documents

Relevant documents are retrieved from a FAISS vector database using OpenAI embeddings.

### 2. Evaluate Retrieval Quality

Each retrieved chunk is scored for relevance using an LLM.

Based on the scores, retrieval is classified into:

* **Correct** → High-confidence retrieval
* **Incorrect** → Retrieval failed
* **Ambiguous** → Partial relevance

### 3. Adaptive Routing

The workflow dynamically chooses one of three execution paths.

**Correct**

* Uses only internal documents.

**Incorrect**

* Rewrites the query.
* Performs web search.
* Uses only external knowledge.

**Ambiguous**

* Uses both retrieved documents and web search results.

### 4. Knowledge Refinement

The collected context is:

* decomposed into individual sentences,
* filtered using an LLM relevance judge,
* recomposed into a refined context containing only useful information.

### 5. Response Generation

The final answer is generated strictly from the refined context to minimize hallucinations.

---

## Tech Stack

* Python
* LangGraph
* LangChain
* OpenAI GPT-4o-mini
* OpenAI Embeddings
* FAISS
* Tavily Search API
* Pydantic
* RecursiveCharacterTextSplitter

---

## Project Structure

```
.
├── documents/
│   ├── book1.pdf
│   ├── book2.pdf
│   └── book3.pdf
├── corrective_rag.ipynb
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Installation

```bash
git clone https://github.com/rahul-x-saini/Corrective-RAG.git

cd Corrective-RAG

python -m venv venv

venv\Scripts\activate

pip install -r requirements.txt
```

Create a `.env` file:

```env
OPENAI_API_KEY=your_openai_key
TAVILY_API_KEY=your_tavily_key
```

---

## Example Query

```
Question:
Batch normalization vs layer normalization
```

The system automatically:

* retrieves relevant documents,
* evaluates retrieval quality,
* decides whether web search is required,
* refines the collected knowledge,
* generates the final answer.

---

## Key Learning Outcomes

This project helped me understand:

* Adaptive Retrieval-Augmented Generation
* Retrieval quality evaluation
* Dynamic workflow orchestration with LangGraph
* Hybrid internal + external knowledge retrieval
* Knowledge refinement strategies
* Query rewriting for web search
* Structured LLM outputs using Pydantic
* Building modular, graph-based AI pipelines

---

## Inspiration

This project is inspired by the paper:

**Corrective Retrieval-Augmented Generation (CRAG)**

The implementation is intended for learning and experimentation. It follows the core architectural ideas of CRAG—retrieval evaluation, adaptive routing, knowledge refinement, and web-augmented retrieval—while using modern tools such as LangGraph, GPT-4o-mini, FAISS, and Tavily Search for implementation.

---

## Future Improvements

* Replace the LLM retrieval evaluator with a fine-tuned lightweight retrieval evaluator
* Support multiple vector databases
* Add citation-aware answer generation
* Introduce reranking before retrieval evaluation
* Add streaming responses
* Deploy as a FastAPI service
* Build a web interface using React or Streamlit
