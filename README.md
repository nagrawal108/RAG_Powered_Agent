# RAG-Powered FAQ Agent with Custom Knowledge

## Overview
This demo builds a **Retrieval-Augmented Generation (RAG)** system that answers questions based on custom FAQ documents using Azure OpenAI and LangChain. Instead of relying solely on the LLM's training data, the agent retrieves relevant information from your own documents before generating answers.

## What is RAG?
RAG (Retrieval-Augmented Generation) combines:
- **Retrieval**: Finding relevant information from a knowledge base
- **Generation**: Using an LLM to generate answers based on the retrieved context

This approach ensures answers are grounded in your specific documents, reducing hallucinations and providing source attribution.

## Prerequisites
- Python 3.8+
- Azure OpenAI account with API access
- An FAQ document (text file named `faq.txt`)

## Required Packages
The following packages are installed in Step 1:
- `langchain` - Framework for LLM applications
- `langchain-openai` - Azure OpenAI integration
- `langchain-community` - Community integrations (loaders, vectorstores)
- `langchain-classic` - Classic chains like RetrievalQA
- `faiss-cpu` - Vector similarity search library
- `tiktoken` - OpenAI's tokenizer

## Setup Instructions

### 1. Create Your FAQ Document
Create a file named `faq.txt` in the same directory with your FAQ content. Example:
```
Q: What is our return policy?
A: We offer a 30-day return policy for all products...

Q: How long does shipping take?
A: Standard shipping takes 5-7 business days...
```

### 2. Configure Azure OpenAI Credentials
You'll need:
- **Azure OpenAI API Key** (set in Step 3)
- **Azure Endpoint** (used in Steps 5 and 6)
- **Deployment names** for embeddings and chat models

Update the API key in cell 7 (Step 3) with your own credentials.

## How It Works

### Step-by-Step Breakdown

**Step 1-2: Install and Import**
- Installs required packages
- Imports LangChain components for document loading, text splitting, embeddings, and QA chains

**Step 3: Authentication**
- Sets the Azure OpenAI API key as an environment variable

**Step 4: Document Loading & Chunking**
- Loads the `faq.txt` file using `TextLoader`
- Splits documents into chunks (500 characters with 50-character overlap)
- Chunking ensures each piece is small enough to embed and retrieve efficiently

**Step 5: Create Vector Store**
- Uses Azure's `text-embedding-ada-002` model to create embeddings
- Stores embeddings in a FAISS vector database for fast similarity search
- FAISS enables quick retrieval of relevant document chunks

**Step 6: Initialize LLM**
- Configures Azure ChatOpenAI with the `gpt-5-mini` deployment
- This model generates the final answer based on retrieved context

**Step 7: Build RAG Chain**
- Creates a `RetrievalQA` chain that:
  1. Takes a user query
  2. Retrieves relevant document chunks from FAISS
  3. Passes chunks and query to the LLM
  4. Returns an answer with source documents

**Step 8-9: Query and Display Results**
- Sends a question to the RAG chain
- Displays the generated answer and the source chunks used

## Usage

1. **Run all cells in order** (cells 1-9)
2. **Modify the query** in Step 8 to ask different questions:
   ```python
   query = "What is our return policy?"  # Change this
   ```
3. **View the answer** and source documents in Step 9

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
- Model: `text-embedding-ada-002`
- API version: `2023-05-15`

### LLM
- Model: `gpt-5-mini`
- API version: `2025-01-01-preview`

### Retrieval
- `return_source_documents=True`: Returns the chunks used to generate the answer

## Customization Tips

1. **Change the FAQ file**: Replace `faq.txt` with any text document
2. **Adjust chunk size**: Smaller chunks = more precise, larger chunks = more context
3. **Change the model**: Update `deployment_name` in Step 6 to use different models
4. **Add more documents**: Load multiple files and combine them before splitting
5. **Tune the retriever**: Add parameters like `search_kwargs={"k": 3}` to control how many chunks are retrieved

## Troubleshooting

- **File not found**: Ensure `faq.txt` exists in the same directory
- **API errors**: Verify your Azure OpenAI credentials and deployment names
- **Import errors**: Reinstall packages using the command in Step 1
- **Empty results**: Check that your FAQ document has content and is properly formatted

## Additional Resources
- [LangChain Documentation](https://python.langchain.com/)
- [Azure OpenAI Service](https://azure.microsoft.com/en-us/products/cognitive-services/openai-service)
- [FAISS Documentation](https://github.com/facebookresearch/faiss)

## Next Steps
- Add a loop to ask multiple questions
- Implement a chat interface
- Experiment with different chunking strategies
- Add metadata filtering to retrieval
- Deploy as a web service or API
