# PrivateChatGPT
Private ChatGPT download in local
privateGPT
Ask questions to your documents without an internet connection, using the power of LLMs. 100% private, no data leaves your execution environment at any point. You can ingest documents and ask questions without an internet connection!

Built with LangChain, GPT4All, LlamaCpp, Chroma and SentenceTransformers.
Environment Setup
In order to set your environment up to run the code here, first install all requirements:

pip3 install -r requirements.txt
Then, download the LLM model and place it in a directory of your choice:

LLM: default to ggml-gpt4all-j-v1.3-groovy.bin. If you prefer a different GPT4All-J compatible model, just download it and reference it in your .env file.
Rename example.env to .env and edit the variables appropriately.

MODEL_TYPE: supports LlamaCpp or GPT4All
PERSIST_DIRECTORY: is the folder you want your vectorstore in
MODEL_PATH: Path to your GPT4All or LlamaCpp supported LLM
MODEL_N_CTX: Maximum token limit for the LLM model
EMBEDDINGS_MODEL_NAME: SentenceTransformers embeddings model name (see https://www.sbert.net/docs/pretrained_models.html)
TARGET_SOURCE_CHUNKS: The amount of chunks (sources) that will be used to answer a question
Note: because of the way langchain loads the SentenceTransformers embeddings, the first time you run the script it will require internet connection to download the embeddings model itself.

Test dataset
This repo uses a state of the union transcript as an example.

Instructions for ingesting your own dataset
Put any and all your files into the source_documents directory

The supported extensions are:

.csv: CSV,
.docx: Word Document,
.doc: Word Document,
.enex: EverNote,
.eml: Email,
.epub: EPub,
.html: HTML File,
.md: Markdown,
.msg: Outlook Message,
.odt: Open Document Text,
.pdf: Portable Document Format (PDF),
.pptx : PowerPoint Document,
.ppt : PowerPoint Document,
.txt: Text file (UTF-8),
Run the following command to ingest all the data.

python ingest.py
Output should look like this:

Creating new vectorstore
Loading documents from source_documents
Loading new documents: 100%|██████████████████████| 1/1 [00:01<00:00,  1.73s/it]
Loaded 1 new documents from source_documents
Split into 90 chunks of text (max. 500 tokens each)
Creating embeddings. May take some minutes...
Using embedded DuckDB with persistence: data will be stored in: db
Ingestion complete! You can now run privateGPT.py to query your documents
It will create a db folder containing the local vectorstore. Will take 20-30 seconds per document, depending on the size of the document. You can ingest as many documents as you want, and all will be accumulated in the local embeddings database. If you want to start from an empty database, delete the db folder.

Note: during the ingest process no data leaves your local environment. You could ingest without an internet connection, except for the first time you run the ingest script, when the embeddings model is downloaded.

Ask questions to your documents, locally!
In order to ask a question, run a command like:

python privateGPT.py
And wait for the script to require your input.

> Enter a query:
Hit enter. You'll need to wait 20-30 seconds (depending on your machine) while the LLM model consumes the prompt and prepares the answer. Once done, it will print the answer and the 4 sources it used as context from your documents; you can then ask another question without re-running the script, just wait for the prompt again.

Note: you could turn off your internet connection, and the script inference would still work. No data gets out of your local environment.

Type exit to finish the script.

CLI
The script also supports optional command-line arguments to modify its behavior. You can see a full list of these arguments by running the command python privateGPT.py --help in your terminal.

How does it work?
Selecting the right local models and the power of LangChain you can run the entire pipeline locally, without any data leaving your environment, and with reasonable performance.

ingest.py uses LangChain tools to parse the document and create embeddings locally using HuggingFaceEmbeddings (SentenceTransformers). It then stores the result in a local vector database using Chroma vector store.
privateGPT.py uses a local LLM based on GPT4All-J or LlamaCpp to understand questions and create answers. The context for the answers is extracted from the local vector store using a similarity search to locate the right piece of context from the docs.
GPT4All-J wrapper was introduced in LangChain 0.0.162.
System Requirements
Python Version
To use this software, you must have Python 3.10 or later installed. Earlier versions of Python will not compile.

C++ Compiler
If you encounter an error while building a wheel during the pip install process, you may need to install a C++ compiler on your computer.

For Windows 10/11
To install a C++ compiler on Windows 10/11, follow these steps:

Install Visual Studio 2022.
Make sure the following components are selected:
Universal Windows Platform development
C++ CMake tools for Windows
Download the MinGW installer from the MinGW website.
Run the installer and select the gcc component.
Mac Running Intel
When running a Mac with Intel hardware (not M1), you may run into clang: error: the clang compiler does not support '-march=native' during pip install.

If so set your archflags during pip install. eg: ARCHFLAGS="-arch x86_64" pip3 install -r requirements.txt

Disclaimer
This is a test project to validate the feasibility of a fully private solution for question answering using LLMs and Vector embeddings. It is not production ready, and it is not meant to be used in production. The models selection is not optimized for performance, but for privacy; but it is possible to use different models and vectorstores to improve performance.
