# 🏥 AI Insurance - Intelligent Policy Analysis System

An AI-powered insurance policy analysis system that uses **Ollama** for intelligent question answering and **ChromaDB** for semantic search across policy documents.

## 🎯 Overview

AI Insurance allows users to upload insurance policy documents and ask natural language questions about coverage, limits, exclusions, and conditions. The system uses:

- **Vector Search** - Find relevant policy clauses instantly
- **LLM Generation** - Ollama generates contextual, structured answers
- **Multi-Answer Types** - Coverage checks, limit lookups, exclusion queries, and more
- **Semantic Understanding** - Classifies questions into 20+ categories for targeted responses

## 🚀 Quick Start

### Prerequisites

- **Python 3.9+**
- **Node.js 18+**
- **Ollama** (running on localhost:11434)
- **Model**: mistral (or neural-chat, llama2, etc.)

### 1. Install Ollama & Model (First Time Only)

```bash
# Install Ollama
brew install ollama

# Download model (choose one)
ollama pull mistral          # Recommended (4GB, balanced)
# OR
ollama pull neural-chat      # Faster (4GB, speed-focused)
```

### 2. Start Ollama (Keep Running!)

```bash
ollama serve
# This must stay running while you use the system
```

### 3. Setup Backend (New Terminal)

```bash
cd /Users/ankit/ai-insurance

# Create virtual environment (first time)
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Start backend
python -m uvicorn app.main:app --reload --port 8888
```

### 4. Setup Frontend (Another Terminal)

```bash
cd /Users/ankit/ai-insurance/frontend

# Install dependencies (first time)
npm install

# Start development server
npm run dev
```

### 5. Use the System

Open **http://localhost:3000** in your browser:

1. Click "Upload Policy"
2. Select a PDF document
3. Ask a question: "Is cancer covered?" or "What's my deductible?"
4. Get AI-generated answer in 5-15 seconds

---

## 📋 System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Frontend (React + TypeScript)             │
│                   Running on localhost:3000                  │
└─────────────────────────────────────────────────────────────┘
                             ↓
                    REST API Calls
                             ↓
┌─────────────────────────────────────────────────────────────┐
│                 Backend (FastAPI + Python)                   │
│                   Running on localhost:8888                  │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Query Processing Pipeline                           │   │
│  │  1. Upload PDF → Chunk & Embed                      │   │
│  │  2. Classify question (20 categories)               │   │
│  │  3. Vector search relevant clauses                  │   │
│  │  4. Call Ollama LLM                                 │   │
│  │  5. Return structured answer                        │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                             ↓
        ┌────────────────────┴────────────────────┐
        ↓                                          ↓
   ┌─────────────┐                        ┌──────────────┐
   │  ChromaDB   │                        │  Ollama LLM  │
   │ (Vectors)   │                        │ (Generation) │
   └─────────────┘                        └──────────────┘
        ↓                                        ↓
   Policy Clauses                         AI Answers
```

### Key Components

**Backend** (`/app`):
- `main.py` - FastAPI application entry point
- `api/routers/` - API endpoints (upload, ask, chat)
- `services/` - Business logic (RAG, Ollama, embeddings)
- `infrastructure/` - Vector store, PDF parsing, embeddings
- `core/` - Configuration and setup

**Frontend** (`/frontend`):
- React 18 + TypeScript
- Vite for fast development
- TailwindCSS for styling
- Recharts for visualizations

**Database**:
- ChromaDB for vector storage (persisted in `/chroma`)

---

## 🎯 Features

### Answer Types

The system can answer questions about:

| Type | Examples | Response |
|------|----------|----------|
| **Coverage** | "Is cancer covered?" | Verdict + summary + next steps |
| **Limits** | "What's my deductible?" | Specific limits + breakdown |
| **Exclusions** | "Is cosmetic surgery covered?" | Excluded items + warnings |
| **Conditions** | "When does coverage apply?" | Conditions + requirements |
| **Requirements** | "What do I need to do?" | Step-by-step requirements |
| **Financial** | "What's the out-of-pocket max?" | Financial details |
| **Ambiguity** | Unclear questions | Clarification needed |

### Query Classification

Questions are classified into 20+ categories:
- Coverage checks
- Benefit lookups
- Limit inquiries
- Exclusion queries
- Provider questions
- Claims process
- Requirements
- Conditions
- Financial details
- And more...

### Semantic Search

- **Embeddings**: SentenceTransformer (all-MiniLM-L6-v2)
- **Matching**: Semantic search finds relevant clauses
- **Ranking**: Top clauses sent to Ollama for generation

---

## 📁 Project Structure

```
ai-insurance/
├── app/                           # Backend
│   ├── main.py                   # FastAPI app
│   ├── api/
│   │   └── routers/
│   │       ├── claims.py         # Claims endpoints
│   │       ├── ingestion.py      # Upload endpoints
│   │       └── policy.py         # Policy endpoints
│   ├── services/
│   │   ├── rag_service.py        # RAG pipeline
│   │   ├── ollama_enhancer.py    # Ollama integration
│   │   ├── fraud_service.py      # Fraud detection
│   │   └── vector_service.py     # Vector operations
│   ├── infrastructure/
│   │   ├── pdf_parser.py         # PDF parsing
│   │   ├── embeddings.py         # Embeddings
│   │   └── vector_store.py       # ChromaDB
│   ├── schemas/
│   │   ├── claim.py              # Claim models
│   │   └── policy.py             # Policy models
│   ├── ml/
│   │   └── fraud_model.py        # ML models
│   └── core/
│       └── config.py             # Configuration
├── frontend/                      # React app
│   ├── src/
│   │   ├── components/           # React components
│   │   ├── services/             # API services
│   │   ├── pages/                # Pages
│   │   └── App.tsx               # Main app
│   └── package.json              # Dependencies
├── chroma/                        # ChromaDB storage
├── data/                          # Sample data
├── tests/                         # Unit tests
├── requirements.txt               # Python dependencies
└── README.md                      # This file
```

---

## ⚙️ Configuration

### Backend Configuration

Edit `app/core/config.py`:

```python
# Vector embeddings
EMBEDDINGS_MODEL = "all-MiniLM-L6-v2"

# ChromaDB
CHROMA_PATH = "./chroma"

# Ollama
OLLAMA_BASE_URL = "http://localhost:11434"
OLLAMA_MODEL = "mistral"  # Change model here
OLLAMA_TIMEOUT = 30
```

### Ollama Model Selection

```bash
# Faster (good for testing)
ollama pull neural-chat

# Balanced (recommended)
ollama pull mistral

# More accurate (slower)
ollama pull llama2

# High quality (large, 27GB)
ollama pull dolphin-mixtral
```

Change model in code:
```python
# app/services/ollama_enhancer.py
OLLAMA_MODEL = "neural-chat"  # or any model you prefer
```

---

## 🔧 API Endpoints

### Core Endpoints

**Upload Policy**
```bash
curl -X POST http://localhost:8888/upload \
  -F "file=@policy.pdf"
```

**Ask Question**
```bash
curl -X POST http://localhost:8888/ask \
  -H "Content-Type: application/json" \
  -d '{"question": "Is cancer covered?"}'
```

**Health Check**
```bash
curl http://localhost:8888/health
```

**List Documents**
```bash
curl http://localhost:8888/documents
```

---

## 📊 Example Usage

### Example 1: Coverage Check

```
Input:  "Is cardiology covered?"
Output: {
  "verdict": "Yes, cardiology is covered",
  "is_covered": true,
  "summary": "Your plan includes specialist consultation for cardiology services...",
  "key_points": [
    "Requires referral from primary care physician",
    "In-network copay: $50",
    "Out-of-network copay: $100 after deductible"
  ],
  "next_step": "Schedule with in-network cardiologist"
}
```

### Example 2: Limits Inquiry

```
Input:  "What's my annual deductible?"
Output: {
  "verdict": "$500 per person, $1000 family",
  "message": "Deductible applies to out-of-network services only",
  "limits_breakdown": [
    { "category": "Individual", "amount": "$500" },
    { "category": "Family", "amount": "$1000" },
    { "category": "Out-of-Pocket Max", "amount": "$3000" }
  ]
}
```

### Example 3: Exclusion Check

```
Input:  "Is cosmetic surgery covered?"
Output: {
  "verdict": "No, cosmetic surgery is not covered",
  "warning": "Cosmetic procedures are explicitly excluded",
  "excluded_items": [
    "Cosmetic surgery",
    "Elective procedures",
    "Non-medically necessary treatments"
  ]
}
```

---

## 🚨 Troubleshooting

### Problem: "Ollama is not available"

```bash
# Check if Ollama running
curl http://localhost:11434/api/tags

# If error, start Ollama (in new terminal)
ollama serve
```

### Problem: "Model not found"

```bash
# List available models
ollama list

# Download model
ollama pull mistral

# Or switch to downloaded model
ollama pull neural-chat
```

### Problem: Slow responses (>30s)

```bash
# Try faster model
ollama pull neural-chat

# Or check CPU usage
top -o %CPU

# Reduce timeout in app/services/ollama_enhancer.py
OLLAMA_TIMEOUT = 20  # Lower from 30
```

### Problem: Upload fails

```bash
# Check backend health
curl http://localhost:8888/health

# Check PDF is valid
file policy.pdf  # Should show "PDF document"

# Check ChromaDB directory exists
ls -la chroma/
```

### Problem: Frontend won't start

```bash
# Clear dependencies
rm -rf frontend/node_modules frontend/package-lock.json

# Reinstall
cd frontend && npm install && npm run dev
```

---

## 🧪 Development

### Running Tests

```bash
# Run all tests
pytest tests/

# Run specific test file
pytest tests/test_rag_service.py -v

# With coverage
pytest tests/ --cov=app --cov-report=html
```

### Backend Development

```bash
# Start with auto-reload
python -m uvicorn app.main:app --reload --port 8888

# Debug mode
PYTHONUNBUFFERED=1 python -m uvicorn app.main:app --reload
```

### Frontend Development

```bash
# Start dev server (auto-refresh)
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

---

## 📦 Dependencies

### Backend
- `fastapi` - Web framework
- `uvicorn` - ASGI server
- `pydantic` - Data validation
- `chromadb` - Vector database
- `sentence-transformers` - Embeddings
- `PyMuPDF` - PDF parsing
- `scikit-learn` - ML utilities
- `requests` - HTTP client
- `python-multipart` - File uploads

### Frontend
- `react` - UI library
- `typescript` - Type safety
- `vite` - Build tool
- `tailwindcss` - Styling
- `recharts` - Charts
- `axios` - HTTP client

---

## 🔐 Security Considerations

1. **API Security**: Add authentication for production
2. **File Validation**: Only accept PDFs from trusted sources
3. **Rate Limiting**: Implement per-user rate limits
4. **Data Privacy**: Store documents securely
5. **Ollama Access**: Keep Ollama local or behind firewall

---

## 🚀 Production Deployment

### Backend (using Docker)

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY app/ ./app
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8888"]
```

### Frontend (using Vercel/Netlify)

```bash
# Build
npm run build

# Deploy dist/ folder
```

### Ollama (Docker)

```bash
docker run -d -p 11434:11434 --name ollama ollama/ollama
docker exec ollama ollama pull mistral
```

---

## 📚 Documentation

- `OLLAMA_CENTERED_COMPLETE.md` - Detailed architecture documentation
- `OLLAMA_CENTERED_QUICKSTART.md` - Quick reference guide
- API docs: http://localhost:8888/docs (Swagger UI)

---

## 🤝 Contributing

1. Create a feature branch: `git checkout -b feature/new-feature`
2. Make changes and test: `pytest tests/`
3. Commit: `git commit -am "Add new feature"`
4. Push: `git push origin feature/new-feature`
5. Create Pull Request

---

## 📝 License

This project is licensed under the MIT License - see LICENSE file for details.

---

## 🙋 Support

For issues or questions:

1. Check `OLLAMA_CENTERED_QUICKSTART.md` for quick fixes
2. Review API documentation at http://localhost:8888/docs
3. Check existing issues on GitHub
4. Create a new issue with detailed error message

---

## 🎉 Getting Started Now

```bash
# 1. Start Ollama
ollama serve

# 2. Start backend (new terminal)
cd /Users/ankit/ai-insurance
source .venv/bin/activate
python -m uvicorn app.main:app --reload --port 8888

# 3. Start frontend (another terminal)
cd /Users/ankit/ai-insurance/frontend
npm run dev

# 4. Open browser
open http://localhost:3000

# 5. Upload a policy and ask questions!
```

**Total time to start: ~2 minutes** ⚡

---

**Built with ❤️ for intelligent insurance analysis**
