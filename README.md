# WiseBot: Philosophical Inquiry Agent

## Overview

WiseBot is an AI-powered chatbot designed to provide philosophical and religious insights by leveraging Retrieval-Augmented Generation (RAG) techniques. It semantically searches through various holy texts (Bhagavad Gita, Quran, and Bible) and generates thoughtful responses using a large language model (Google's Gemini API).

This project aims to demonstrate how semantic search and modern LLMs can be combined to create an interactive and knowledgeable agent capable of discussing deep philosophical and religious concepts.

## Features

*   **Semantic Search**: Utilizes `sentence-transformers` and FAISS for efficient and accurate retrieval of relevant verses from a vast corpus of religious texts.
*   **Retrieval-Augmented Generation (RAG)**: Combines retrieved information with the generative capabilities of the Gemini LLM to produce contextually rich and coherent answers.
*   **Multi-Textual Context**: Can query and synthesize information from the Bhagavad Gita, Quran, and Bible.
*   **Interactive Chat Interface**: Provides a user-friendly command-line interface for natural language questions.
*   **Scope Management**: Intelligently identifies and responds to out-of-scope questions to maintain focus on philosophical and religious discourse.
*   **Caching**: Implements a caching mechanism to speed up responses for repeated queries.
*   **Logging**: Detailed logging for debugging and monitoring application behavior.

## Setup and Installation

### 1. Google Drive Configuration

This notebook is designed to run in Google Colab and uses Google Drive for persistent storage of datasets, embeddings, and cache.

*   **Create a `WiseBot` Folder**: In your Google Drive, create a folder named `WiseBot` in your root directory (`MyDrive/`). This folder will be used to store all necessary files.

*   **Upload Datasets**: Place the following CSV files into the `MyDrive/WiseBot/` folder:
    *   `gita.csv`
    *   `quran.csv`
    *   `bible.csv`

    *Note: These datasets should contain a column named 'verse' with the text content you wish to embed and search.*

### 2. Google Colab Notebook Setup

#### A. Mount Google Drive

The first step in the notebook mounts your Google Drive to access the `WiseBot` folder and its contents. Run the following cell:

```python
from google.colab import drive
drive.mount('/content/drive')
```

#### B. Install Dependencies

The project requires `sentence-transformers` for embeddings and `faiss-cpu` for efficient similarity search. Run the following cell to install them:

```python
!pip install sentence_transformers
!pip install faiss-cpu
```

#### C. Configure Google Gemini API Key

WiseBot uses the Google Gemini API to generate responses. You'll need an API key from Google AI Studio.

1.  **Obtain API Key**: Go to [Google AI Studio](https://aistudio.google.com/app/apikey) and create an API key.
2.  **Colab Secrets**: In your Google Colab notebook, open the 'Secrets' tab (usually a key icon 🔑 on the left sidebar).
3.  **Add Secret**: Add a new secret with the name `GOOGLE_API_KEY` and paste your Gemini API key as its value. Ensure "Notebook access" is enabled for this secret.

    *The notebook will then securely fetch this key using `google.colab.userdata.get('GOOGLE_API_KEY')`.*

#### D. Run Initial Setup Cells

Execute all the cells under the sections:

*   `## Pre-embedding and FAISS Index Setup`
*   `## Global Configuration, Logging, and Data Initialization`

These cells will:
*   Load or generate text embeddings and build a FAISS index (this can take some time the first time it runs).
*   Initialize the Sentence Transformer model and the Gemini API model.
*   Set up logging and caching.

## How It Works (High-Level)

1.  **Data Ingestion**: Religious texts are loaded from CSV files.
2.  **Embedding Generation**: Each verse from the texts is converted into a numerical vector (embedding) using a `SentenceTransformer` model (`all-MiniLM-L6-v2`). These embeddings are then stored, along with their metadata, on Google Drive.
3.  **FAISS Indexing**: A FAISS (Facebook AI Similarity Search) index is created from these embeddings, allowing for fast similarity searches.
4.  **User Query**: The user asks a question.
5.  **Semantic Search**: The user's question is embedded, and a semantic search is performed against the FAISS index to find the most relevant verses from the holy texts.
6.  **Context Grouping**: The retrieved verses are grouped by their respective holy book.
7.  **LLM Prompting**: The retrieved verses (context) and the user's question are formatted into a prompt for the Gemini LLM.
8.  **Response Generation**: The Gemini model generates an answer based on the provided context, following specific instructions to format the response by book, including a relevant verse, its meaning, and an example.
9.  **Out-of-Scope Handling**: Questions identified as general knowledge, greetings, or unrelated topics are flagged as "out of scope" immediately, without calling the LLM.

## How to Run WiseBot

1.  **Open the Colab Notebook**: Load the `WiseBot.ipynb` notebook in Google Colab.
2.  **Run All Cells**: Execute all code cells sequentially from top to bottom. Ensure Google Drive is mounted and API key is configured.
3.  **Start Chat**: Once the setup cells have finished executing, the interactive chat will start automatically by running the last cell under `## Interactive Question Answering Logic`.

    ```python
    if __name__ == "__main__":
        logger.info("Application started.")
        handler.flush()
        ask_question()
        logger.info("Application ended.")
        handler.flush()
        handler.close()
    ```

    You will be prompted to type your questions.

### Example Interaction

```
I am WiseBot, your philosophical companion.
Ask me any question and gain Wisdom!
You: what is pain
Choose books by entering numbers separated by commas (e.g., 1,2 for Gita and Quran):
1. Gita
2. Quran
3. Bible
Enter your choices (e.g., 1,2 for Gita and Quran): 1
According to Gita:
Verse: It is said the fruit of actions performed in the mode of passion result in pain, while those performed in the mode of ignorance result in darkness.
Meaning: Pain arises not just from external circumstances, but also from our own actions, particularly those driven by passion (intense desire and attachment) or ignorance (lack of wisdom and understanding). These actions lead to suffering.
Example: A student who cheats on an exam out of passion for a good grade, or out of ignorance about the value of learning, might face the pain of guilt, potential expulsion, or a lack of genuine knowledge if caught.
You: what is the meaning of life
Choose books by entering numbers separated by commas (e.g., 1,2 for Gita and Quran):
1. Gita
2. Quran
3. Bible
Enter your choices (e.g., 1,2 for Gita and Quran): 1,2,3
According to Gita:
Verse: I am the Supreme Goal of all living beings, and I am also their Sustainer, Master, Witness, Abode, Shelter, and Friend. I am the Origin, End, and Resting Place of creation; I am the Repository and Eternal Seed.
Meaning: Life's meaning is found in recognizing and connecting with the divine, which is the source and sustainer of all existence. The ultimate purpose is to realize this divine connection.
Example: A person dedicates their life to selfless service and spiritual practice, seeking to understand and align themselves with a higher purpose beyond their individual desires.
According to Quran:
Verse: And nothing is the life of this world but a play and a passing delight; and the life in the hereafter is by far the better for all who are conscious of God.
Meaning: The true meaning and purpose of life lies not in the temporary pleasures of this world, but in preparing for the eternal life in the hereafter through consciousness of God and righteous living.
Example: Someone who chooses to live a modest life, focusing on prayer and good deeds, rather than accumulating wealth and seeking fleeting worldly joys, because they believe in a greater, lasting reality.
According to Bible:
Verse: It is the spirit who gives life. The flesh profits nothing.
Meaning: The true essence of life and its ultimate meaning are spiritual, not material. Focusing on spiritual growth and connection with the divine is what truly gives life purpose and substance.
Example: A person facing hardship chooses to find strength and purpose in their faith and inner spiritual resolve, rather than solely in their physical comfort or material possessions.
You: can you tell me a joke
This question is out of scope for my purpose.
You: exit
```

## Customization for Other Users

If you fork this repository or want to use this code:

*   **Google Drive Path**: The `DRIVE_PATH` variable is set to `/content/drive/MyDrive/WiseBot`. If you store your `WiseBot` folder in a different location within your Google Drive, update this path accordingly in the notebook.
*   **Datasets**: Ensure your `gita.csv`, `quran.csv`, and `bible.csv` files are present in the specified `DRIVE_PATH`.
*   **Gemini API Key**: You *must* set up your own `GOOGLE_API_KEY` in Google Colab secrets as described in the "Configure Google Gemini API Key" section.
*   **Models**: The `SentenceTransformer` model (`all-MiniLM-L6-v2`) and `gemini-2.5-flash-lite` model are specified in the code. You can experiment with different models by changing these model names, but ensure compatibility and API access.
*   **Out-of-Scope Keywords**: The `OUT_OF_SCOPE_KEYWORDS` list can be modified to better suit your desired scope for the chatbot.

## Future Enhancements

*   **Expand Text Corpus**: Integrate more holy texts, philosophical works, or ethical treatises to broaden WiseBot's knowledge base.
*   **Conversational Memory**: Implement a more sophisticated memory mechanism to allow for multi-turn conversations and follow-up questions.
*   **Advanced RAG Techniques**: Explore techniques like re-ranking retrieved documents or using a more advanced fusion of retrieval and generation for improved answer quality.
*   **User Interface**: Develop a more interactive web-based interface (e.g., using Streamlit, Flask, or Gradio) to enhance user experience.
*   **Source Citation Accuracy**: Refine the extraction and presentation of source citations to pinpoint exact verses or chapters more precisely.
*   **Multilingual Support**: Explore adding support for questions and responses in multiple languages.
*   **Fine-tuning LLM**: Potentially fine-tune a smaller LLM on philosophical/religious texts for domain-specific knowledge and nuanced understanding.

## Performance and API Optimization

*   **Asynchronous API Calls**: Implement asynchronous calls to the Gemini API to handle multiple requests concurrently, reducing wait times for users with high query volumes.
*   **Dynamic Batching for Embeddings**: Optimize embedding generation by dynamically batching verses based on available resources, balancing speed and memory usage.
*   **Quantization**: Explore quantizing the Sentence Transformer model and/or the FAISS index to reduce their memory footprint and potentially speed up inference on resource-constrained environments.
*   **Distributed FAISS Index**: For extremely large text corpora, consider sharding the FAISS index and distributing it across multiple nodes or using a GPU-accelerated FAISS index.
*   **API Rate Limit Handling**: Implement robust error handling and retry mechanisms for Gemini API calls, specifically to manage rate limits and transient network errors gracefully.

## Contributions & Feedback

Feel free to fork the repository, open issues, or submit pull requests. Your contributions to improving WiseBot are welcome!

## Connect

*   **LinkedIn**: [Link to your LinkedIn profile/post]

Share your experience with WiseBot! It's a great project to highlight on platforms like LinkedIn.
