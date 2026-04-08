# My Skills Repository

A collection of reusable skills for Claude and GitHub Copilot CLI.

## Overview

This repository contains custom skills that extend AI capabilities with specialized workflows and domain expertise.

## Installation

Skills can be installed in several ways:

### GitHub Skills Directory (Cloud-based)
Add this repository URL to your GitHub Skills settings to make all skills available in GitHub Copilot CLI and Claude.ai:
```
https://github.com/Tobitap04/skills.git
```

### Local Installation for Claude Code
**macOS/Linux:**
```bash
mkdir -p ~/.claude/skills
cp -r <skill-folder> ~/.claude/skills/
```

**Windows:**
```bash
mkdir -p %APPDATA%\Claude\skills
xcopy <skill-folder> %APPDATA%\Claude\skills\ /E /I
```

### Local Installation for GitHub Copilot CLI
**macOS/Linux:**
```bash
mkdir -p ~/.agents/skills
cp -r <skill-folder> ~/.agents/skills/
```

**Windows:**
```bash
mkdir -p %USERPROFILE%\.agents\skills
xcopy <skill-folder> %USERPROFILE%\.agents\skills\ /E /I
```

## Available Skills

### RemNote Flashcard Generator
**Folder:** `remnote-flashcard-generator/`

Generate optimal flashcards in RemNote format from lecture PDFs and study notes for computer science topics. Applies evidence-based learning principles to create effective study decks. Supports PDF-only input, notes-only refinement, or combined processing with intelligent consolidation of redundant questions.

**Use Case:** Convert lectures and notes into RemNote-compatible flashcards with proper formatting, definitions-first ordering, and exam-focused content.

---

**Last Updated:** April 2026
