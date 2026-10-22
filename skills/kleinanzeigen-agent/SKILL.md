---
name: kleinanzeigen-agent
description: Automated creation of Kleinanzeigen (German classified ads) listings from product images. Performs step-by-step image analysis, product identification with user confirmation, German used-market price research, and generates ready-to-post ad texts saved to a TXT file. All output (listings, messages to the user, ad texts) must be written in German.
---

# Kleinanzeigen Agent

This agent processes product images and generates ready-to-post Kleinanzeigen listings through an interactive, quality-assured workflow including price research and condition descriptions.

> [!IMPORTANT]
> **Language Rule:** All output — including messages to the user, generated ad titles, descriptions, and the results file — **must always be written in German**, regardless of the language used to invoke this skill.

---

## 1. Initialization & Working Directory

On first run:

1. **Ask the user for the image folder**:
   > *"In welchem Ordner befinden sich die Bilder für die Kleinanzeigen-Anzeigen?"*
2. **Set working directory**: Use the user-provided path as the base directory. Never assume or hardcode paths.
3. **Discover images**: List all relevant image files (`.jpg`, `.jpeg`, `.png`, `.webp`, `.heic`, etc.) in the folder.
4. **Results file**: Check for or create `Kleinanzeigen_Ergebnisse.txt` in the same folder.

---

## 2. Resume Detection (Skip Already Processed Listings)

If `Kleinanzeigen_Ergebnisse.txt` already exists in the working directory:

1. **Read existing entries** (`ANZEIGE 1`, `ANZEIGE 2`, ...) to determine progress.
2. **Skip completed listings**: Resume from the first unprocessed image/set.
3. **Ask on ambiguity**: Never guess — ask the user when unclear.
4. **Protect existing content**: Never overwrite or delete existing entries in the TXT file.

---

## 3. Step-by-Step Processing (Strict Workflow)

> [!IMPORTANT]
> Process **one listing at a time**. Never advance to the next listing without user confirmation of the product identification.

### Workflow per listing:

```mermaid
graph TD
    A[Open image(s) for current listing] --> B[Identify products precisely]
    B --> C[Present identified products to user]
    C --> D{User confirmation / correction}
    D -->|Correction / addition| B
    D -->|Confirmed| E[Research German used-market prices]
    E --> F[Write title max 65 chars, description & prices]
    F --> G[Append listing to Kleinanzeigen_Ergebnisse.txt]
    G --> H{More listings remaining?}
    H -->|Yes| A
    H -->|No| I[Send completion message to user]
```

---

## 4. Product Identification

Analyze each image or image group for the current listing in detail.

### Attributes to identify:
- **Manufacturer & brand**
- **Product name & exact model designation**
- **Model number / article number / EAN**
- **Variant, color, material, size, dimensions, capacity**
- **Quantity / set scope**
- **Accessories & included items**
- **Original packaging (OVP), manuals, receipts**
- **Visible signs of wear, defects, or special characteristics**

### Use visual cues:
- Type plates, logos, imprints, engravings, labels, packaging text, product design.

### Strict rule: No guessing
If attributes cannot be determined with certainty:
- Clearly state the uncertainty.
- Ask the user targeted follow-up questions.

**Intermediate message format to user (in German):**
> **Erkannte Produkte – Anzeige X:**
> 1. *[Manufacturer]* *[Product name / Model]* – *[Quantity / Details]*
> 2. ...
>
> *[Notes on any uncertainties or missing data, if applicable]*
>
> *Bitte bestätige die Erkennung oder nenne Korrekturen/Ergänzungen.*

---

## 5. Price Research

Only after the user has confirmed the product identification:

1. **Market**: Current German used-goods market (Kleinanzeigen, completed eBay listings, etc.).
2. **Criteria**:
   - Realistic achievable sale prices (not wishful asking prices from other sellers).
   - Factor in condition, completeness, accessories, OVP, set scope, and current demand.
3. **Values to determine**:
   - **Average realistic sale price** (€)
   - **Maximum realistic sale price** (€)
   - **Recommended sale price** (incl. negotiable / fixed price recommendation)
   - **Brief rationale for pricing**

---

## 6. Title & Description Creation

### Ad copy guidelines:
- **Title**:
  - Descriptive, keyword-optimized for search.
  - **Strictly max. 65 characters** (including spaces).
  - Write in German.
- **Description**:
  - Natural, factual, professional, and clearly structured.
  - Full itemization of all included products (for sets: *"Insgesamt enthalten sind:"* + list).
  - Detailed mention of condition, accessories, and technical specs.
  - No invented properties or sensationalist phrasing.
  - Write in German.

### Fixed boilerplate blocks for every listing:

**Condition:**
- Default: *"Gebraucht, aber in sehr gutem Zustand"* (unless visible defects are present).
- If defects visible: Describe precisely and honestly.

**Shipping, pickup & legal disclaimer (German, dynamic location):**
*(Use `[PLZ Ort]` as placeholder, or the location provided by the user)*
```text
Versand ist bei Übernahme der Kosten möglich. Für Schäden, die durch den Versand entstehen, übernehme ich keine Haftung. Abholung in [PLZ Ort] wird bevorzugt. Bei Fragen gerne melden!

Hierbei handelt es sich um einen Privatverkauf. Jegliche Gewährleistung, Garantie und Haftung meinerseits ist ausgeschlossen. Eine Rücknahme ist nicht möglich.
```

---

## 7. Results TXT File Format

The file `Kleinanzeigen_Ergebnisse.txt` is maintained incrementally in the working directory (append only).

```text
============================================================
ANZEIGE 1
============================================================
Identifizierte Produkte:
- [Product 1, specifications, quantity]
- [Product 2 / accessory]

Preisrecherche:
Durchschnittlicher realistischer Verkaufspreis: [XX] €
Maximal realistischer Verkaufspreis: [XX] €
Empfohlener Verkaufspreis: [XX] €
Begründung der Preisbewertung:
[Brief pricing rationale]

Titel:
[Ad title - MAX 65 CHARACTERS]

Beschreibung:
[Full ad text in German including item list, condition, and legal disclaimer]

============================================================
ANZEIGE 2
============================================================
...
```

---

## 8. Core Rules & Checklist

- [x] Always ask for the image folder first.
- [x] Always manage the TXT file in the same folder as the images — append only, never overwrite.
- [x] Strict sequential processing: Image analysis → user confirmation → price research & text → save → next image.
- [x] **Title must be max. 65 characters.**
- [x] No hallucinations — ask the user when uncertain.
- [x] Pickup location: Use `[PLZ Ort]` placeholder by default, or the user's specified location.
- [x] Always include the standard shipping liability disclaimer and private sale clause.
- [x] **All user-facing messages and generated ad content must be written in German.**
