---
name: remnote-flashcard-generator
description: Generate optimal study flashcards in RemNote format from lecture PDFs and/or existing study notes. Use when the user asks to create flashcards, convert lecture materials to RemNote format, generate study cards from PDFs or notes, or refine existing RemNote questions. Handles both scenarios - PDF-only input, notes-only input, or combined PDF+notes where the PDF serves as the authoritative source for refinement and gap-filling.
---

# RemNote Flashcard Generator

Generate evidence-based, exam-focused flashcards in RemNote format from lecture materials and study notes. This skill applies cognitive science principles (minimum information principle, active recall) to create optimal flashcards for exam preparation.

## Core Principles

Your flashcards must follow these evidence-based best practices:

1. **Minimum Information Principle** - One fact per card. This is the most important rule. Break complex concepts into atomic facts. NEVER ask "What are X and Y?" - make two separate cards.

2. **Active Recall Over Recognition** - Test understanding, not memorization. Avoid yes/no questions and multiple choice unless testing specific discriminations.

3. **No Orphan Cards** - Every card needs context. Don't create isolated facts that have no connection to broader concepts.

4. **Quality Over Quantity** - Better to have 30 excellent cards than 80 mediocre ones. Be selective about what's truly exam-relevant.

5. **Preserve Existing Questions** - When refining notes, keep the user's questions and improve them. Don't replace or omit their questions - they chose those topics for a reason.

6. **Visual Formatting Enhances Retention** - Use **bold** for key terms, `inline code` for function names/syntax, LaTeX for equations, and italic only with `__text__` (never `*text*`).

7. **Definition First** - Always place the definition of a concept BEFORE any follow-up questions about it. If user's notes have questions in the "wrong" order (e.g., "How does X work?" before "What is X?"), reorder them so the definition comes first.

8. **Overview Before Details** - When there are multiple methods/algorithms for solving a problem (e.g., search algorithms, sorting methods, optimization techniques), ALWAYS add an overview question first that lists all methods, then individual questions for each:
   - First: "What are the main **search algorithms** for lists?" → Binary Search, Linear Search, Interpolation Search
   - Then: "How does **Binary Search** work?" → ...
   - Then: "How does **Linear Search** work?" → ...

9. **Bold Keywords in Questions** - ALWAYS make the main keyword/concept in each question **bold**:
   - ✅ "What is **Information Gain** in feature selection?"
   - ✅ "How does **SMAC** work?"
   - ✅ "What is the default behavior of `sample(x)`?" (Use of inline code is the only exception)
   - ❌ "What is Information Gain in feature selection?"

10. **Concise Basic Answers** - For basic Q&A cards (>>), keep answers SHORT. If an answer requires more than ~10 words, either:
    - Convert to multi-line card (>>>)
    - Split into multiple cards
    - ❌ "Removes features with highly skewed value distributions, eliminating both very high frequency features (e.g., 'the', 'and') and very low frequency features"
    - ✅ "Removes features with extreme occurrence frequencies (very high or very low)"
    - ✅ Convert to multi-line: "How does **Frequency Filtering** work? >>>" with concise bullets

11. **Explain Mathematical Notation** - When an answer contains mathematical symbols or complex notation, always explain what each symbol means within the same card. Convert to multi-line format (>>>) to include the notation alongside its definition:
    - ❌ "What is **Automated Algorithm Configuration**? >> Finding $\theta^* = \arg\max_{\theta \in \Theta}\, \mathbb{E}_{\pi \sim \mathcal{D}}\, p(A_\theta, \pi)$" (unclear symbols)
    - ✅ Use multi-line with explanations (make each card like this):
      ```
      - What is **Automated Algorithm Configuration (AAC)**? >>>
        - Goal: find optimal config $\theta^* = \arg\max_{\theta \in \Theta}\, \mathbb{E}_{\pi \sim \mathcal{D}}\, p(A_\theta, \pi)$
        - $\Theta$ — configuration space
        - $\pi$ — problem instance, drawn from distribution $\mathcal{D}$
        - $p(A_\theta, \pi)$ — performance of algorithm $A$ with config $\theta$ on instance $\pi$
      ```

## Input Handling

You will receive input in one of the following formats:

### PDF Only
Extract text from the PDF, identify main topics and exam-relevant concepts, generate comprehensive flashcard coverage. Focus on definitions, algorithms, properties, trade-offs, and computational complexity—skip trivial details like author birth dates or historical anecdotes unless exam-relevant.

### Notes Only  
The notes are structured hierarchically with bullets:
- **H2 headings** (top-level topics, e.g., "Feature Selection")
- **H4 subheadings** (subtopics, e.g., "Filter methods")
- **Questions** (may be incomplete sentences or poorly worded)
- **Partial answers/notes** (indented under questions, may be fragments)
- **Empty answer slots** (questions with no content beneath)

Your job:
1. **Consolidate redundant questions** - Look for duplicate or very similar questions scattered throughout the notes and merge them into single, well-worded questions
2. **Complete empty answers** - Fill in based on your knowledge
3. **Refine questions** - Convert fragments to complete, grammatically correct sentences
4. **Enhance answers** - Expand partial notes into complete, formatted answers using bullet points
5. **Preserve structure** - Maintain the H2/H4 heading hierarchy
6. **Fill gaps** - Add missing questions if the existing ones don't cover all important aspects of the topic

### PDF + Notes (Combined)
Use the PDF as the authoritative source:
1. Extract content from PDF
2. **Consolidate redundant questions** from notes - merge similar/duplicate questions scattered throughout
3. **Preserve and refine remaining questions** - don't skip or replace them
4. Improve question wording using PDF terminology
5. Complete/enhance answers using PDF content
6. **Only add new questions if critical topics are missing** - be conservative
7. Improve heading/subheading wording if needed for clarity

**Important**: 
- When both are provided, the PDF is ground truth for content
- User's existing questions show what they care about - keep and improve them
- Don't generate excessive cards - quality > quantity

## Card Density Guidelines

**BE CONSERVATIVE**. Quality matters far more than quantity.

**Every card must justify its existence**:
- Is it truly exam-relevant?
- Does it test a distinct, important concept?
- Can't it be merged with another card?

**Avoid long cards**:
- Multi-line answers: max 5 bullet points
- Numbered lists: max 5 items
- Basic Q&A: max ~10 words in answer
- If a concept requires more, split into multiple related cards

### PDF-Only Input
- Target to cover all exam-relevant concepts (definitions, algorithms, properties, trade-offs, complexity).
- Don't create cards for trivial details (author birth dates, historical context, etc.) unless explicitly exam-relevant.

### Notes-Only or Notes+PDF Input
**First consolidate, then add carefully**:
- **Consolidate redundant questions** - Merge similar questions scattered throughout notes
- Only add if there's a **critical gap** the user's questions don't cover
- If user's notes already comprehensively cover the topic, add nothing

## RemNote Card Type Selection

Choose the optimal card type for each concept:

### Basic Q&A (`>>`)
**Use for**: Conceptual questions, definitions, "what/why/when" questions

**Format**: Dash before question, space after `>>`

```
- What is the time complexity of QuickSort in the average case? >> $O(n \log n)$
```

### Two-Way (`<>`)
**Use for**: Bidirectional relationships, term ↔ definition

**Format**: Dash before term

```
- Binary Search Tree <> A tree data structure where left children are smaller and right children are larger than the parent node
```

### Cloze (`{{...}}`)
**Use for**: Fill-in-the-blank, embedded facts, definitions with key terms

**Format**: Dash before statement

```
- A {{Hash Table}}{({data structure})} provides $O(1)$ average-case lookup time using {{hashing}}.
```

**With hints**: Add `{({hint})}` immediately after cloze for disambiguation

### Multi-line (`>>>`)
**Use for**: Lists of related properties, characteristics (when order doesn't matter)

**Format**: Dash before question, exactly 2 spaces before each bullet point

```
- Properties of **Greedy Algorithms** >>>
  - Make locally optimal choice
  - Don't reconsider decisions
  - May not find global optimum
  - Often efficient
```

### List-Answer (`>>1.`)
**Use for**: Ordered sequences, algorithm steps, numbered procedures

**CRITICAL**: Use this for ALL algorithm steps, not multi-line (`>>>`). Order matters!

**Format**: Dash before question, exactly 2 spaces before each step

```
- Steps in **Merge Sort** >>1.
  - Divide array into halves
  - Recursively sort each half
  - Merge sorted halves
```

### Multiple-Choice (`>>A)`)
**Use sparingly**: Only for discriminating between commonly confused concepts

**Format**: Dash before question, exactly 2 spaces before each option

```
- Which traversal visits BST nodes in ascending order? >>A)
  - In-order traversal
  - Pre-order traversal
  - Post-order traversal
```

**First nested bullet = correct answer** (RemNote shuffles during practice)

### Concept/Descriptor Framework
**Use rarely**: Only if you're already using RemNote's concept/descriptor system

- `:>` forward concept card
- `;;` forward descriptor card
- `::` two-way concept card

## Visual Formatting

Apply consistently:

- **Bold** (`**text**`) - Important terms, key concepts, algorithm names
- __Italic__ (`__text__`) - Definitions, formal terminology (double underscore, no extra spaces)
- `Inline code` (backticks) - Function names, keywords, syntax (`quickSort()`, `malloc()`, `class`)
- LaTeX (`$formula$`) - Equations, complexity notation (`$O(n^2)$`, `$\sum_{i=1}^{n} i$`)

**IMPORTANT RemNote Syntax**:
- **Inline code cannot be made bold** - Do not attempt to combine bold and code formatting (e.g., **`func(x)`** won't work). Use either bold OR code, not both:
  - ✅ `func(x)` (inline code)
  - ✅ **Host** (bold)
  - ❌ **`func(x)`** (won't render correctly)
- Italic: `__text__` (double underscore, NO extra spaces)
- For italic, do **not** place punctuation immediately after closing `__`. Place a normal word first, then punctuation.
  - ✅ `__term__ is important.`
  - ❌ `__term__.`
- Underline: `  _text_  ` (single underscore with 2 spaces before and after)
- Do NOT use `*text*` for italic - it doesn't work in RemNote!

**Example with full formatting**:

```
        - What is the **Bellman-Ford Algorithm** used for? >> Finding __shortest paths__ in graphs with **negative edge weights**, running in $O(V \cdot E)$ time
```

## Language and Wording

- **Output language**: Always English, regardless of input language
- **Questions**: Complete sentences with proper grammar and punctuation
  - ❌ "Grid Search advantages?"
  - ✅ "What are the advantages of Grid Search?"
- **Answers**: Can be bullet points/fragments (concise, not full sentences)
  - ✅ "All configurations considered"
  - ✅ "Massively parallelizable"
- **Terminology**: Use standard terminology from the lecture/PDF when available

## Heading Structure

Preserve the hierarchical structure from input with proper indentation:

```
## Feature Selection
    - What is **Feature Selection**? >> Identifying and keeping only the most informative features from a dataset
    #### Filter Methods
        - What is Information Gain filtering? >> Selects features maximizing $I(X; Y)$
        - What is Frequency Filtering? >> Selects features based on occurrence counts
    #### Wrapper Methods
        - What are problems with forward selection? >>>
          - Misses features strong in combination
          - Computationally expensive
```

**Indentation Rules**:
- **H2 (`##`)** - No indentation (left-aligned)
- **H4 (`####`)** - 4 spaces indentation (under H2)
- **Cards** - 4 spaces indentation (under H2), 8 spaces indentation (under H4)
- **Bullets** - 6 spaces indentation (under card question in H2), 10 spaces indentation (under card question in H4)
- **NO blank lines**

## Workflow

### Step 1: Extract and Analyze

**If PDF provided**:
1. Extract full text using Python (PyPDF2, pdfplumber, or similar)
2. Identify main topics, algorithms, definitions, theorems
3. Note equations, code snippets, diagrams (describe if relevant)
4. Flag exam-relevant content (complexity analysis, trade-offs, key properties)

**If notes provided**:
1. Parse hierarchical structure (H2/H4 headings)
2. Identify questions (lines ending with "?")
3. Map partial answers/notes to questions
4. List empty answer slots

**If both**:
1. Extract PDF content
2. Parse note structure  
3. Map questions to PDF sections
4. Identify coverage gaps

### Step 2: Generate Flashcards

For each exam-relevant concept:

1. **Choose card type** - Based on guidelines above
2. **Write question** - Complete sentence, clear and specific
3. **Write answer** - Concise, formatted, atomic (one fact)
4. **Add formatting** - Bold, italic, code, LaTeX as appropriate
5. **Check length** - Multi-line/lists ≤5 items; split if needed

### Step 3: Quality Control

Before outputting, verify each card:

- ✅ One fact per card (minimum information principle)
- ✅ Tests understanding, not recognition (active recall)
- ✅ Has context (no orphans)
- ✅ Question is complete sentence with **bold keyword** (exception: if the keyword itself is an inline code block)
- ✅ Answer is concise (basic Q&A ≤10 words, multi-line ≤5 bullets)
- ✅ Formatting applied correctly
- ✅ Exam-relevant (not trivial)
- ✅ **Definition comes before related questions** (reorder if needed)
- ✅ **Overview question before individual method questions** (for multiple methods)
- ✅ **Redundant questions consolidated** (merge similar questions scattered in notes)

### Step 4: Output Format

Structure the output as valid RemNote import with proper indentation:

```
## Topic Name
    - Question text >> Answer text
        - Another question >>>
          - Bullet point one
          - Bullet point two
    #### Subtopic
        - Question text >> Answer text
        - Another question >>>
          - Bullet point one
          - Bullet point two
    #### Another Subtopic
        - Question text >> Answer text
```

**CRITICAL FORMATTING RULES**:
1. **No blank lines** between cards or after headings
2. **Proper indentation**: H2 (0), H4 (4 spaces), cards (4 spaces when part of H2, 8 spaces when part of H4), bullets (6 spaces when part of H2, 10 spaces when part of H4)
3. **Dash before every question** (even basic Q&A)
4. **Exactly 2 additional spaces** before nested bullets (relative to card)
5. **No markdown code fences** around output
6. **Use `__text__` for italic** (NOT `*text*` - doesn't work in RemNote)
7. **After `__italic__`, put a normal word before punctuation** (NOT `__term__.`)
8. **Don't use inline code with bold formatting** (NOT **`func(x)`**)
9. **No trailing blank line** at end of output

## Common Flashcard Patterns

### Overview Then Details Pattern (for multiple methods)

When a topic has multiple approaches/methods/algorithms, ALWAYS structure like this:

```
    #### Feature Selection Methods
        - What are the main types of **feature selection** methods? >>>
          - Filter methods
          - Wrapper methods
          - Embedded methods
        - What are **Filter** methods? >> Methods that evaluate features using __statistical properties__ independent of the model
        - What are **Wrapper** methods? >> Methods that evaluate feature subsets based on model performance
        - What are **Embedded** methods? >> Methods where feature selection occurs during model training
```

First the overview question listing all methods, then definition of each, then details.

### Algorithm Complexity

```
- What is the time complexity of **Binary Search**? >> $O(\log n)$
- What is the space complexity of **Merge Sort**? >> $O(n)$
```

Note: Keep answers SHORT for basic Q&A cards.

### Algorithm Steps (use >>1. for ordered sequences!)

```
- How does **Forward Selection** work? >>1.
  - Start with empty set $F = \emptyset$
  - Select best feature $X_r$
  - Add if improves score
  - Repeat until convergence
```

### Algorithm Properties (unordered)

```
- What are properties of **Depth-First Search**? >>>
  - Uses stack or recursion
  - Not necessarily shortest path
  - $O(V + E)$ time complexity
  - Can detect cycles
```

### Trade-offs (can use multi-line for clarity)

```
- What is the trade-off between **Hash Tables** and **BSTs**? >>>
  - Hash tables: $O(1)$ average lookup, no ordering
  - BSTs: $O(\log n)$ lookup, maintains sorted order
```

### Code/Syntax

```
- What does `malloc(size)` return in C? >> A `void*` pointer to allocated memory, or `NULL` if allocation fails
```

### Definitions (with italic)

```
- What is **Dynamic Programming**? >> An __optimization technique__ that breaks problems into overlapping subproblems and stores solutions
- {{Dynamic Programming}}{({optimization technique})} breaks problems into overlapping subproblems
```

## Edge Cases

- **Practice problems in slides**: Convert to flashcards (e.g., "What is the output of `function(5)`? >> `25`")
- **Diagrams/figures**: Describe key insights if exam-relevant (e.g., "What does the ROC curve show? >> Trade-off between true positive rate and false positive rate")
- **Proofs/derivations**: Focus on key steps or results, not full proof (unless explicitly exam-relevant)
- **Historical context**: Skip unless exam-relevant
- **Code blocks**: If >10 lines, extract key concepts rather than memorizing full code

## Example Transformation

**Input (from notes)**:
```
- Feature Selection
    - What is Feature Selection?
    - Filter Methods
        - Information Gain Filtering
        - How does Frequency Filtering work?
        - What is Markov Blanket Filtering?
```

**Output (with PDF context)**:
```
## Feature Selection
    - What is **Feature Selection**? >> Identifying and keeping only the most informative features from a dataset
    #### Filter Methods
        - What are **Filter** methods in feature selection? >> Methods that evaluate features using __statistical properties__ independent of the model
        - What are the main **filter** techniques? >>>
          - Frequency Filtering
          - Information Gain Filtering
          - Markov Blanket Filtering
        - How does **Frequency Filtering** work? >> Removes features with extreme occurrence frequencies
        - How does **Information Gain** filtering work? >> Selects features maximizing $I(X; Y)$ between feature and target
        - How does **Markov Blanket** filtering work? >> Selects features in the Markov blanket of the target variable, making it conditionally independent of all others
```

**Rationale**:
- **Definition first**: Added "What are Filter methods?" before the how-to questions
- **Overview before details**: Added "What are the main filter techniques?" listing all methods
- **Preserve and refine user's questions**: All original questions kept and refined
- **Concise answers**: Short answers for basic Q&A (no long sentences)
- **Bold keywords**: Main concept bolded in each question 
- **Proper indentation**: H2 (0 spaces), H4 (4 spaces), cards (4 or 8 spaces), bullets (6 or 10 spaces)

## Anti-Patterns to Avoid

❌ **Compound questions**: "What are Grid Search's advantages and disadvantages?"  
✅ **Split into two cards**: One for advantages, one for disadvantages

❌ **Yes/no questions**: "Is Binary Search faster than Linear Search?"  
✅ **Test understanding**: "What is the time complexity advantage of Binary Search over Linear Search? >> $O(\log n)$ vs $O(n)$"

❌ **Vague questions**: "What about Quicksort?"  
✅ **Specific**: "What is the worst-case time complexity of Quicksort? >> $O(n^2)$"

❌ **Orphan cards**: "What is a Markov blanket? >> ..."  
✅ **With context**: "In feature selection, what is a **Markov blanket**? >> The minimal set of features that makes a feature conditionally independent of all others"

❌ **Overlong lists**: 10+ bullets in a multi-line answer  
✅ **Split**: "Properties of X (1/2)" and "Properties of X (2/2)"

## Summary Checklist

Before finalizing output:

- [ ] All relevant concepts covered (definitions, algorithms, properties, trade-offs, complexity)
- [ ] All questions are complete sentences with **bold keyword** (exception: if the keyword itself is an inline code block)
- [ ] All answers are concise (basic Q&A ≤10 words, multi-line ≤5 bullets)
- [ ] One fact per card (NO compound questions like "What are X and Y?")
- [ ] **Redundant questions consolidated** (merge similar questions from notes)
- [ ] **Definitions come BEFORE related questions** (reorder if needed)
- [ ] **Overview questions BEFORE individual method questions** (for multiple methods)
- [ ] Algorithm steps use `>>1.` (ordered), properties use `>>>` (unordered)
- [ ] Visual formatting: **bold**, `__italic__`, `code`, LaTeX
- [ ] No punctuation (e.g., ';',',','.','?','!') directly after closing `__italic__` (place a normal word first)
- [ ] No use of inline code with bold formatting. If it occurs, remove the bold formatting (even if it is the question keyword)
- [ ] Correct indentation: H2 (0), H4 (4 spaces), cards (8 spaces), bullets (10 spaces)
- [ ] Dash before EVERY card (basic Q&A, cloze, multi-line, etc.)
- [ ] Exactly 2 additional spaces before nested bullets
- [ ] NO blank lines
- [ ] H2/H4 structure preserved
- [ ] When refining notes: ALL user's questions preserved and improved (after consolidation)
- [ ] No trivial facts (author birth dates, etc.)
- [ ] Output is in English
- [ ] No markdown code fences around output (ready to paste into RemNote)

---

## Output Format

Deliver the final flashcards in one of these formats:

**Option 1: Complete text file** - Save as `.txt` file with all flashcards. Useful for reviewing, editing, or sharing before import to RemNote.

**Option 2: Code block** - Display all flashcards in a single markdown code block, ready to copy-paste into RemNote.

The user can specify which format they prefer, or you can choose whichever is most appropriate for the situation.

---

## User Overrides and Refinements

**User instructions always take precedence** over this skill's defaults. Examples:
- "I only want exactly 5 flashcards" → Generate exactly 5, ignore card count guidelines
- "Focus only on SMAC" → Only cover that topic, ignore comprehensive coverage rules
- "Make all cards cloze format" → Use cloze even where other formats would normally be preferred

**For refinement requests** (e.g., "fix the formatting on card 3", "make the SMAC answer shorter", "add bold to the keywords"):
- Do NOT re-read the PDF or notes from scratch
- Do NOT regenerate all cards
- Instead, make **targeted edits** to address the specific issue mentioned
- If user provides the current output, edit it directly
- Only re-process source materials if explicitly asked or if the refinement requires new content

---

You're ready! Extract, analyze, generate, and deliver optimal RemNote flashcards.
