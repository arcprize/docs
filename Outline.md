# Documentation Information Architecture Outline

## Top-Level Navigation (3 Tabs)

1. **Overview**
2. **ARC-AGI SDK**
3. **REST API**

---

## Tab 1: Overview

### Get Started
- **Quickstart** ← Home page, entry point
- **API Keys**

### Core Concepts
- **Games**
  - Game Schema
  - Available Games
  - Actions
- **Scoring**
  - Methodology
  - Scorecards
- **Recordings**
- **Agents**
  - Overview
  - LLM Agents
  - Partner Templates
    - Anthropic
    - LangChain
    - HuggingFace
    - AgentOps

### Further Reading
- **Local vs Online**
- **Full Play Test** ← What happens under the hood
- **Swarms**
- **Contributing**

---

## Tab 2: ARC-AGI SDK

### Get Started
- **Overview**
- **Examples**
  - REPL
  - Minimal

### Guides
- **List Games**
- **Create Game**
- **Edit Game**

### API Reference
- **Arcade Class**
- **Environment Wrapper**

---

## Tab 3: REST API

- **Overview**
- **Rate Limits**
- **[OpenAPI Endpoints]** (auto-generated)

---

## Notes

- Quickstart is the front door (toolkit-first, not agent-first)
- Full Play Test shows the raw workflow for those who want to understand mechanics
- Agents are secondary, nested under Core Concepts
- SDK tab has its own "Get Started" flow for those diving into the Python SDK
- Creating/Editing Games lives under SDK since it's hands-on toolkit work
