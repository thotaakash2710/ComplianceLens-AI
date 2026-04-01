# ComplianceLens AI

An AI-powered pipeline that audits YouTube videos for compliance violations — automatically detecting misleading claims, policy breaches, and content issues using Azure AI and LangGraph.

## How It Works

```
YouTube URL → Download → Azure Video Indexer (speech-to-text + OCR) → Azure AI Search → GPT-4 Auditor → Compliance Report
```

The LangGraph workflow runs two nodes in sequence:
1. **Indexer** — downloads the video, uploads to Azure Video Indexer, extracts transcript and insights
2. **Auditor** — retrieves relevant compliance rules from Azure AI Search and uses GPT-4 to detect violations

## Setup

**Prerequisites:** Python 3.12+, [uv](https://github.com/astral-sh/uv)

```bash
# Install dependencies
uv sync

# Configure environment
cp .env.example .env   # fill in your Azure keys
```

### Required `.env` Variables

| Variable | Description |
|---|---|
| `AZURE_STORAGE_CONNECTION_STRING` | Azure Blob Storage connection |
| `AZURE_OPENAI_API_KEY` | Azure OpenAI key |
| `AZURE_OPENAI_ENDPOINT` | Azure OpenAI endpoint |
| `AZURE_SEARCH_ENDPOINT` | Azure AI Search endpoint |
| `AZURE_SEARCH_KEY` | Azure AI Search admin key |
| `LANGSMITH_API_KEY` | LangSmith tracing (optional) |

## Usage

```bash
python main.py
```

Edit the `video_url` in `main.py` to audit any YouTube video.

**Sample output:**
```
=== COMPLIANCE AUDIT REPORT ===
Video ID:    vid_ce6c43bb
Status:      FAIL

[ VIOLATIONS DETECTED ]
- [CRITICAL] Misleading Claims: Absolute guarantee detected

[ FINAL SUMMARY ]
Video contains 1 critical violation related to unsubstantiated claims.
```

## Project Structure

```
ComplianceQAPipeline/
├── main.py                    # Entry point / CLI runner
├── backend/
│   ├── src/
│   │   ├── graph/
│   │   │   ├── workflow.py    # LangGraph DAG definition
│   │   │   ├── nodes.py       # Indexer + Auditor node logic
│   │   │   └── state.py       # Shared workflow state schema
│   │   └── services/
│   │       └── video_indexer.py  # Azure Video Indexer integration
│   └── scripts/
│       └── index_documents.py    # Upload compliance rules to Azure Search
└── azure_functions/           # Azure Function App (serverless trigger)
```

## Tech Stack

- **Orchestration:** LangGraph
- **LLM:** Azure OpenAI (GPT-4)
- **Video Processing:** Azure Video Indexer + yt-dlp
- **Vector Search:** Azure AI Search
- **Observability:** LangSmith
