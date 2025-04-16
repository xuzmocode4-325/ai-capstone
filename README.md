# README for MirascopeBot Code

## **Overview**

This repository contains the code for a conversational AI chatbot designed to answer questions about any provided files or documents. The bot combines Retrieval Augmented Generation (RAG) principles with Google's Gemini language models and LlamaIndex to provide highly contextualized responses based on user queries.

The chatbot retrieves relevant documents, ranks them based on relevance, and uses Google's Gemini model to generate responses. It is ideal for applications requiring domain-specific knowledge retrieval and conversational interfaces.

---

## **Features**

1. **Document Retrieval**: Retrieves relevant Mirascope documentation based on semantic similarity.
2. **Relevance Ranking**: Reranks retrieved documents using an LLM-based postprocessor.
3. **Conversational Interface**: Provides an interactive chatbot experience for users.
4. **Custom Parsing**: Parses LLM-generated responses to extract relevance scores and choice numbers.
5. **Integration with Google Gemini**: Uses advanced language models for response generation.
6. **Persistence**: Saves and loads indices to avoid reprocessing documents in future sessions.

---

## **Requirements**

### **Libraries**
- Python 3.x
- `llama-index`
- `mirascope`
- `pydantic`

### **API Keys**
- A valid Google API key is required for accessing Google Gemini models. The key must be securely stored and retrieved using tools like Kaggle Secrets or environment variables.

---

## **Installation**

1. Clone the repository:
   ```bash
   git clone 
   cd 
   ```

2. Install dependencies:
   ```bash
   pip install llama-index mirascope pydantic
   ```

3. Set up your Google API key:
   - For Kaggle:
     Use `UserSecretsClient` to securely retrieve the API key.
   - Locally:
     Store the API key in an environment variable or a secure file.

---

## **Code Breakdown**

### **Custom Parsing Function**
The `custom_parse_choice_select_answer_fn` function parses LLM-generated answers to extract choice numbers and relevance scores.

#### Key Steps:
1. Splits the raw answer into lines.
2. Extracts numerical values using regular expressions.
3. Filters out invalid lines based on format and constraints.

#### Example Input:
```plaintext
"answer_num: 1, answer_relevance: 0.85\nanswer_num: 2, answer_relevance: 0.75"
```

#### Output:
```python
([1, 2], [0.85, 0.75])
```

---

### **Document Retrieval**
The `get_documents` function retrieves relevant documentation chunks using LlamaIndex's query engine.

#### Key Components:
- Retrieves top 10 similar chunks (`similarity_top_k=10`).
- Applies LLM-based reranking using `LLMRerank`.
- Summarizes retrieved nodes into a cohesive response (`response_mode="tree_summarize"`).

#### Example Usage:
```python
context = get_documents("What is Mirascope?")
print(context)
```

---

### **MirascopeBot Class**
Encapsulates the chatbot's functionality, including document retrieval, response generation, and user interaction.

#### Methods:
1. `_step`: Defines how prompts are structured and sent to Google's Gemini model.
2. `_get_response`: Combines document retrieval and response generation.
3. `run`: Provides an interactive interface for users.

#### Example Interaction:
```plaintext
(User): What is Mirascope?
(Assistant): Mirascope is a tool designed for...
(User): exit
```

---

## **How It Works**

### Workflow:
1. User enters a query.
2. The bot retrieves relevant documents using semantic search and reranking.
3. Documents are passed as context to Google's Gemini model along with the user's question.
4. The model generates a response based on both its general knowledge and the provided context.

### Example Code Flow:
```python
question = "Explain Mirascope's features."
context = get_documents(question)
response = MirascopeBot()._step(context, question)
print(response.content)
```

---

## **Customization**

### Adjusting Retrieval Parameters
Modify `similarity_top_k` or `choice_batch_size` in the query engine configuration to change how many documents are retrieved or reranked.

### Adding Metadata Filters
Integrate metadata filtering for more targeted retrieval:
```python
filtered_query_engine = loaded_index.as_query_engine(
    filters={"category": "documentation"}
)
```

---

## **Limitations**

1. Requires a valid Google API key for functionality.
2. Designed specifically for Mirascope documentation; may require adjustments for other domains.
3. Limited scalability with the default vector store (`SimpleVectorStore`). Consider using external vector databases like Pinecone or Chroma for larger datasets.

---

## **Future Enhancements**

1. Support for multilingual queries and responses.
2. Integration with external vector databases for scalability.
3. Improved error handling and user feedback mechanisms.

---

## **FAQs**

### How do I set up my Google API key?
Store your API key securely using tools like Kaggle Secrets or environment variables, depending on your environment.

### Can I use this bot with other types of documentation?
Yes! Replace the document directory path in `SimpleDirectoryReader` with your own dataset path.

### What happens if no relevant documents are found?
The bot will return "No documents found." This can be customized in the `get_documents` function.

### How scalable is this system?
The default vector store works well for small to medium datasets; larger datasets should use external vector databases like Pinecone or Chroma.

### Can I modify the bot's prompt template?
Absolutely! Update the `@prompt_template` decorator in `_step()` to customize system instructions or user prompts.
