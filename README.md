# Document Parser & Editor

This application helps organize unstructured files like invoices, resumes, or contracts into structured tables that can be edited in real-time. It handles PDFs, Word documents, text files, and images, saving all extractions and manual corrections locally.

---

## Key Features

* **File Text Extraction**: Directly reads text from standard PDFs, DOCX, and TXT files.
* **OCR & Visual Fallback**: If a document is scanned, blank, or uploaded as an image, it automatically routes through Gemini's visual API to analyze the layout.
* **In-Line Editing**: Double-click any cell in the table to correct values manually, which instantly updates the local database.
* **Confidence Tags**: High, Medium, or Low tags are displayed for each field based on how clearly the information was parsed.
* **Local History Log**: Extractions are saved to a local SQLite database, allowing you to reload past work from the sidebar.
* **Data Exporting**: Download your finalized datasets as CSV tables or raw JSON files.

---

## Getting Started

Make sure you have **Python 3.10+** and **Node.js 18+** installed on your system.

### 1. Set Up the Environment Variables
Go to the `backend/` folder and create a `.env` file (or edit the existing one) with these keys:

```env
API_PROVIDER=gemini

# Google Gemini Credentials
GEMINI_API_KEY=your_actual_api_key_here
GEMINI_MODEL=gemini-2.5-flash

# OpenAI Credentials (Optional fallback)
OPENAI_API_KEY=your_openai_key_here
OPENAI_MODEL=gpt-4o-mini
```

*(Note: If you don't want to edit files, you can also paste your API keys directly in the frontend dashboard under "Configure Connection").*

### 2. Run the App
An automatic Windows launcher script handles environment setup and starts both servers. Simply double-click **`run.bat`** in the root directory, or execute it in your terminal:

```powershell
.\run.bat
```

The script will automatically:
1. Initialize a Python virtual environment (`.venv`).
2. Install all necessary backend dependencies.
3. Start the FastAPI server on `http://localhost:8000`.
4. Start the React frontend on `http://localhost:5173`.

---

## Project Structure

Here is how the project files are organized:

* `backend/app/schemas.py`: Defines standard templates for Invoices, Resumes, and Contracts.
* `backend/app/parser.py`: Extracts raw text from files and decides if they need OCR processing.
* `backend/app/extractor.py`: Configures structured prompts and connects with Gemini or OpenAI.
* `backend/app/database.py`: Manages the local SQLite database (`extractions.db`) and saves manual changes.
* `backend/app/main.py`: Handles API endpoints for uploading, retrieving, and updating files.
* `frontend/`: React single-page application and its CSS configurations.
