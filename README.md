# 🚀 Financial Document Analyzer – A Debugging & Refactoring Journey

## Project Overview

This project is an AI-powered **financial document analysis tool** built with **CrewAI** and **FastAPI**. It accepts a PDF financial report, processes it with a team of AI agents running on a local LLM, and provides a comprehensive analysis.

This repository documents the end-to-end process of taking a deliberately broken application and transforming it into a robust, functional tool. The journey involved fixing core application logic, resolving complex dependency conflicts, configuring system-level environments, and implementing advanced features like background processing and database persistence for a complete **MVP (Minimum Viable Product)** architecture.

---

## ✨ Key Features

- 🧠 **AI-Powered Analysis:** Leverages a multi-agent system (CrewAI) to perform in-depth analysis of PDF financial reports.  
- ⚡ **Asynchronous API:** Uses FastAPI's `BackgroundTasks` for a non-blocking user experience, immediately returning a `job_id` while processing happens in the background.  
- 💾 **Database Persistence:** Implements a SQLite database to track job status and store final analysis results, which can be retrieved at any time.  
- 💻 **Local LLM Integration:** Powered by Ollama (`Llama 3`) for privacy, cost-effectiveness, and to eliminate external API key dependencies.  

---

## 🏛️ System Architecture

The application follows an asynchronous worker model. The user interacts with a FastAPI server, which offloads the heavy AI processing to a background task and stores the result in a database.

[User] --(1. Upload PDF)--> [FastAPI Server] --(2. Returns Job ID Instantly)--> [User]
    | 
    '--(6. Check Status w/ Job ID)--> [FastAPI Server] --(3. Dispatches Background Task)--> [CrewAI Worker] --> [AI Agents] --(4. Analyze Document)--> [AI Agents] --(5. Stores Result)--> [SQLite DB]'

