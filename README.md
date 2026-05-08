# OpenTutor

OpenTutor is an AI-powered math learning workspace that turns questions and PDFs into persistent, interactive lesson artifacts.

Instead of returning a chat answer, OpenTutor builds a reusable lesson with explanations, formulas, a worked example, an interactive visual, and practice. Students can explore the lesson directly, adjust visual controls, revisit previous artifacts, and generate PDF-grounded lessons with citations.

```text
Ask a math question or upload a PDF.
Get a structured lesson you can read, explore, and practice with.
```

## What OpenTutor Creates

Each lesson artifact is a structured learning object:

- **Concept**: a clear explanation of the idea
- **Formulas**: LaTeX-rendered notation with short explanations
- **Worked example**: step-by-step reasoning toward a final answer
- **Interactive visual**: Plotly-based graph or area visualization
- **Controls**: sliders, toggles, and inputs for exploring the concept
- **Practice**: one exercise with hints and a hidden answer
- **Follow-ups**: prompts for extending or regenerating the lesson
- **Citations**: source excerpts when the lesson is based on a PDF

## Lesson Families

The MVP focuses on a small set of polished math experiences:

| Family | Example | Interaction |
| --- | --- | --- |
| Algebra transformations | `f(x)=a(x-h)^2+k` | change `a`, `h`, and `k` to see shifts, scaling, and reflection |
| Limits | `\lim_{x \to a} f(x)` | compare left-hand, right-hand, and two-sided approaches |
| Riemann sums | `\int_a^b f(x) dx` | adjust bounds, rectangle count, and approximation method |

## Architecture

OpenTutor is split into a lesson-focused frontend and a generation-focused backend.

```text
Next.js lesson workspace
        |
        v
FastAPI artifact API
        |
        v
LangGraph generation workflow
        |
        +--> LangChain model and retrieval layer
        +--> SymPy/NumPy math tools
        +--> PostgreSQL + pgvector storage
```

### Frontend

The frontend is a desktop-first lesson workspace built with **Next.js** and **TypeScript**.

It is responsible for:

- rendering lesson artifacts
- displaying Plotly visuals
- rendering formulas with KaTeX
- handling lightweight visual control updates locally
- keeping the prompt input compact and secondary to the lesson

Typical layout:

```text
Top: topic, language, PDF/source state
Main: interactive visual and controls
Side: concept summary and formulas
Below: worked example, practice, citations, follow-ups
```

### Backend

The backend is a **FastAPI** service that owns artifact generation, validation, persistence, and PDF grounding.

It is responsible for:

- creating lesson artifacts from prompts
- ingesting uploaded PDFs
- retrieving source excerpts for grounded lessons
- validating math expressions and LaTeX
- generating visual specifications
- regenerating sections such as practice or worked examples
- storing sessions, sources, artifacts, versions, visuals, and tool runs

### Generation

OpenTutor uses **LangGraph** to coordinate the artifact workflow. A request moves through topic classification, lesson planning, visual selection, math validation, formula generation, worked example generation, practice generation, citation checks, and artifact assembly.

**LangChain** provides model integration, structured output, prompt templates, retrieval pieces, and embedding adapters.

**SymPy** and **NumPy** handle deterministic math work such as expression parsing, canonical LaTeX, graph data, limit tables, transformation parameters, and Riemann sum calculations.

### PDF Grounding

PDF mode uses retrieval over the uploaded document:

```text
upload PDF -> extract text -> chunk by page/section -> embed -> store -> retrieve -> generate with citations
```

The generated lesson follows the PDF's terminology and notation where relevant, while still adding interactive visuals, examples, and practice.

## Planned API

```text
POST /api/sources/pdf
POST /api/lesson-artifacts
GET  /api/lesson-artifacts/:id
PATCH /api/lesson-artifacts/:id
POST /api/lesson-artifacts/:id/validate-expression
POST /api/lesson-artifacts/:id/regenerate-section
GET  /api/sessions/:id
GET  /api/examples
GET  /api/health
```

## Stack

- **Frontend**: Next.js, TypeScript, Plotly, KaTeX
- **Backend**: FastAPI, LangGraph, LangChain, Pydantic v2
- **Math**: SymPy, NumPy
- **Data**: PostgreSQL, pgvector, SQLAlchemy 2.0, Alembic

