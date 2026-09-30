# **Milestone 1: Production RAG System** 



**Joshua Farmer**

**Data 790 - Advanced AI Frameworks in Production** 



#### **Overview:**

This project implements and evaluates a production-oriented Retrieval-Augmented Generation (RAG) system using a regulatory data privacy whitepaper as its source corpus. 



The system compares two RAG approaches: 



* Demo-RAG - Retrieves top 5 semantically similar chunks and generates an answer from the retrieved context



* Self-RAG - Retrieves a wider candidate set, uses an LLM to evaluate chunk relevance, selects the highest ranked evidence, and then generates an answer from the filtered context



This project also includes query checks, LLM-based security testing, cost tracking, retrieval evaluation, and failure-case analysis 



#### **System Architecture** 



User Query -> Query Validation/Security Checks -> Query Embedding -> Pinecone Vector Search ->



For Demo-RAG: Top 5 Chunks -> Answer Generation



For Self-RAG: Top 10 Chunks -> LLM Relevance Grading -> Top 3 Chunks -> Answer Generation





#### **Source Corpus**

The system uses sample\_docs/whitepaper.pdf

The PDF is processed page by page and divided into 500 character chunks with a 50 character overalp



The splitter uses Python's len() function, so that is the reason for the measure of character rather than token.



Chunking Hierarchy: Paragraph -> Line -> Sentence -> Word -> Character


Each chunk stores with the metadata:

* ChunkID
* Original Text
* Page Number
* Source Document



These are stored in pinecone



**Technologies Used:**

* Python
* Jupyter Notebook
* OpenAI-compatible API through the UNC AI API
* text-embedding-3-small
* gpt-4.1-mini
* Pinecone
* LangChain Text Splitters
* PyPDF
* pandas
* python-dotenv



Create a .env file in the project directory containing:

API\_KEY=your\_api\_key 

PINECONE\_API\_KEY=your\_pinecone\_api\_key



**DO NOT COMMIT .ENV OR API KEYS TO REPOSITORY**



#### **Evaluation**



The notebook evaluates the two RAG pipelines using a set of 20 evaluation questions.



The evaluation includes questions that are directly answerable from the source document as well as questions that are intentionally outside the source corpus.



Retrieval is evaluated using:



Precision@5 for Demo-RAG

Precision@5 for Self-RAG's initial retrieval

Precision@3 for Self-RAG's final reranked evidence



The retrieval evaluator uses lexical evidence first and an independent LLM relevance judge when lexical evidence is insufficient.



The evaluation also compares the generated answers and identifies retrieval failure cases for further analysis.



#### **Cost Tracking**



The system records token usage and estimated API cost for individual LLM calls.



Tracked call types include:



demo\_generation

self\_rag\_grading

self\_rag\_generation

retrieval\_evaluation

security\_judge



The production RAG cost is distinguished from evaluation and security-testing overhead.



For the workload projection, the measured per-query cost is scaled to 1,000 requests per day, providing daily, monthly, and yearly estimates.



#### **Security**



The notebook includes a two-layer query security approach.



Layer 1 — Deterministic Query Check



The first layer checks for:



Empty queries

Queries exceeding the 500-character limit

Suspicious instruction-override phrases

Attempts to request system instructions or credentials

Attempts to manipulate retrieval boundaries

Layer 2 — LLM Security Judge



Queries that pass the deterministic checks are evaluated by an LLM security judge.



The judge determines whether the query should be:



ALLOW



or



BLOCK



This provides a second layer of protection against prompts that may not match a predefined suspicious phrase.





#### **Repository Structure**

.

├── sample\_docs/

│   └── whitepaper.pdf

├── Milestone1\_Production\_RAG.ipynb

├── requirements.txt

├── README.md

├── .env.example

└── .gitignore



#### **Limitations**



This implementation is designed as a Milestone 1 production-oriented RAG system rather than a complete production deployment.



Current limitations include:



The system uses a single source document.

Retrieval evaluation uses dynamically generated relevance judgments rather than a manually labeled gold-standard dataset.

The lexical relevance heuristic can produce false positives when a keyword appears as a substring of another word.

LLM-based retrieval grading introduces additional latency and API cost.

Embedding costs are separate from the currently tracked chat-completion costs.

The system does not yet include a user-facing application interface.



These limitations provide areas for improvement in later milestones.

