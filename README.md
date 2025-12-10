# SecuSpectra
#sedmvkf
A security system that simulates the reception of new files on a computer, detects suspicious files, assigns a suspicion score, and allows users to decide how to proceed — delete, reject, verify externally, open in an isolated Docker environment, approve, edit (if possible), or block the source.

## Table of Contents
- [Overview and Goals](#overview-and-goals)
- [Target Audience](#target-audience)
- [Key Features](#key-features)
- [High-Level Architecture](#high-level-architecture)
- [Technologies](#technologies)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Workflow and Testing Process](#workflow-and-testing-process)
- [Sandbox and Docker](#sandbox-and-docker)
- [Database](#database)
- [Report and PDF Export](#report-and-pdf-export)
- [Useful Links](#useful-links)
- [Roadmap](#roadmap)
- [License](#license)

## Overview and Goals
SecuSpectra is designed to improve privacy and cybersecurity for end-users by monitoring, analyzing, and controlling incoming files. The system combines static and dynamic analysis and provides real-time results with actionable options for users.

## Target Audience
Private and organizational users who seek to harden their endpoints against suspicious or potentially harmful files — such as those downloaded from untrusted sources or received via email. The focus is on user-friendliness without requiring technical expertise.

## Key Features
- **Static Analysis**: File extension check, signature verification, hash validation, and metadata inspection.
- **Dynamic Analysis**: Execution in a sandbox environment to monitor processes, files, network activity, and permissions.
- **Isolated Environment**: Secure file testing using Docker containers.
- **Suspicion Scoring**: Numerical suspicion score (1–5) with configurable alert thresholds.
- **Real-Time Reporting**: Results continuously updated in the database and exportable to PDF.
- **User Actions**: Delete, reject, approve, edit, block source, or send for external verification (e.g., VirusTotal).
- **Signature Management**: Integration with known safe and malicious file signature databases.
- **File History Tracking**: Full log of all analyzed files and user actions.

## High-Level Architecture
- **Client Module**: File upload, authentication, report display, task scheduling.
- **Core System**: Orchestrates static and dynamic tests, manages communication with Docker, sandbox, and the database.
- **Sandbox Component**: Executes dynamic analysis safely and returns behavioral reports.
- **Docker Module**: Runs isolated analyses for enhanced parallelism and safety.
- **File Analysis Engine**: Implements both static and dynamic checks.
- **Database**: SQLite for lightweight persistent storage of files, test results, and user actions.

## Technologies
- **Languages**: C++ (core engine), optional Python (supplemental analysis)
- **Libraries**: 
  - C++: filesystem, fstream, Windows API, OpenSSL SHA, libharu (PDF generation)
  - Analysis: yara (signature matching), lief (PE/ELF/Mach-O analysis)
- **Infrastructure**: Docker for isolation, Sandbox for behavioral testing
- **Database**: SQLite

## Installation
1. **Pre-Requisites**
   - Docker
   - CMake and a modern C++ compiler
   - Python (optional) with yara and lief
   - SQLite3 development libraries

2. **Build**
   ```bash
   git clone <repo-url>
   cd SecuSpectra
   cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
   cmake --build build --config Release
   ```

3. **Environment Setup**
   - Provide a `config.json` containing input paths, threshold values, and signature sources.
   - Ensure Docker daemon access permissions.

## Quick Start
```bash
# Static scan example
./build/SecuSpectra --scan-static ./samples/suspicious.exe

# Dynamic scan in sandbox
./build/SecuSpectra --scan-dynamic ./samples/suspicious.exe

# Run inside Docker
./build/SecuSpectra --docker-run ./samples/suspicious.exe

# Export PDF report
./build/SecuSpectra --export-pdf ./reports/report.pdf --file-id 123
```

## Workflow and Testing Process
1. New file received from network or external source.
2. **Static Analysis** — check extension, hash, and metadata.
3. **Dynamic Analysis** — execute in sandbox and observe behavior.
4. **Scoring** — assign suspicion level (1–5). Aggregate events trigger alerts.
5. **User Decision** — delete, approve, block, verify externally, or isolate via Docker.
6. **Logging** — all actions and results are stored in the database and report.

## Sandbox and Docker
- **Docker**: Enables efficient, isolated static analysis and concurrent testing.
- **Sandbox**: Focused on runtime behavior of potentially harmful executables.
- Combined, these ensure both safety and performance.

## Database
SQLite schema includes key tables such as:
- `users`, `files`, `tests`, `user_choices`, `blocked_sources`
- `safe_files`, `suspicious_files`, `signatures_malicious`, `signatures_safe`, `extensions`
Each record stores origin, hash, type, permissions, test status, and timestamps.

## Report and PDF Export
Reports are continuously updated from the database and include:
- Test name, purpose, short explanation, execution date, and result.
- Exported via `libharu` to PDF format.

## Useful Links
- **VirusTotal**: https://www.virustotal.com/gui/home/upload
- **File signature references**:
  - https://gist.github.com/leommoore/f9e57ba2aa4bf197ebc5
  - https://en.wikipedia.org/wiki/List_of_file_signatures
  - https://www.scribd.com/document/212801661/List-of-file-signatures-pdf
  - https://gist.github.com/seunlanlege/401898b5ca8486bd6685390cd87b1db4
- **Learning Resources**:
  - Malware Analysis for Beginners
  - Malware Analysis Explained
  - 10 Malware Detection Techniques
  - Static vs. Dynamic Malware Analysis and Best Practices
  - Mastering Dynamic Malware Analysis (Anuj Soni - YouTube)

## Roadmap
**Iteration 1:**
- Gather files from different sources and record origins.
- Extract file properties (size, extension, timestamps, content).
- Implement hash and signature checks.
- Develop decision-making logic for unrecognized files.
- Add source-blocking and detailed reporting.

**Deadlines Example:**
- Oct 25: Gather sample files.
- Oct 28: Implement source tracking.
- Oct 31: Add file metadata extraction.
- Nov 4: Implement hash checking.
- Nov 7: Add signature analysis and decision logic.
- Nov 10: Add source blocking and reporting.
