# Document Extraction Engine

This is a full stack document extractor engine designed to extract structured tables from files like invoices, resumes, and contracts. It runs on a React frontend and a FastAPI backend with a local SQLite database keeping records of all past extractions and manual edits.

---

## Setup Steps

To set this up, make sure the host computer has Python 3.10 or higher and Node.js 18 or higher installed.

1. Configure the Backend: Navigate to the backend folder and create a new file named .env. In this file, write API_PROVIDER=gemini on the first line, GEMINI_API_KEY=your_key on the second line, and GEMINI_MODEL=gemini-2.5-flash on the third line. To use OpenAI instead, write API_PROVIDER=openai, OPENAI_API_KEY=your_key, and OPENAI_MODEL=gpt-4o-mini.
2. Optional UI Key Setup: If choosing not to create a configuration file, skip step 1 and paste the API keys directly into the connection settings button inside the browser dashboard once the app starts.
3. Launch the Application: Double-click the run.bat file in the main project directory, or open a terminal and execute it by typing .\run.bat. This script automatically builds the virtual environment, installs backend dependencies, and starts both the FastAPI server on port 8000 and the React development server on port 5173.
4. Open the Interface: Open a web browser and navigate to http://localhost:5173 to start using the document extraction engine.

---

## Prompt Design Explanation

To parse messy documents accurately, the application sends a rigid instructions guide to the language model. The prompt focuses on three main rules:

Accuracy and Grounding: The model is instructed to act as a strict extraction parser. It must only extract fields that are explicitly stated in the source text or image.

Hallucination Block: If a field is not found in the uploaded document, the model is strictly forbidden from guessing. It must return a null value for that field and write a short, helpful explanation in the note parameter (such as Field not present in document).

Confidence Grading: For every extracted field, the model must assign a confidence score. It rates it as High if the value is clearly read and matches the expected type, Medium if the value is present but required formatting or contextual inference, or Low if the value was blurry, poorly read, or highly ambiguous.

---

## Schema Definitions

The extraction engine validates all data structures against strict definitions before presenting them. Every document type contains fields grouped under the ExtractedField class, containing a value, a confidence grade, and a source note.

Invoice Template:
* Vendor Name: Name of the billing merchant or company.
* Invoice Date: The exact date the bill was issued.
* Invoice Number: The unique identification number of the bill.
* Line Items: A list of individual products or services, each containing its own description, quantity, unit price, and total amount.
* Subtotal: Net amount billed before tax.
* Tax: Total tax amount applied.
* Total: The grand total billed.
* Billing Address: Billed location or merchant address.

Resume Template:
* Name: Candidate's full name.
* Email: Direct contact email.
* Phone: Contact phone number.
* LinkedIn or Website: Social profile or portfolio links.
* Summary: Personal biography statement.
* Skills: List of technical or soft skills.
* Experience: Professional history list, each item with a job title, company name, employment period, and list of responsibilities.
* Education: Academic history list, each item with a degree name, institution, and graduation year.
* Certifications: Active achievements or credentials.

Contract Template:
* Contract Title: Official agreement name.
* Parties: List of corporations or individuals signing the agreement.
* Effective Date: Date the contract becomes active.
* Expiration Date: End date or termination criteria.
* Governing Law: The legal jurisdiction for resolving disputes.
* Termination Clause: Summary of terms to end the agreement.
* Key Obligations: Primary duties promised by the signing parties.

---

## Handling Extraction Failures

The system is designed to handle document failures at multiple levels to ensure the app never crashes:

Visual File Detection: Standard text readers fail entirely when analyzing raw images or scanned PDFs (which yield blank texts). When the parser reads a file and finds fewer than 50 text characters, it automatically flags the document as visual. It then converts the raw file bytes into a base64 string and routes it directly to Gemini's multi-modal visual API to read the document layout visually.

API Communication Failures: If the language model fails due to a network timeout, invalid credentials, or rate limits, the system catches the error. Instead of breaking the interface, it registers a new database record in extractions.db with a Failed status and logs the exact error description in the database.

User Visibility: The history sidebar displays a red Error badge next to failed documents. Hovering over the badge shows the precise error message returned by the server (such as connection error or invalid api key) to see exactly what to fix.
