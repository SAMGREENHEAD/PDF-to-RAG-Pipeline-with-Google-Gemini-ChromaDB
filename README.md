# PDF-to-RAG-Pipeline-with-Google-Gemini-ChromaDB


Turn your PDFs into a **searchable knowledge base** and perform **question answering** with Google’s Gemini model using a **Retrieval-Augmented Generation (RAG)** workflow.

---

## Features

-  **PDF Text Extraction** – Cleanly extract text using `pdfplumber`
-  **Semantic Search** – Chunk text and store in ChromaDB for fast retrieval
-  **Embeddings** – Generate vector representations using Google’s `text-embedding-004`
-  **Question Answering** – Ask natural language questions and get grounded answers from `gemini-pro`
-  **End-to-End Notebook** – Ready to run on Kaggle or locally

---

##  Installation

Clone the repo or copy the notebook, then install dependencies:

```bash
pip install google-genai==1.7.0 chromadb==0.6.3 pdfplumber PyPDF2 ftfy
````

---

## Setup

### 1. Get a Google API Key

Generate your API key from [Google AI Studio](https://aistudio.google.com/app/apikey).

### 2. Provide API Key Securely

When running the notebook, you will be prompted:

```python
Enter your Google API Key:
```

The key is stored securely as an environment variable for the session.

---

## Usage

### 1. Extract & Chunk PDF

```python
pdf_path = "/kaggle/input/sample.pdf"
pdf_text = extract_text_from_pdf(pdf_path)

chunk_size = 500
chunks = [pdf_text[i:i+chunk_size] for i in range(0, len(pdf_text), chunk_size)]

for i, chunk in enumerate(chunks):
    collection.add(documents=[chunk], ids=[f"chunk_{i}"])
```

### 2. Generate Embeddings

```python
for i, chunk in enumerate(chunks):
    embedding = genai.embed_content(
        model="models/text-embedding-004",
        content=chunk
    )["embedding"]

    collection.add(
        documents=[chunk],
        ids=[f"chunk_{i}_embedded"],
        embeddings=[embedding]
    )
```

### 3. Ask Questions

```python
answer = query_rag("What is mentioned in the introduction?")
print(answer)
```

---



##  How It Works

1. **Ingest PDFs** → Extract text → Clean and chunk
2. **Store Chunks** → Add to ChromaDB
3. **Embed** → Generate embeddings with Google’s `text-embedding-004`
4. **Retrieve** → Query for top-N relevant chunks
5. **Generate** → Use Gemini to answer questions based on context

---

## Example Output

```text
Q: What is mentioned in the introduction?
A: The introduction provides background on the topic, highlights the main objectives,
and sets the context for the rest of the document.
```

---

## Customization

* **Chunk Size** – Tune `chunk_size` for better recall or precision.
* **Model Choice** – Replace `gemini-pro` with any supported Gemini model.
* **Persistence** – Enable Chroma persistence to reuse collections between sessions.
* **Multi-Document Support** – Loop through multiple PDFs to build a knowledge base.

---

## Best Practices

*  **Secure API Keys** – Never commit API keys to GitHub.
* **Clean Text** – Use `ftfy` or custom preprocessing for consistent results.
* **Prompt Tuning** – Experiment with prompt templates to improve model output.
* **Manage Costs** – Batch embedding requests to minimize API usage.

```
