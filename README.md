# RAG-Powered FAQ Agent with Custom Knowledge

## Overview
This demo builds a **Retrieval-Augmented Generation (RAG)** system that answers questions based on custom FAQ documents using **Groq (Llama 3.3)** and **LangChain**. Instead of relying solely on the LLM's training data, the agent retrieves relevant information from your own documents before generating answers. It includes an interactive Q&A loop so you can ask multiple questions in one session.

## What is RAG?
RAG (Retrieval-Augmented Generation) combines:
- **Retrieval**: Finding relevant information from a knowledge base
- **Generation**: Using an LLM to generate answers based on the retrieved context

This approach ensures answers are grounded in your specific documents, reducing hallucinations and providing source attribution.

## Prerequisites
- Python 3.8+
- A free **Groq API key** from [console.groq.com](https://console.groq.com)
- An FAQ document (text file named `faq.txt`)
- A `.env` file containing `GROQ_API_KEY=your-key-here`

## Required Packages
The following packages are installed in Step 1:
- `langchain` - Framework for LLM applications
- `langchain-openai` - OpenAI integration
- `langchain-community` - Community integrations (loaders, vectorstores, embeddings)
- `langchain-classic` - Classic chains like RetrievalQA
- `langchain-groq` - Groq LLM integration
- `faiss-cpu` - Vector similarity search library
- `fastembed` - Lightweight local embedding library (ONNX-based)
- `tiktoken` - OpenAI's tokenizer
- `python-dotenv` - Load environment variables from `.env` files

## Setup Instructions

### 1. Create Your FAQ Document
Create a file named `faq.txt` in the same directory with your FAQ content. Example:
```
Q: What is our return policy?
A: We offer a 30-day return policy for all products...

Q: How long does shipping take?
A: Standard shipping takes 5-7 business days...
```

### 2. Configure Groq API Credentials
Create a `.env` file in the same directory as the notebook:
```
GROQ_API_KEY=gsk_your_key_here
```
Get a free API key from [console.groq.com](https://console.groq.com).

## How It Works

### Step-by-Step Breakdown

**Step 1-2: Install and Import**
- Installs required packages
- Imports LangChain components for document loading, text splitting, embeddings, and QA chains

**Step 3: Authentication**
- Loads the `GROQ_API_KEY` from the `.env` file using `python-dotenv`
- Verifies the key was loaded successfully

**Step 4: Document Loading & Chunking**
- Loads the `faq.txt` file using `TextLoader`
- Splits documents into chunks (500 characters with 50-character overlap)
- Chunking ensures each piece is small enough to embed and retrieve efficiently

**Step 5: Create Vector Store**
- Uses **FastEmbed** with the `BAAI/bge-small-en-v1.5` model to create embeddings locally (no API needed)
- Stores embeddings in a FAISS vector database for fast similarity search
- FAISS enables quick retrieval of relevant document chunks

**Step 6: Initialize LLM**
- Configures **ChatGroq** with the `llama-3.3-70b-versatile` model (free, fast, open-source)
- `temperature=0` for deterministic, factual answers

**Step 7: Build RAG Chain**
- Creates a `RetrievalQA` chain that:
  1. Takes a user query
  2. Retrieves relevant document chunks from FAISS
  3. Passes chunks and query to the LLM
  4. Returns an answer with source documents

**Step 8-9: Query and Display Results**
- Sends a question to the RAG chain
- Displays the generated answer and the source chunks used

**Step 10: Interactive Q&A Loop**
- Repeatedly prompts the user for questions
- Returns answers with source citations
- Type `quit` or `exit` to stop

## Usage

1. **Run all cells in order** (Steps 1-9)
2. **For a single query**, modify the question in Step 8:
   ```python
   query = "What is our return policy?"  # Change this
   ```
3. **For interactive mode**, run Step 10 and type questions directly in the terminal. Type `quit` or `exit` to stop.

## Example Output
```
Answer: We offer a 30-day return policy for all products. Items must be in original condition...

--- Sources ---

Source 1:
Q: What is our return policy?
A: We offer a 30-day return policy for all products...

Source 2:
Additional return policy details...
```

## Key Configuration Parameters

### Text Splitting
- `chunk_size=500`: Maximum characters per chunk
- `chunk_overlap=50`: Overlap between chunks to maintain context

### Embeddings
- Model: `BAAI/bge-small-en-v1.5` (runs locally via FastEmbed)
- No API key required — uses ONNX runtime

### LLM
- Provider: **Groq** (free tier)
- Model: `llama-3.3-70b-versatile`
- `temperature=0`: Deterministic output

### Retrieval
- `return_source_documents=True`: Returns the chunks used to generate the answer

## Customization Tips

1. **Change the FAQ file**: Replace `faq.txt` with any text document
2. **Adjust chunk size**: Smaller chunks = more precise, larger chunks = more context
3. **Change the model**: Update the `model` parameter in Step 6 (e.g., `llama-3.1-8b-instant` for faster responses)
4. **Add more documents**: Load multiple files and combine them before splitting
5. **Tune the retriever**: Add parameters like `search_kwargs={"k": 3}` to control how many chunks are retrieved

## Troubleshooting

- **File not found**: Ensure `faq.txt` exists in the same directory
- **API errors**: Verify your Groq API key in the `.env` file
- **Import errors**: Reinstall packages using the command in Step 1
- **Empty results**: Check that your FAQ document has content and is properly formatted
- **Rate limits**: Groq's free tier has rate limits — wait a moment and retry

## Additional Resources
- [LangChain Documentation](https://python.langchain.com/)
- [Groq Console](https://console.groq.com)
- [FAISS Documentation](https://github.com/facebookresearch/faiss)
- [FastEmbed Documentation](https://github.com/qdrant/fastembed)
