# Document Extraction Engine

This is a full-stack document intelligence application. It allows uploading unstructured documents (invoices, resumes, and contracts) as PDFs, Word files, plain text, or raw images, and automatically organizes their data fields into structured, interactive tables. 

The application is fully audited, meaning any extracted value can be edited directly inside the UI, and changes are instantly saved to a local database.

---

## Key Capabilities

* **Multi-Format Text Parser**: Directly extracts text from clear PDFs, DOCX files, and plain text documents.
* **Intelligent OCR Fallback**: If a document is scanned, blank, or uploaded as an image (PNG/JPG), it automatically bypasses standard parsing and streams the raw bytes directly to Gemini's visual model to analyze tables and layouts.
* **Interactive Editing**: Double-click any cell in the UI to correct values, update numbers, or refine descriptions instantly.
* **Confidence Scoring**: Every extracted field is tagged with a **High**, **Medium**, or **Low** indicator, letting you quickly spot which fields require review.
* **SQLite Persistence**: All processed uploads, original texts, and manual edits are recorded locally in a database so you can reload your work anytime.
* **Dual-Format Export**: Download your completed data tables as clean CSV files or raw JSON schemas.

---

## Project Architecture

The codebase is split into two independent services to ensure high responsiveness.

### 1. The React Frontend (`frontend/src/`)
Designed as a modern single-page dashboard with glassmorphic borders and smooth transitions:

* **`App.jsx`**: Coordinates the global client state, coordinates uploads to the API, handles browser-side settings, and handles routing between components.
* **`components/DocumentUpload.jsx`**: A drag-and-drop file interface with built-in format checkers (validating files up to 10MB).
* **`components/ExtractionsList.jsx`**: The left sidebar showing your historical records, complete with visual status lights (Ready vs. Error) and document category tags.
* **`components/ExtractionResult.jsx`**: The core results panel. It renders standard input fields for single attributes, handles dynamic grid editors for nested tables (like line items or work history), and houses the CSV/JSON download widgets.
* **`components/Loader.jsx`**: A rotating, minimal loading spinner that plays during processing.
* **`index.css`**: Holds all customized light-mode styles, scrollbars, confidence badges (emerald for high, amber for medium, ruby for low), and micro-animations.

### 2. The Python Backend (`backend/app/`)
A fast, typed API layer built on top of FastAPI and Pydantic:

* **`main.py`**: Declares API routes (extractions, lists, manual updates), handles temporary directory buffering, and manages CORS settings.
* **`extractor.py`**: Integrates with LLMs (Google Gemini and OpenAI) with strict temperature control to prevent guesswork. If a field is missing in the document, it safely returns `null` along with an explanation note.
* **`schemas.py`**: Defines standard schemas for **Invoices**, **Resumes**, and **Contracts** using Pydantic, acting as the structural blueprint for LLM outputs.
* **`parser.py`**: Determines file types and extracts raw string lines. It also detects if a PDF is scanned (less than 50 characters of readable text) and flags it for image analysis.
* **`database.py`**: Manages the local SQLite database (`extractions.db`), handles table migrations, and records updates.

---

## Quick Start

### 1. Set Up the Backend Configuration
Inside the `backend/` folder, create a file named `.env` and add your API keys:

```env
API_PROVIDER=gemini

### 2. Launch the Application
Double-click the automatic **`run.bat`** script in the root directory, or execute it in your terminal:

```powershell
.\run.bat
```

This script will set up a Python virtual environment, install all requirements, and launch both services:
* **Frontend Dashboard**: `http://localhost:5173`
* **Backend API Docs**: `http://localhost:8000/docs`
