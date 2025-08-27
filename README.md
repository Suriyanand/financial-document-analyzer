# 🚀 Project: The Financial Document Analyzer - A Debugging Journey

This project is an AI-powered **financial document analysis tool** built with **CrewAI** and **FastAPI**. It accepts a PDF financial report, processes it with a team of AI agents, and provides a comprehensive analysis.

This repository showcases the journey of transforming a deliberately broken application into a robust, functional tool by systematically debugging a wide range of real-world issues. The final product includes advanced features like background task processing and database integration for a complete **MVP (Minimum Viable Product)** architecture.

---

## 🎯 The Initial State: A Debugging Challenge

The project began with a non-functional codebase. The core logic was present, but it was plagued with placeholder code, logical errors, and incorrect configurations. The primary goal was to systematically identify, diagnose, and fix every bug to bring the application to a fully operational state.

---

## 🛠️ The Debugging Journey

### Phase 1: Fixing the Core Application Logic

The first step was to address the **fundamental errors in the Python code itself**.

* **Bug: Missing Language Model (LLM) Initialization.**
    * **Problem:** The `agents.py` file used an `llm` variable without ever defining it. The AI agents had no "brain."
    * **Solution:** To avoid API key requirements, we chose a local LLM. We added code to import and initialize **Ollama**, starting with the `llama2` model.

* **Bug: Incorrect Tool Definitions.**
    * **Problem:** The custom tool for reading PDFs was defined incorrectly, causing a `KeyError: 'tools'` during agent validation because the framework could not parse it.
    * **Solution:** We refactored the tool in `tools.py` to be a simple function decorated with the `@tool` decorator from `crewai_tools`, making it a valid object the agent framework could understand.

---

### Phase 2: Taming the Local LLM

Even with the code logic fixed, the AI agents were not performing their tasks correctly.

* **Bug: Poor Tool-Using Capability of `llama2`.**
    * **Problem:** The `llama2` model understood the *intent* to use a tool but failed to format its command in the exact structured way CrewAI expected. This caused the agent to get stuck in a loop.
    * **Solution:** We upgraded the LLM to **Llama 3** (`ollama pull llama3`), which is significantly better at "function calling" (formatting tool use commands correctly), completely solving the issue.

---

### Phase 3: Conquering the Python Environment (Dependency Hell)

This phase involved solving a series of library conflicts that crashed the application.

* **Bug: Strict Version Conflicts (`python-dotenv`).**
    * **Problem:** The `crewai` library required `python-dotenv==1.0.0`, but our `requirements.txt` had `1.0.1`, causing an `ERROR: ResolutionImpossible`.
    * **Solution:** We pinned the dependency in `requirements.txt` to the exact required version: `python-dotenv==1.0.0`.

* **Bug: NumPy 2.0 Incompatibility.**
    * **Problem:** The app crashed with an `AttributeError` because a sub-dependency, `chromadb`, was not compatible with the brand-new NumPy 2.0 release.
    * **Solution:** We forced a downgrade of NumPy to a compatible version: `pip install numpy==1.26.4`.

* **Bug: PDF Library Conflicts (`pdfminer.six` & `matplotlib`).**
    * **Problem:** The PDF reader tool failed with multiple `ImportError` and `ModuleNotFound` errors due to version mismatches and missing optional dependencies.
    * **Solution:** We downgraded `pdfminer.six` to a known-stable version and explicitly installed `matplotlib` to provide full support for the `unstructured` library.

---

### Phase 4: Solving System-Level Dependencies

The final bugs were not with Python packages, but with the underlying machine environment.

* **Bug: Missing NLTK Data Package (`punkt`).**
    * **Problem:** The `unstructured` library requires a data package from the Natural Language Toolkit (NLTK), causing a `Resource punkt_tab not found` error.
    * **Solution:** We created and ran a simple Python script to download and install the required `punkt` package.

* **Bug: Missing System Utility (Poppler).**
    * **Problem:** For robust PDF processing, `unstructured` relies on a system utility called `Poppler`. The application failed with the error `Is poppler installed and in PATH?`.
    * **Solution:** We downloaded the Poppler binaries, extracted them to `C:\poppler`, and added the `C:\poppler\bin` directory to the Windows System PATH to make it accessible to the application.

---

## ✨ Bonus Features: A Robust MVP Architecture

With the core application working, we added advanced features to demonstrate a more complete architecture.

* **Queue Worker Model:** We used **FastAPI's built-in `BackgroundTasks`** to create a non-blocking API. When a user uploads a document, the server immediately returns a `job_id`, and the long-running AI analysis is processed in the background. This simulates a real-world worker queue without the setup overhead of Celery/Redis.

* **Database Integration:** We used **Python's built-in SQLite** to add persistence. A simple `database.py` module handles creating a database file (`analysis_results.db`), storing job statuses, and saving the final analysis, allowing users to retrieve their results later using their `job_id`.

---

## 💡 Key Skills Demonstrated

* **Comprehensive Debugging:** Systematically diagnosing and resolving issues across application logic, library dependencies, and the system environment.
* **Dependency Management:** Resolving complex version conflicts and managing a stable Python environment with `pip` and `requirements.txt`.
* **AI / LLM Integration:** Configuring and utilizing local Large Language Models (LLMs) like Llama 2/3 with frameworks such as CrewAI and LangChain.
* **Software Architecture:** Implementing a robust MVP using modern web frameworks (FastAPI), including background task processing and database persistence (SQLite).
* **Environment Configuration:** Managing system-level dependencies, including data packages (NLTK) and external binaries (Poppler), and configuring the system PATH.
