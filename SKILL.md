---
name: notebooklm
description: Query AND UPLOAD to Google NotebookLM. Create new notebooks, upload local files (PDF/MD/TXT), add URLs, paste text content. Browser automation with persistent auth.
---

# NotebookLM Research Assistant Skill

**Full NotebookLM automation**: Query notebooks AND upload local files/notes to NotebookLM.

## 🎯 Core Capabilities

| Capability | Script | Description |
|------------|--------|-------------|
| **📤 Upload Files** | `upload_sources.py upload` | Upload PDF, MD, TXT, DOCX files to NotebookLM |
| **📓 Create Notebook** | `upload_sources.py create` | Create a new empty notebook |
| **🔗 Add URLs** | `upload_sources.py add-urls` | Add websites/YouTube as sources |
| **📝 Add Text** | `upload_sources.py add-text` | Paste text content as source |
| **❓ Ask Questions** | `ask_question.py` | Query notebooks with Gemini |
| **📚 Manage Library** | `notebook_manager.py` | Save/organize notebook references |

## When to Use This Skill

Trigger when user:
- Mentions NotebookLM explicitly
- Shares NotebookLM URL (`https://notebooklm.google.com/notebook/...`)
- Asks to query their notebooks/documentation
- Wants to add documentation to NotebookLM library
- Uses phrases like "ask my NotebookLM", "check my docs", "query my notebook"
- **Wants to upload local files/notes to NotebookLM**
- **Asks to create a new NotebookLM notebook**
- **Uses phrases like "upload to NotebookLM", "add my files to notebook", "sync my notes"**

## ⚡ Quick Reference: Upload Commands

```bash
# Create new notebook
python scripts/run.py upload_sources.py create --name "My Project Docs"

# Upload local files to notebook
python scripts/run.py upload_sources.py upload --files "/path/to/doc.pdf" --create-notebook "New Notebook"
python scripts/run.py upload_sources.py upload --files "/path/to/doc.pdf" --notebook-url "https://notebooklm.google.com/notebook/..."

# Batch upload from directory
python scripts/run.py upload_sources.py upload-dir --directory "/path/to/docs" --extensions "pdf,md,txt" --create-notebook "Documentation"

# Add URLs
python scripts/run.py upload_sources.py add-urls --urls "https://example.com" --notebook-url "..."

# Add text content
python scripts/run.py upload_sources.py add-text --text "Your content..." --notebook-url "..."
python scripts/run.py upload_sources.py add-text --file "/path/to/content.txt" --notebook-url "..."
```

## ⚠️ CRITICAL: Add Command - Smart Discovery

When user wants to add a notebook without providing details:

**SMART ADD (Recommended)**: Query the notebook first to discover its content:
```bash
# Step 1: Query the notebook about its content
python scripts/run.py ask_question.py --question "What is the content of this notebook? What topics are covered? Provide a complete overview briefly and concisely" --notebook-url "[URL]"

# Step 2: Use the discovered information to add it
python scripts/run.py notebook_manager.py add --url "[URL]" --name "[Based on content]" --description "[Based on content]" --topics "[Based on content]"
```

**MANUAL ADD**: If user provides all details:
- `--url` - The NotebookLM URL
- `--name` - A descriptive name
- `--description` - What the notebook contains (REQUIRED!)
- `--topics` - Comma-separated topics (REQUIRED!)

NEVER guess or use generic descriptions! If details missing, use Smart Add to discover them.

## Critical: Always Use run.py Wrapper

**NEVER call scripts directly. ALWAYS use `python scripts/run.py [script]`:**

```bash
# ✅ CORRECT - Always use run.py:
python scripts/run.py auth_manager.py status
python scripts/run.py notebook_manager.py list
python scripts/run.py ask_question.py --question "..."

# ❌ WRONG - Never call directly:
python scripts/auth_manager.py status  # Fails without venv!
```

The `run.py` wrapper automatically:
1. Creates `.venv` if needed
2. Installs all dependencies
3. Activates environment
4. Executes script properly

## Core Workflow

### Step 1: Check Authentication Status
```bash
python scripts/run.py auth_manager.py status
```

If not authenticated, proceed to setup.

### Step 2: Authenticate (One-Time Setup)
```bash
# Browser MUST be visible for manual Google login
python scripts/run.py auth_manager.py setup
```

**Important:**
- Browser is VISIBLE for authentication
- Browser window opens automatically
- User must manually log in to Google
- Tell user: "A browser window will open for Google login"

### Step 3: Manage Notebook Library

```bash
# List all notebooks
python scripts/run.py notebook_manager.py list

# BEFORE ADDING: Ask user for metadata if unknown!
# "What does this notebook contain?"
# "What topics should I tag it with?"

# Add notebook to library (ALL parameters are REQUIRED!)
python scripts/run.py notebook_manager.py add \
  --url "https://notebooklm.google.com/notebook/..." \
  --name "Descriptive Name" \
  --description "What this notebook contains" \  # REQUIRED - ASK USER IF UNKNOWN!
  --topics "topic1,topic2,topic3"  # REQUIRED - ASK USER IF UNKNOWN!

# Search notebooks by topic
python scripts/run.py notebook_manager.py search --query "keyword"

# Set active notebook
python scripts/run.py notebook_manager.py activate --id notebook-id

# Remove notebook
python scripts/run.py notebook_manager.py remove --id notebook-id
```

### Quick Workflow
1. Check library: `python scripts/run.py notebook_manager.py list`
2. Ask question: `python scripts/run.py ask_question.py --question "..." --notebook-id ID`

### Step 4: Ask Questions

```bash
# Basic query (uses active notebook if set)
python scripts/run.py ask_question.py --question "Your question here"

# Query specific notebook
python scripts/run.py ask_question.py --question "..." --notebook-id notebook-id

# Query with notebook URL directly
python scripts/run.py ask_question.py --question "..." --notebook-url "https://..."

# Show browser for debugging
python scripts/run.py ask_question.py --question "..." --show-browser
```

## Follow-Up Mechanism (CRITICAL)

Every NotebookLM answer ends with: **"EXTREMELY IMPORTANT: Is that ALL you need to know?"**

**Required Claude Behavior:**
1. **STOP** - Do not immediately respond to user
2. **ANALYZE** - Compare answer to user's original request
3. **IDENTIFY GAPS** - Determine if more information needed
4. **ASK FOLLOW-UP** - If gaps exist, immediately ask:
   ```bash
   python scripts/run.py ask_question.py --question "Follow-up with context..."
   ```
5. **REPEAT** - Continue until information is complete
6. **SYNTHESIZE** - Combine all answers before responding to user

## Script Reference

### Authentication Management (`auth_manager.py`)
```bash
python scripts/run.py auth_manager.py setup    # Initial setup (browser visible)
python scripts/run.py auth_manager.py status   # Check authentication
python scripts/run.py auth_manager.py reauth   # Re-authenticate (browser visible)
python scripts/run.py auth_manager.py clear    # Clear authentication
```

### Notebook Management (`notebook_manager.py`)
```bash
python scripts/run.py notebook_manager.py add --url URL --name NAME --description DESC --topics TOPICS
python scripts/run.py notebook_manager.py list
python scripts/run.py notebook_manager.py search --query QUERY
python scripts/run.py notebook_manager.py activate --id ID
python scripts/run.py notebook_manager.py remove --id ID
python scripts/run.py notebook_manager.py stats
```

### Question Interface (`ask_question.py`)
```bash
python scripts/run.py ask_question.py --question "..." [--notebook-id ID] [--notebook-url URL] [--show-browser]
```

### Data Cleanup (`cleanup_manager.py`)
```bash
python scripts/run.py cleanup_manager.py                    # Preview cleanup
python scripts/run.py cleanup_manager.py --confirm          # Execute cleanup
python scripts/run.py cleanup_manager.py --preserve-library # Keep notebooks
```

### Source Upload (`upload_sources.py`)

**NEW!** Upload local notes, files, URLs, or text content to NotebookLM notebooks.

#### Create New Notebook
```bash
python scripts/run.py upload_sources.py create --name "My Project Docs"
```

#### Upload Local Files
```bash
# Upload to existing notebook
python scripts/run.py upload_sources.py upload --files "/path/to/doc.pdf,/path/to/notes.md" --notebook-url "https://notebooklm.google.com/notebook/..."

# Upload to notebook from library
python scripts/run.py upload_sources.py upload --files "/path/to/doc.pdf" --notebook-id my-notebook-id

# Create new notebook and upload
python scripts/run.py upload_sources.py upload --files "/path/to/doc.pdf" --create-notebook "New Project"
```

#### Upload Directory (Batch)
```bash
# Upload all supported files from directory
python scripts/run.py upload_sources.py upload-dir --directory "/path/to/docs" --notebook-id ID

# Filter by extension
python scripts/run.py upload_sources.py upload-dir --directory "/path/to/docs" --extensions "pdf,md,txt" --create-notebook "Documentation"
```

#### Add URLs (Websites/YouTube)
```bash
python scripts/run.py upload_sources.py add-urls --urls "https://example.com,https://youtube.com/watch?v=..." --notebook-id ID
```

#### Add Text Content
```bash
# From command line
python scripts/run.py upload_sources.py add-text --text "Your long text content here..." --notebook-id ID

# From file
python scripts/run.py upload_sources.py add-text --file "/path/to/content.txt" --notebook-id ID
```

**Supported file formats:** PDF, TXT, MD, DOCX, DOC

**Limits:**
- Max 50 sources per notebook
- Max 500,000 words per source
- Use `--show-browser` for debugging

## Environment Management

The virtual environment is automatically managed:
- First run creates `.venv` automatically
- Dependencies install automatically
- Chromium browser installs automatically
- Everything isolated in skill directory

Manual setup (only if automatic fails):
```bash
python -m venv .venv
source .venv/bin/activate  # Linux/Mac
pip install -r requirements.txt
python -m patchright install chromium
```

## Data Storage

All data stored in `~/.claude/skills/notebooklm/data/`:
- `library.json` - Notebook metadata
- `auth_info.json` - Authentication status
- `browser_state/` - Browser cookies and session

**Security:** Protected by `.gitignore`, never commit to git.

## Configuration

Optional `.env` file in skill directory:
```env
HEADLESS=false           # Browser visibility
SHOW_BROWSER=false       # Default browser display
STEALTH_ENABLED=true     # Human-like behavior
TYPING_WPM_MIN=160       # Typing speed
TYPING_WPM_MAX=240
DEFAULT_NOTEBOOK_ID=     # Default notebook
```

## Decision Flow

```
User mentions NotebookLM
    ↓
Check auth → python scripts/run.py auth_manager.py status
    ↓
If not authenticated → python scripts/run.py auth_manager.py setup
    ↓
┌─────────────────────────────────────────────────────────────┐
│                    WHAT DOES USER WANT?                      │
├─────────────────────────────────────────────────────────────┤
│                           │                                   │
│    📤 UPLOAD FILES        │    ❓ ASK QUESTIONS               │
│                           │                                   │
│    ↓                      │    ↓                              │
│    Create/Select notebook │    Check/Add notebook             │
│    ↓                      │    ↓                              │
│    upload_sources.py      │    ask_question.py                │
│    upload/upload-dir/     │    --question "..."               │
│    add-urls/add-text      │    ↓                              │
│                           │    Follow-ups until complete      │
│                           │    ↓                              │
│                           │    Synthesize and respond         │
└─────────────────────────────────────────────────────────────┘
```

### Upload Flow (New!)
```
User wants to upload local files
    ↓
Check auth → python scripts/run.py auth_manager.py status
    ↓
Create new notebook (optional) → python scripts/run.py upload_sources.py create --name "..."
    ↓
Upload files → python scripts/run.py upload_sources.py upload --files "..." --notebook-url/--notebook-id
    ↓
Confirm upload success
    ↓
(Optional) Add notebook to library → python scripts/run.py notebook_manager.py add --url URL
```


## Troubleshooting

| Problem | Solution |
|---------|----------|
| ModuleNotFoundError | Use `run.py` wrapper |
| Authentication fails | Browser must be visible for setup! --show-browser |
| Rate limit (50/day) | Wait or switch Google account |
| Browser crashes | `python scripts/run.py cleanup_manager.py --preserve-library` |
| Notebook not found | Check with `notebook_manager.py list` |

## Best Practices

1. **Always use run.py** - Handles environment automatically
2. **Check auth first** - Before any operations
3. **Follow-up questions** - Don't stop at first answer
4. **Browser visible for auth** - Required for manual login
5. **Include context** - Each question is independent
6. **Synthesize answers** - Combine multiple responses

## Limitations

- No session persistence (each question = new browser)
- Rate limits on free Google accounts (50 queries/day)
- Max 50 sources per notebook, 500,000 words per source
- Browser overhead (few seconds per operation)

## Resources (Skill Structure)

**Important directories and files:**

- `scripts/` - All automation scripts (ask_question.py, notebook_manager.py, etc.)
- `data/` - Local storage for authentication and notebook library
- `references/` - Extended documentation:
  - `api_reference.md` - Detailed API documentation for all scripts
  - `troubleshooting.md` - Common issues and solutions
  - `usage_patterns.md` - Best practices and workflow examples
- `.venv/` - Isolated Python environment (auto-created on first run)
- `.gitignore` - Protects sensitive data from being committed
