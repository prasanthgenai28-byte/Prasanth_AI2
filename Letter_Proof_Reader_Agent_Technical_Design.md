# Letter Proof Reader Agent

## Technical Architecture & System Design Document

------------------------------------------------------------------------

# 1. Objective

Design an AI Agent that:

-   Validates dynamically generated letters (from source system)
-   Validates uploaded PDF letters
-   Detects:
    -   Grammar & spelling errors (basic level)
    -   Word spacing issues
    -   Paragraph spacing inconsistencies
    -   Capitalization at paragraph start
    -   Bullet indentation & spacing issues
    -   Period consistency
    -   Presence of header, name/address, signature, footer
-   Generates annotated PDF output
    -   Original layout preserved
    -   Errors highlighted at exact coordinates
    -   Proper error annotations attached
-   Operates in strict mode
    -   Highlight every minor issue
    -   Output must remain readable and clean

------------------------------------------------------------------------

# 2. High-Level Architecture

Input Sources: 1. Source System (HTML) 2. Uploaded PDF

Pipeline: Input → Content Ingestion → Document Normalization →
Validation Engine → Error Mapping → Annotated PDF Generator

------------------------------------------------------------------------

# 3. System Phases (Agent Execution Flow)

## Phase 1 --- Input Acquisition

Mode A: Direct System Scraping - Scrape rendered HTML/text before PDF
generation - Preserve semantic blocks - Capture DOM structure

Mode B: PDF Upload - Extract: - Text - Line structure - Font metadata -
X/Y coordinates - Bounding boxes

Output Model:

DocumentModel: - tokens\[\] - paragraphs\[\] - bullet_blocks\[\] -
line_positions\[\] - section_candidates\[\]

------------------------------------------------------------------------

## Phase 2 --- Document Structure Detection

### Section Presence Detection (Presence Only)

Validate existence of: - Header - Name & Address section - Body -
Signature - Footer

Output:

SectionPresenceReport: - header: true/false - name_address: true/false -
body: true/false - signature: true/false - footer: true/false

------------------------------------------------------------------------

## Phase 3 --- Linguistic Validation Engine (Body Only)

Strict Mode: Highlight every issue.

### Grammar & Spell Check

-   Dictionary-based spell check
-   Deterministic grammar rules
-   No rewriting or tone correction

### Word Spacing Validation

-   Detect double spaces
-   Detect irregular spacing
-   Detect missing space after punctuation
-   Detect extra space before punctuation

### Paragraph Spacing Consistency

-   Measure vertical Y-distance between paragraph blocks
-   Identify dominant spacing
-   Flag outliers

### Capitalization Rule

-   First alphabetical character of each paragraph must be uppercase

### Bullet Validation

Validate: - Proper indentation alignment - Uniform spacing after bullet
symbol - Proper punctuation in bullet lines - Period consistency across
bullets

### Period Consistency

-   All paragraphs must follow consistent period usage
-   All bullet lines must follow consistent period usage

------------------------------------------------------------------------

# 4. Error Mapping Engine

Responsibilities: 1. Map validation errors to exact PDF coordinates 2.
Identify bounding box of token or line 3. Create highlight rectangle 4.
Attach annotation metadata

ErrorAnnotation: - page_number - bounding_box (x1, y1, x2, y2) -
error_type - error_message

------------------------------------------------------------------------

# 5. Annotated PDF Generation

Requirements: - Preserve original layout 100% - Do not re-render text -
Add overlay highlights - Add readable annotation comments - Avoid
overlapping comments

Output: original_filename_annotated.pdf

------------------------------------------------------------------------

# 6. Internal Modules

## Document Model Layer

Unifies: - HTML input - PDF input

## Rule Engine Layer

Deterministic validation: - Grammar rules - Spacing rules - Bullet
rules - Capitalization rules

## Layout Intelligence Layer

-   Paragraph boundary detection
-   Vertical spacing measurement
-   Indentation measurement
-   Section block identification

## Annotation Layer

-   Text-to-coordinate index
-   Bounding box merge logic
-   Collision avoidance system

------------------------------------------------------------------------

# 7. Strict Mode Policy

-   Highlight every issue
-   No suppression threshold
-   No grouping minor issues
-   All errors individually annotated

------------------------------------------------------------------------

# 8. Non-Functional Requirements

-   Support multi-page PDFs
-   Support large letters (10+ pages)
-   Maintain coordinate precision
-   Optimized execution time
-   Deterministic results

------------------------------------------------------------------------

# 9. Agent Operational Prompt

You are a deterministic Letter Proof Reader Agent.

PHASE 1 --- INGEST If input is HTML → parse DOM. If input is PDF →
extract text with coordinates.

PHASE 2 --- SECTION PRESENCE VALIDATION Detect presence of: - Header -
Name & Address - Body - Signature - Footer

PHASE 3 --- BODY VALIDATION Apply strict proofreading: 1. Grammar &
spelling 2. Word spacing issues 3. Paragraph spacing inconsistencies 4.
Capital letter at paragraph start 5. Bullet indentation alignment 6.
Uniform spacing after bullet 7. Proper punctuation in bullet lines 8.
Period consistency across document

Highlight every issue.

PHASE 4 --- ERROR MAPPING Map each error to exact PDF coordinates.

PHASE 5 --- ANNOTATION GENERATION Overlay highlights on original PDF.
Attach readable annotation message. Preserve layout.

OUTPUT: Generate annotated PDF with all errors highlighted.

------------------------------------------------------------------------

# 10. Design Characteristics

-   No template dependency
-   Deterministic validation
-   Structural validation: Presence only
-   Layout-preserving annotation
-   Strict proofreading enforcement
