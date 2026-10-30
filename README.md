```text
_____ _       _   _      _____         _ _          
 / ____| |     | | | |    / ____|       (_| |         
| (___ | | ___ | |_| |__ | (___  _   _   _| |_ ___    
 \___ \| |/ _ \| __| '_ \ \___ \| | | | | | __/ _ \   
 ____) | | (_) | |_| | | |____) | |_| | | | ||  __/   
|_____/|_|\___/ \__|_| |_|_____/ \__,_| |_|\__\___|   
                                 __/ |                
 [ 🦥 Slow & Steady • 100% Local AI • Zero Cloud ]   |___/
```

# 🦥 SlothSuite (`Git_slothNtidy`)

### Zero-Friction, Privacy-First Local AI Document Management & Git Automation Suite

*Built for local-first developers, students, and privacy advocates.*

> ## 🏆 Dev Challenges — Week 2 Submission
>
> This project was submitted as the **Week 2 entry for Dev Challenges**.
>
> ## ⚠️ Post-Week-2 Update
>
> **Everything below the original Week 2 scope reflects changes made AFTER the Week 2 submission.** The notes-sorter pipeline, the Streamlit GUI, local MiniLM embeddings, the filing stage, the test suite, and the installer scripts were all added in the post-Week-2 iteration — the Week 2 version was the PDF-deduplication + Git-automation core only.

---

## 🌿 The Sloth Ethos

Sloths survive by conserving energy. You shouldn't waste yours manually renaming messy downloads, figuring out which PDF version has the extra page, or writing repetitive Conventional Commit messages.

**SlothSuite** is a fully local AI toolkit. It does not just move files — it **deduplicates PDFs, diffs their contents, reads WhatsApp-classnote chaos, maps notes against your syllabus, and automates your Git workflows offline**. No cloud calls, no API keys, no telemetry.

* 🔒 **100% Local Privacy:** Class notes, resumes, private source code — nothing ever leaves your machine. Zero telemetry, zero cloud.
* 💸 **Zero API Costs:** Local Ollama for LLM tasks + a small CPU-only MiniLM embedding model. No rate limits.
* 🧠 **Structured AI Contracts:** JSON schema enforcement turns unstructured documents and raw diffs into clean, actionable data.

---

## 🆕 What Changed After Week 2

The Week 2 submission shipped the two original pipelines (`Tidy.py` + `sloth.py`). The post-Week-2 iteration adds a full third product on top — **slothNtidy, the Notes Sorter** — plus a GUI, a real test suite, and one-click installers:

| Area | Week 2 | After Week 2 |
| --- | --- | --- |
| Interface | CLI only | **Streamlit web GUI** (`app.py`) with a 3-step flow: Syllabus → Import chats → Sort |
| Matching | Ollama LLM only | **Local MiniLM sentence embeddings** (`sentence-transformers`, CPU) for syllabus-vs-note matching |
| Ingestion | PDFs in a folder | **WhatsApp chat exports + photo/scan OCR** (Tesseract), with a syllabus-from-photo reader |
| Filtering | Text-similarity dedup | **SHA-256 exact dupes + pHash near-dupes + circular/notice noise filter** |
| Output | Categorized vault | **Syllabus-mapped topic PDFs** (`OS_U2_Deadlocks_Rohan_21Oct.pdf` style) + `COVERAGE.md` report |
| Filing | `SlothVault/{Category}/` | **Stage 6.5 (`filing.py`)**: sorts output into `SlothVault/<Subject>/` and rebuilds `SLOTH_INDEX.md` + `sloth_memory.json` |
| Review | — | **Needs-Review queue** in the GUI: assign a note to a unit, restore it, or mark it not-in-syllabus — with page-level evidence for every coverage claim |
| Install | Manual pip | **`setup.bat` / `Install-slothNtidy.bat` (Windows) + `run.sh` (Linux/macOS)** |
| Tests | — | **25/25 pytest tests passing** (`tests/`) |
| Demo data | — | Full OS-themed demo inbox for one-command live demos |

> 📦 The GUI-heavy post-Week-2 build is also mirrored at **[adeeb-builds/slothNtidy](https://github.com/adeeb-builds/slothNtidy)** (Streamlit front-end, per-subtopic coverage dashboard, demo inbox). This repo remains the source of truth for the full suite.

---

## 🏗️ Architectural Flow & Pipelines

SlothSuite now operates through **three** interconnected, offline pipelines.

### Pipeline A: Smart PDF Deduplication & Vault Engine (`Tidy.py`) — *original, Week 2*

```text
 [ ./Downloaded_PDFs ] 
         │
         ▼
 🔍 Step 1: Pre-Scan & Metadata Extraction (PyMuPDF)
         │
         ▼
 🧠 Step 2: Cross-File Similarity Match (difflib SequenceMatcher)
         ├── [ Unique File ] ──► Send to LLM for Categorization & Key Points
         └── [ Duplicate Match ] ──► Compare Character Counts & Page Lengths
                 │
                 ▼
 🤖 Step 3: Local LLM Diff Analysis (qwen2.5:7b via Ollama)
         │
         ▼
 👤 Step 4: Interactive User Permission ([y/N] prompt)
         ├── [ Approved ] ──► Physically delete inferior file from disk
         └── [ Rejected ] ──► Keep both files safe
         │
         ▼
 📂 Step 5: Atomic Move to [ ./SlothVault/{Category}/ ] & Live Markdown Update
```

### Pipeline B: Offline Git Automation Agent (`sloth.py`) — *original, Week 2*

```text
 [ Staged Git Changes (git add -A) ]
         │
         ▼
 🔍 Step 1: Read Local Diff & Stat Summary (--cached --stat)
         │
         ▼
 🧠 Step 2: Zero-Cloud Local Inference (engine.py -> Ollama)
         │
         ▼
 🤖 Step 3: LLM Parses Diff into Structured JSON Packet
         ├── Conventional Commit Message (e.g., refactor(tidy): ...)
         ├── CHANGELOG.md Bullet Points
         └── Custom "Lazy Score" Evaluation
         │
         ▼
 👤 Step 4: Developer Review & Approval [y/N]
         │
         ▼
 🚀 Step 5: Local Commit, CHANGELOG.md Append & Automated GitHub Push
```

### Pipeline C: slothNtidy — Notes Sorter (`pipeline.py` + `app.py`) — *added after Week 2*

```text
 [ WhatsApp chat exports / photos / PDFs in INBOX ]
         │
         ▼
 1️⃣ Parse & OCR       parser.py — chat exports, PDFs, photos (Tesseract)
         │              · handles WhatsApp's invisible U+200E character bug
         ▼
 2️⃣ Filter Noise      filters.py — SHA-256 exact dupes → pHash near-dupes
         │              · drops circulars & notice spam before any AI runs
         ▼
 3️⃣ Embed & Match     embedder.py + matcher.py — local MiniLM embeddings
         │              · maps each note to syllabus units/subtopics (with confidence)
         ▼
 4️⃣ Coverage Report   report.py — per-subtopic coverage with PAGE EVIDENCE
         │              · writes COVERAGE.md + report.json, flags gaps & off-topic
         ▼
 5️⃣ Export            exporter.py — one merged PDF per syllabus unit
         │              · clean names: OS_U2_Deadlocks_Rohan_21Oct.pdf
         ▼
 6️⃣ + 6.5 File Away   filing.py — sorts output into SlothVault/<Subject>/,
         │              · rebuilds SLOTH_INDEX.md and sloth_memory.json
         ▼
 🖥️ Streamlit GUI (app.py) — Syllabus → Import chats → Sort,
     coverage dashboard, cluster inspector, Needs-Review queue
```

---

## ⭐ Spotlight Feature: Git Sloth (`sloth.py`) — Why It's a Game-Changer

1. **Zero Cloud Risk:** Unlike online GitHub Actions or third-party AI extensions that inspect your proprietary codebase in the cloud, Git Sloth runs entirely on your machine. It reads your local `git diff` and pipes it straight into your local Ollama instance.
2. **Context-Aware Conventional Commits:** It doesn't just look at file names; it reads the *actual lines added and removed*. If you refactored a deduplication loop, it writes `refactor(tidy): optimize SequenceMatcher threshold and batch processing`.
3. **Automated Changelog Maintenance:** It automatically formats a clean markdown entry in your `CHANGELOG.md` with structured bullet points detailing what changed under today's date.
4. **Instant Productivity Metrics:** It evaluates your commit size and prints a fun "Lazy Score" highlighting how many lines of typing you just avoided.

---

## 🚀 Quickstart & Installation

### 1. One-Click Setup *(added after Week 2)*

```powershell
# Windows — installs Python deps, Tesseract OCR, and the MiniLM model
setup.bat          # (or docs/Install-slothNtidy.bat)

# Then run the app
run.bat
```

```bash
# Linux / macOS
./run.sh
```

### 2. Manual Setup

Ensure you have **Python 3.11+** and, for Pipelines A & B, **Ollama** running locally:

```bash
git clone https://github.com/akshat-bit420/Git_slothNtidy.git
cd Git_slothNtidy

# Create virtual environment
python -m venv .venv
source .venv/Scripts/Activate.ps1   # On Windows PowerShell
# source .venv/bin/activate         # On Linux/macOS

# Install dependencies
pip install -r requirements.txt

# For the original LLM pipelines
ollama pull qwen2.5:7b

# For OCR of photos/scans (Notes Sorter)
#   Windows: install Tesseract, or let setup.bat do it
#   Linux:   sudo apt install tesseract-ocr
```

---

## 🖥️ Usage Guide

### 1. slothNtidy Notes Sorter GUI *(added after Week 2)*

```bash
streamlit run app.py
```

1. **Syllabus** — paste/upload your syllabus (JSON, text, or even a **photo of the syllabus sheet** — the GUI reads it).
2. **Import chats** — drop WhatsApp chat exports, PDFs, or photos into the inbox (or load the built-in **demo inbox**).
3. **Sort** — one click runs the full 6-stage pipeline. You get:
   * a **coverage dashboard** with per-subtopic coverage and page-level evidence,
   * a **Needs-Review queue** (assign a note to a unit, restore it, or mark it not-in-syllabus),
   * a **cluster inspector** and noise-reduction view,
   * merged topic-wise PDFs filed into `SlothVault/<Subject>/` with an updated `SLOTH_INDEX.md`.

Prefer the terminal? `python main.py` runs the same pipeline headlessly.

### 2. Smart PDF Deduplication & Tidying (`Tidy.py`) — *original*

1. Place your raw or duplicate PDFs into the **`./Downloaded_PDFs`** folder.
2. Run: `python Tidy.py`
3. The engine pre-scans all files, detects overlapping content, generates a content diff, asks for your confirmation (`[y/N]`), safely deletes inferior copies, and organizes survivors into **`./SlothVault`** with an auto-updated **`SLOTH_INDEX.md`**.

### 3. Offline Git Automation Agent (`sloth.py`) — *original*

1. Stage your changes: `git add -A`
2. Run: `python sloth.py`
3. Review the AI-generated Conventional Commit message, changelog items, and lazy score. Press **`y`** to commit, append your changelog, and push!

---

## 🧪 Tests *(added after Week 2)*

```bash
python -m pytest tests/ -q
```

**25/25 passing** — covers parsing (incl. the WhatsApp U+200E bug), noise filtering, syllabus matching, coverage math, and the syllabus-from-photo reader (`tests/test_pipeline.py`, `tests/test_syllabus_reader.py`).

---

## 📂 Project Structure

```text
Git_slothNtidy/
├── INBOX/                   # Drop zone: WhatsApp exports, photos, PDFs
├── SlothVault/              # Organized output per subject + SLOTH_INDEX.md
├── app.py                   # 🖥️ Streamlit GUI (added after Week 2)
├── pipeline.py              # 6-stage orchestrator (added after Week 2)
├── parser.py                # Chat-export & document parser + OCR (added after Week 2)
├── filters.py               # SHA-256 / pHash dedupe + circular filter (added after Week 2)
├── embedder.py              # Local MiniLM embeddings (added after Week 2)
├── matcher.py               # Syllabus-to-note matcher (added after Week 2)
├── report.py                # COVERAGE.md + page-evidence report (added after Week 2)
├── exporter.py              # Topic-wise merged PDF export (added after Week 2)
├── filing.py                # Stage 6.5: vault filing + memory rebuild (added after Week 2)
├── syllabus.py              # Syllabus load/save (added after Week 2)
├── syllabus_reader.py       # Syllabus-from-photo reader (added after Week 2)
├── config.py                # Central thresholds & paths
├── mascot.py                # 🦥 GUI mascot
├── tests/                   # pytest suite — 25 tests (added after Week 2)
├── docs/                    # Installer, screenshots, landing page
├── engine.py                # Local Ollama REST client & JSON schema handler (original)
├── Tidy.py                  # PDF pre-scanner, diff analyzer & deduplicator (original)
├── sloth.py                 # Git diff parser, commit generator & auto-pusher (original)
├── main.py                  # Headless CLI entry point for the Notes Sorter
├── requirements.txt
└── CHANGELOG.md             # Auto-generated changelog history
```

---

## 🛠️ Known Fixes & Troubleshooting *(learned post-Week-2)*

* **WhatsApp U+200E bug:** chat exports contain invisible left-to-right-mark characters that break naive parsers — handled in `parser.py`.
* **Tesseract on Windows:** if OCR fails, point pytesseract at your Tesseract install path (done automatically by `setup.bat`).
* **Inbox moves:** the pipeline uses `shutil.move` so the inbox is actually emptied after a run.
* **Pitch-deck PDFs get flagged as circulars:** that's the noise filter working as intended — keep circulars out of the notes inbox.

---

## 🤝 Contributing & License

Distributed under the **MIT License**. Created with ❤️ for **Hacktoberfest 2026** — submitted to **Dev Challenges (Week 2)**, and actively iterated on after the submission. Pull requests, bug reports, and feature enhancements are welcome!
