Project: The Financial Document Analyzer - A Debugging Journey
This project is an AI-powered financial document analysis tool built with CrewAI and FastAPI. It accepts a PDF financial report, processes it with a team of AI agents, and provides a comprehensive analysis. This repository showcases the journey of taking a deliberately broken application and systematically debugging a wide range of real-world issues to create a robust, functional tool with advanced features like background processing and database integration.

The Initial State: A Debugging Challenge
The project began with a non-functional codebase. The core logic was present, but it was plagued with placeholder code, logical errors, and incorrect configurations. The primary goal was to systematically identify, diagnose, and fix every bug to bring the application to a fully operational state.

The Debugging Journey
Phase 1: Fixing the Core Application Logic
The first step was to address the fundamental errors in the Python code itself.

Bug: Missing Language Model (LLM) Initialization.

Problem: The agents.py file used an llm variable without ever defining it. The AI agents had no "brain."

Solution: To avoid API key requirements, we chose a local LLM. We added code to import and initialize Ollama, starting with the llama2 model.

Bug: Incorrect Tool Definitions.

Problem: The custom tool for reading PDFs was defined in a way that the agent framework could not correctly parse, leading to a KeyError: 'tools' during agent validation.

Solution: We refactored the tool in tools.py from a class method to a simple function and decorated it with the @tool decorator from crewai_tools. This converted it into a valid object the agent framework could understand.

Phase 2: Taming the Local LLM
Even with the code logic fixed, the AI agents were not performing their tasks correctly.

Bug: Poor Tool-Using Capability of llama2.

Problem: The llama2 model understood the intent to use the PDF reader tool but failed to format its command in the exact structured way CrewAI expected. This caused the agent to get stuck in a loop, repeatedly failing to call its tool.

Solution: We upgraded the LLM. We downloaded Llama 3 (ollama pull llama3) and updated agents.py to use llm = Ollama(model="llama3"). Llama 3 is significantly better at "function calling" (formatting tool use commands correctly), which completely solved the issue.

Phase 3: Conquering the Python Environment (Dependency Hell)
This was the most challenging phase, involving a series of library conflicts and missing packages that crashed the application.

Bug: Strict Version Conflicts (python-dotenv).

Problem: The application failed to install due to an ERROR: ResolutionImpossible. The crewai library required python-dotenv==1.0.0 specifically, but our requirements.txt had 1.0.1.

Solution: We edited requirements.txt to pin the dependency to the exact required version: python-dotenv==1.0.0.

Bug: NumPy 2.0 Incompatibility.

Problem: The app crashed on startup with an AttributeError because a sub-dependency, chromadb, was not compatible with the brand-new NumPy 2.0 release.

Solution: We forced a downgrade of NumPy to the last stable version of the previous generation: pip install numpy==1.26.4.

Bug: PDF Library Conflicts (pdfminer.six).

Problem: The PDF reader tool failed again with an ImportError, indicating a version mismatch between unstructured and its dependency pdfminer.six.

Solution: We downgraded pdfminer.six to a specific, known-stable version: pip install "pdfminer.six==20221105".

Bug: Missing Optional Dependencies (matplotlib).

Problem: The PDF reader then failed with No module named 'matplotlib', an optional dependency unstructured uses for complex layouts.

Solution: We explicitly installed the missing package: pip install matplotlib.

Phase 4: Solving System-Level Dependencies
The final bugs were not with Python packages, but with the underlying machine environment.

Bug: Missing NLTK Data Package (punkt).

Problem: The unstructured library requires a data package from the Natural Language Toolkit (NLTK) for sentence tokenization, causing a Resource punkt_tab not found error.

Solution: We created and ran a simple Python script to download and install the required punkt package.

Bug: Missing System Utility (Poppler).

Problem: For robust PDF processing, unstructured relies on a system utility called Poppler. The application failed with the error Is poppler installed and in PATH?.

Solution: This required a multi-step fix. We downloaded the Poppler binaries for Windows, extracted them to C:\poppler, and added the C:\poppler\bin directory to the Windows System PATH. To ensure this worked reliably, we also added three lines of code to main.py to programmatically add the Poppler path to the environment at runtime.

Bonus Features: Asynchronous Processing and Persistence
With the core application working, we added advanced features to demonstrate a more robust architecture.

Queue Worker Model: We used FastAPI's built-in BackgroundTasks to create a non-blocking API. When a user uploads a document, the server immediately returns a job_id, and the long-running AI analysis is processed in the background. This simulates a real-world worker queue without the overhead of Celery/Redis for this MVP.

Database Integration: We used Python's built-in SQLite to add persistence. A simple database.py module was created to handle a database file (analysis_results.db) for storing job statuses and saving the final analysis, allowing users to retrieve their results later using their job_id.

This journey showcases a wide range of real-world debugging skills, from fixing core logic and managing complex dependency conflicts to configuring system environments and making strategic architectural decisions.












Tools

