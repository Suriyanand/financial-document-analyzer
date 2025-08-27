# 🚀 Financial Document Analyzer – A Debugging & Refactoring Journey

## Project Overview

This project is an AI-powered **financial document analysis tool** built with **CrewAI** and **FastAPI**. It accepts a PDF financial report, processes it with a team of AI agents running on a local LLM, and provides a comprehensive analysis.

This repository documents the end-to-end process of taking a deliberately broken application and transforming it into a robust, functional tool. The journey involved fixing core application logic, resolving complex dependency conflicts, configuring system-level environments, and implementing advanced features like background processing and database persistence for a complete **MVP (Minimum Viable Product)** architecture.

---

## 🎯 The Challenge: From Broken Code to a Robust MVP

The project began with a non-functional codebase. The core logic was present but plagued with placeholder code, logical errors, and incorrect configurations. The primary goal was to systematically identify, diagnose, and fix every bug to bring the application to a fully operational and stable state.

---

## 🛠️ The Debugging & Resolution Process

The debugging journey was broken down into four distinct phases, each addressing a different layer of the technology stack.

### Phase 1: Core Application Logic

The first step was to address the **fundamental errors in the Python code itself**.

* **Bug: Missing Language Model (LLM) Initialization.**
    * **Problem:** The `agents.py` file used an `llm` variable without ever defining it. The AI agents had no "brain" to think with.
    * **Solution:** To avoid external API key dependencies, we chose a local LLM. We implemented code to import and initialize **Ollama** from `langchain_community`, providing the necessary intelligence to the AI agents.

* **Bug: Incorrect Tool Definitions.**
    * **Problem:** The custom tool for reading PDFs was defined in a way that the agent framework could not correctly parse, leading to a `KeyError: 'tools'` during the agent's validation process.
    * **Solution:** We refactored the tool in `tools.py` from a class method to a simple function and applied the `@tool` decorator from `crewai_tools`. This transformed the function into a valid `Tool` object that the agent framework could correctly understand and utilize.

---

### Phase 2: Python Environment & Dependency Management

This was the most challenging phase, involving a series of library conflicts that crashed the application—a situation commonly known as "Dependency Hell."

* **Bug: Strict Version Conflicts (`python-dotenv`).**
    * **Problem:** The application failed to install due to an `ERROR: ResolutionImpossible`. The `crewai` library had a strict dependency on `python-dotenv==1.0.0`, but our `requirements.txt` file specified version `1.0.1`.
    * **Solution:** We resolved the conflict by pinning the dependency in `requirements.txt` to the exact version required by `crewai`: `python-dotenv==1.0.0`.

* **Bug: NumPy 2.0 Incompatibility.**
    * **Problem:** The application crashed on startup with an `AttributeError`. A sub-dependency, `chromadb`, was not yet compatible with the brand-new NumPy 2.0 release and was trying to use a removed feature.
    * **Solution:** We forced a downgrade of NumPy to the last stable version of the previous generation, which `chromadb` was built for: `pip install numpy==1.26.4`.

* **Bug: PDF Library Conflicts (`pdfminer.six` & `matplotlib`).**
    * **Problem:** The PDF reader tool failed with multiple `ImportError` and `ModuleNotFound` errors. This was caused by a version mismatch between `unstructured` and its dependency `pdfminer.six`, as well as a missing optional dependency, `matplotlib`, needed for complex layouts.
    * **Solution:** We resolved this by downgrading `pdfminer.six` to a specific, known-stable version (`20221105`) and explicitly installing `matplotlib`.

---

### Phase 3: LLM Integration & Optimization

With a stable environment, the next challenge was ensuring the AI agents could perform their tasks reliably.

* **Bug: Poor Tool-Using Capability of `llama2`.**
    * **Problem:** The initial `llama2` model understood the *intent* to use the PDF reader tool but consistently failed to format its commands in the exact structured way CrewAI expected. This caused the agent to get stuck in an infinite loop of failed attempts.
    * **Solution:** We upgraded the LLM from Llama 2 to **Llama 3**. Llama 3 is significantly better at "function calling"—the ability to generate perfectly formatted commands for tools. This switch completely solved the reliability issue.

---

### Phase 4: System-Level Configuration

The final bugs were not with Python packages, but with the underlying machine environment.

* **Bug: Missing NLTK Data Package (`punkt`).**
    * **Problem:** The `unstructured` library requires a data package from the Natural Language Toolkit (NLTK) for sentence tokenization, causing a `Resource punkt_tab not found` error.
    * **Solution:** We created and ran a simple Python script to programmatically download and install the required `punkt` package.

* **Bug: Missing System Utility (Poppler).**
    * **Problem:** For robust PDF processing, `unstructured` relies on a system utility called `Poppler`. The application failed with the error `Is poppler installed and in PATH?`.
    * **Solution:** We downloaded the Poppler binaries for Windows, extracted them, and added the location to the Windows System PATH. To create a foolproof solution, we also added three lines of code to `main.py` to programmatically add the Poppler path to the environment at runtime, ensuring the application could always find it.

---

## ✨ Architectural Decisions & Bonus Features

With the core application stabilized, we implemented advanced features, making strategic choices suitable for an MVP.

#### Asynchronous Worker Model (FastAPI BackgroundTasks)

* **The Choice:** We used FastAPI's built-in `BackgroundTasks` instead of a full Celery/Redis stack.
* **The "Why":** This was a strategic decision to demonstrate the **principle of non-blocking I/O** without the significant setup overhead of a dedicated message broker. It proves an understanding of asynchronous processing in a pragmatic, time-efficient manner perfect for an MVP. The API immediately returns a `job_id` while the heavy AI analysis runs in the background.

#### Database Integration (SQLite)

* **The Choice:** We used Python's built-in `SQLite` instead of a client-server database like PostgreSQL.
* **The "Why":** SQLite is a serverless, file-based database ideal for **rapid prototyping and demonstration**. It allowed us to add persistence for job tracking and results without requiring a separate database server. This choice demonstrates an understanding of selecting the right tool for the project's scale, while acknowledging that a production system would be migrated to a more robust database.

---

## 💡 Key Skills Demonstrated

* **Comprehensive Debugging:** Systematically diagnosing and resolving issues across application logic, library dependencies, and the system environment.
* **Dependency Management:** Resolving complex version conflicts (`pip`, `requirements.txt`) to create a stable Python environment.
* **AI / LLM Integration:** Configuring and utilizing local Large Language Models (LLMs) and making informed decisions to upgrade models (`Llama 2` to `Llama 3`) to meet functional requirements.
* **Software Architecture:** Implementing a robust MVP using modern web frameworks (FastAPI), including background task processing and database persistence.
* **Environment Configuration:** Managing system-level dependencies beyond the package manager, including data packages (NLTK) and external binaries (Poppler), and configuring the system `PATH`.
