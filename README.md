# LegalEase - AI-Powered Legal Document Generator

LegalEase is an AI-assisted application for generating structured legal
documents from user-provided information.

The application accepts:

- Document type
- Parties involved
- Terms and conditions
- Effective date

It generates a structured legal document, provides an editable preview,
and allows the result to be downloaded as:

- TXT
- DOCX
- PDF

> **Important:** LegalEase is a drafting-assistance application and does
> not replace advice from a qualified lawyer. Generated content should be
> reviewed for accuracy, jurisdiction, and enforceability before use.

---

## Project Overview

LegalEase was designed as a full-stack Generative AI application using:

- Python
- Streamlit
- FastAPI
- Google Gemini
- Pydantic
- python-docx
- FPDF
- Pillow

The application separates the user interface, backend API, AI-generation
logic, and document-formatting logic into independent modules.

---

## Architecture

```text
                   USER
                    |
                    v
          +--------------------+
          | Streamlit Frontend |
          |   frontend/app.py  |
          +---------+----------+
                    |
                    | HTTP POST /generate
                    v
          +--------------------+
          |   FastAPI Backend  |
          | backend/main.py    |
          | backend/routes.py  |
          +---------+----------+
                    |
                    v
          +--------------------+
          |      AI Core       |
          | Gemini Generator   |
          | Prompt Builder     |
          +---------+----------+
                    |
                    v
             Google Gemini
                    |
                    v
          Generated legal text
                    |
                    v
          +--------------------+
          | Formatting Modules |
          | TXT / DOCX / PDF   |
          +--------------------+
                    |
                    v
              User Download
````

---

## Main Features

### 1. Legal Document Generation

Users can enter document information such as:

* Employment contracts
* Non-disclosure agreements
* Freelance contracts
* Lease agreements
* Other structured legal documents

The application sends the supplied information to the FastAPI backend,
which passes the request to the Gemini generation layer.

### 2. Editable Preview

After generation, the document is displayed in the Streamlit interface
and can be edited before downloading.

### 3. Multiple Export Formats

LegalEase supports:

```text
TXT
DOCX
PDF
```

DOCX output supports structured headings, tables, branding and
signature sections.

PDF output supports structured headings, branding, footers and page
numbers.

### 4. Branding

A LegalEase logo is included in the project assets.

The application can create the PNG logo automatically for document
exports.

### 5. Offline Mock Mode

The project includes a mock generation mode for local testing without a
Gemini API key.

Set:

```env
USE_MOCK_AI=true
```

This is useful for testing the complete application before connecting
the live Gemini API.

---

## Project Structure

```text
LegalEaseAI/
|
├── ai_core/
│   ├── __init__.py
│   ├── gemini_generator.py
│   └── prompt_builder.py
|
├── assets/
│   ├── logo.svg
│   └── logo.png
|
├── backend/
│   ├── __init__.py
│   ├── config.py
│   ├── main.py
│   ├── models.py
│   └── routes.py
|
├── formatting/
│   ├── __init__.py
│   ├── branding.py
│   ├── docx_formatter.py
│   ├── markdown_utils.py
│   ├── pdf_formatter.py
│   ├── sanitize.py
│   └── txt_formatter.py
|
├── frontend/
│   └── app.py
|
├── scripts/
│   └── smoke_test.py
|
├── tests/
│   ├── test_api.py
│   └── test_formatting.py
|
├── .streamlit/
│   └── config.toml
|
├── .env.example
├── .gitignore
├── requirements.txt
├── pyproject.toml
└── README.md
```

---

## Requirements

Recommended environment:

```text
Python 3.10+
pip
```

The development environment can be isolated using Python's virtual
environment support.

---

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
```

Then:

```bash
cd LegalEaseAI
```

### 2. Create a virtual environment

macOS / Linux:

```bash
python3 -m venv .venv
```

Activate it:

```bash
source .venv/bin/activate
```

Windows PowerShell:

```powershell
python -m venv .venv
```

```powershell
.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
python -m pip install -r requirements.txt
```

---

## Environment Configuration

Create a `.env` file from `.env.example`.

Example:

```env
GEMINI_API_KEY=your_gemini_api_key
GEMINI_MODEL=gemini-3.8-flash

USE_MOCK_AI=false

BACKEND_HOST=127.0.0.1
BACKEND_PORT=8000

BACKEND_URL=http://127.0.0.1:8000

COMPANY_NAME=LegalEase

LOGO_PATH=assets/logo.png

MAX_INPUT_LENGTH=12000
MAX_GENERATED_CHARS=50000

CORS_ORIGINS=http://localhost:8501,http://127.0.0.1:8501
```

**Never commit the real `.env` file or a real API key to GitHub.**

Use `.env.example` as the public configuration template.

---

## Running the Application

The application uses two processes.

### Terminal 1 - FastAPI Backend

Activate the virtual environment and run:

```bash
source .venv/bin/activate
```

Then:

```bash
uvicorn backend.main:app --reload
```

Backend:

```text
http://127.0.0.1:8000
```

FastAPI documentation:

```text
http://127.0.0.1:8000/docs
```

### Terminal 2 - Streamlit Frontend

Open a second terminal.

Activate the environment:

```bash
source .venv/bin/activate
```

Then run:

```bash
python -m streamlit run frontend/app.py
```

Frontend:

```text
http://localhost:8501
```

---

## Using Mock AI Mode

For testing without Gemini:

```env
USE_MOCK_AI=true
```

Start both servers as described above.

Then open:

```text
http://localhost:8501
```

Enter the document information and click:

```text
Generate Document
```

The application will generate a local test document without calling the
Gemini API.

---

## Using Gemini

To use real Gemini generation:

```env
USE_MOCK_AI=false
GEMINI_API_KEY=YOUR_REAL_GEMINI_API_KEY
```

The Gemini integration uses Google's current Python GenAI SDK:

```python
from google import genai

client = genai.Client()

response = client.models.generate_content(
    model="gemini-3.8-flash",
    contents="Your prompt here",
)

print(response.text)
```

The Gemini API currently documents this `google-genai` client pattern and
the `gemini-3.8-flash` model.

---

## API

### Health Check

```text
GET /
```

Example response:

```json
{
  "status": "ok",
  "service": "LegalEase",
  "model": "gemini-3.8-flash",
  "ai_configured": true
}
```

### Generate Document

```text
POST /generate
```

Request:

```json
{
  "document_type": "Non-Disclosure Agreement",
  "parties": "Alice Smith (Disclosing Party), TechNova Inc. (Receiving Party)",
  "terms": "Confidentiality; No disclosure to third parties; 30 days notice",
  "dates": "September 27, 2026",
  "company_name": "TechNova"
}
```

Response:

```json
{
  "document_type": "Non-Disclosure Agreement",
  "content": "# NON-DISCLOSURE AGREEMENT ...",
  "model": "gemini-3.8-flash",
  "mock": false
}
```

---

## Document Generation Flow

```text
1. User enters document details
              |
              v
2. Streamlit sends POST /generate
              |
              v
3. FastAPI validates request
              |
              v
4. GeminiDocumentGenerator builds prompt
              |
              v
5. Gemini generates document
              |
              v
6. Generated text returns to Streamlit
              |
              v
7. User edits document
              |
              v
8. User downloads TXT / DOCX / PDF
```

---

## Document Formatting

### TXT

Plain UTF-8 text generated using:

```text
formatting/txt_formatter.py
```

### DOCX

Generated using:

```text
python-docx
```

The DOCX formatter supports:

* Document title
* Legal headings
* Paragraphs
* Bullet lists
* Key-term table
* Signature section
* Branding
* Footer notice

### PDF

Generated using:

```text
fpdf2
```

The PDF formatter supports:

* A4 layout
* Document headings
* Paragraphs
* Bullet-style terms
* Signature section
* Header/footer
* Page numbering
* Branding

---

## Testing

Run:

```bash
pytest
```

The project contains tests for:

* API health checks
* `/generate` endpoint
* Pydantic validation
* Text sanitization
* HTML preview generation
* TXT export
* DOCX export
* PDF export

A smoke test is also available:

```bash
python scripts/smoke_test.py
```

---

## Sample Outputs

Example generated files can be stored in:

```text
sample_outputs/
```

Recommended examples:

```text
sample_outputs/
├── sample_employment_contract.pdf
├── sample_employment_contract.docx
└── sample_employment_contract.txt
```

These are demonstration outputs and are not part of the application
source code.

---

## Deployment

The project architecture supports separate frontend and backend
deployment.

Example architecture:

```text
Streamlit Community Cloud
        |
        v
Streamlit Frontend
        |
        | HTTPS
        v
FastAPI Backend
        |
        v
Google Gemini API
```

The project can be containerized using Docker.

Example:

```bash
docker compose up --build
```

The backend and frontend containers are defined in:

```text
Dockerfile.backend
Dockerfile.frontend
docker-compose.yml
```

---

## Security Notes

Never commit:

```text
.env
.venv/
__pycache__/
*.pyc
```

API keys should only be stored in environment variables or a secure
deployment secret manager.

Generated legal documents should be reviewed before use.

---

## Technologies

| Component      | Technology    |
| -------------- | ------------- |
| Frontend       | Streamlit     |
| Backend        | FastAPI       |
| AI             | Google Gemini |
| Validation     | Pydantic      |
| Word Export    | python-docx   |
| PDF Export     | fpdf2         |
| Image/Branding | Pillow        |
| HTTP           | Requests      |
| Testing        | Pytest        |

---

## Project Documentation

The original project requirements define the following major areas:

1. Generative AI model selection
2. Full-stack architecture
3. FastAPI backend
4. Gemini integration
5. Streamlit frontend
6. Editable preview
7. TXT/DOCX/PDF export
8. Testing
9. Deployment

---

## Disclaimer

LegalEase is a software project demonstrating Generative AI-assisted
document drafting.

It does not provide legal advice, legal representation, or a guarantee
that generated documents are legally valid or enforceable.

Always review generated material with an appropriately qualified legal
professional before relying on it.

---

## Author

Akison-Shan-R

GitHub:

[https://github.com/akisonshan](https://github.com/akisonshan)
