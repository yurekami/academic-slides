---
name: academic-slides
description: Create Beamer-inspired academic HTML presentations from scratch or by converting PowerPoint files. Use when the user wants to build slides for a conference talk, lecture, seminar, or thesis defense. Supports theorem environments, KaTeX equations, algorithm pseudocode, and citations. Helps academics discover their preferred visual theme through Beamer-style previews rather than abstract choices.
---

# Academic Slides Skill

Create zero-dependency, Beamer-inspired HTML presentations that run entirely in the browser. This skill helps academics and researchers build professional presentations with proper theorem environments, equation rendering via KaTeX, and structured frame navigation. Users discover their preferred theme through visual exploration ("show, don't tell"), then the skill generates production-quality frame decks.

## Core Philosophy

1. **Zero Build Dependencies** -- Single HTML files with inline CSS/JS. KaTeX loaded via CDN for equations. No npm, no build tools.
2. **Show, Don't Tell** -- People don't know what they want until they see it. Generate visual theme previews for academic users.
3. **Academic Authenticity** -- Beamer-inspired structure, Computer Modern typography, proper theorem environments. Not PowerPoint-with-serif-fonts.
4. **Production Quality** -- Accessible, responsive, well-commented code.
5. **Viewport Fitting (CRITICAL)** -- Every frame MUST fit exactly within the viewport. No scrolling within frames, ever. This is non-negotiable.

---

## CRITICAL: Viewport Fitting Requirements

**This section is mandatory for ALL presentations. Every frame must be fully visible without scrolling on any screen size.**

### The Golden Rule

```text
Each frame = exactly one viewport height (100vh/100dvh)
Content overflows? -> Split into multiple frames or reduce content
Never scroll within a frame.
```

### Content Density Limits

To guarantee viewport fitting, enforce these limits per frame:

| Frame Type | Maximum Content |
|------------|-----------------|
| Title Frame | 1 title + 1 subtitle + author/institute/date block |
| Content Frame | 1 heading + 4-5 bullet points OR 1 heading + 2 short paragraphs |
| Theorem/Proof Frame | 1 theorem box (max 4 lines) + 1 proof sketch (max 5 lines) |
| Equation Frame | 1 heading + 1-3 display equations with optional annotation |
| Algorithm Frame | 1 heading + max 12 lines of pseudocode |
| Definition Frame | 1 heading + 1-2 definition boxes (max 3 lines each) |
| Citation/References Frame | 1 heading + max 8 reference entries |
| Section Divider Frame | 1 section number + 1 section title + optional outline |

**If content exceeds these limits -> Split into multiple frames**

### Required CSS Architecture

Every presentation MUST include this base CSS for viewport fitting:

```css
/* ===========================================
   VIEWPORT FITTING: MANDATORY BASE STYLES
   These styles MUST be included in every presentation.
   They ensure frames fit exactly in the viewport.
   =========================================== */

/* 1. Lock html/body to viewport */
html, body {
    height: 100%;
    overflow-x: hidden;
}

html {
    scroll-snap-type: y mandatory;
    scroll-behavior: smooth;
}

/* 2. Each frame = exact viewport height */
.frame {
    width: 100vw;
    height: 100vh;
    height: 100dvh; /* Dynamic viewport height for mobile browsers */
    overflow: hidden; /* CRITICAL: Prevent ANY overflow */
    scroll-snap-align: start;
    display: flex;
    flex-direction: column;
    position: relative;
}

/* 3. Content container with flex for centering */
.frame-content {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: center;
    max-height: 100%;
    overflow: hidden; /* Double-protection against overflow */
    padding: var(--frame-padding);
}

/* 4. ALL typography uses clamp() for responsive scaling */
:root {
    /* Titles scale from mobile to desktop */
    --title-size: clamp(1.5rem, 5vw, 3.5rem);
    --h2-size: clamp(1.25rem, 3.5vw, 2.25rem);
    --h3-size: clamp(1rem, 2.5vw, 1.75rem);

    /* Body text */
    --body-size: clamp(0.75rem, 1.5vw, 1.125rem);
    --small-size: clamp(0.65rem, 1vw, 0.875rem);

    /* Spacing scales with viewport */
    --frame-padding: clamp(1rem, 4vw, 4rem);
    --content-gap: clamp(0.5rem, 2vw, 2rem);
    --element-gap: clamp(0.25rem, 1vw, 1rem);

    /* Frame chrome */
    --chrome-height-top: clamp(2.5rem, 5vh, 3.5rem);
    --chrome-height-bottom: clamp(1.5rem, 3vh, 2.5rem);
}

/* 5. Theorem/definition/proof boxes use viewport-relative max sizes */
.theorem-box, .definition-box, .example-box, .lemma-box, .corollary-box {
    border-left: 4px solid var(--theorem-border);
    background: var(--theorem-bg);
    padding: clamp(0.5rem, 1.5vw, 1rem) clamp(0.75rem, 2vw, 1.5rem);
    margin: clamp(0.25rem, 0.5vw, 0.5rem) 0;
    max-height: min(40vh, 350px);
    overflow: hidden;
}

.theorem-box .env-title, .definition-box .env-title {
    font-weight: 700;
    font-size: var(--body-size);
    color: var(--theorem-border);
    margin-bottom: clamp(0.2rem, 0.5vw, 0.5rem);
}

.proof-box {
    border-left: 2px solid var(--proof-border);
    background: var(--proof-bg);
    padding: clamp(0.4rem, 1vw, 0.75rem) clamp(0.75rem, 2vw, 1.5rem);
    font-style: italic;
}

.proof-box::after {
    content: '\25A1'; /* QED square */
    float: right;
    font-style: normal;
}

.algorithm-box {
    border: 1px solid var(--theorem-border);
    background: var(--proof-bg);
    padding: clamp(0.5rem, 1.5vw, 1rem);
    font-family: var(--font-mono);
    font-size: var(--small-size);
    line-height: 1.6;
}

/* 6. Lists auto-scale with viewport */
.item-list, .enum-list {
    gap: clamp(0.4rem, 1vh, 1rem);
}

.item-list li, .enum-list li {
    font-size: var(--body-size);
    line-height: 1.4;
}

/* 7. Columns adapt to available space */
.columns {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(min(100%, 250px), 1fr));
    gap: clamp(0.5rem, 1.5vw, 1rem);
}

/* 8. Images constrained to viewport */
img, .image-container {
    max-width: 100%;
    max-height: min(50vh, 400px);
    object-fit: contain;
}

/* ===========================================
   RESPONSIVE BREAKPOINTS
   Aggressive scaling for smaller viewports
   =========================================== */

/* Short viewports (< 700px height) */
@media (max-height: 700px) {
    :root {
        --frame-padding: clamp(0.75rem, 3vw, 2rem);
        --content-gap: clamp(0.4rem, 1.5vw, 1rem);
        --title-size: clamp(1.25rem, 4.5vw, 2.5rem);
        --h2-size: clamp(1rem, 3vw, 1.75rem);
    }
}

/* Very short viewports (< 600px height) */
@media (max-height: 600px) {
    :root {
        --frame-padding: clamp(0.5rem, 2.5vw, 1.5rem);
        --content-gap: clamp(0.3rem, 1vw, 0.75rem);
        --title-size: clamp(1.1rem, 4vw, 2rem);
        --body-size: clamp(0.7rem, 1.2vw, 0.95rem);
    }

    /* Hide non-essential elements */
    .frame-nav, .keyboard-hint, .decorative {
        display: none;
    }
}

/* Extremely short (landscape phones, < 500px height) */
@media (max-height: 500px) {
    :root {
        --frame-padding: clamp(0.4rem, 2vw, 1rem);
        --title-size: clamp(1rem, 3.5vw, 1.5rem);
        --h2-size: clamp(0.9rem, 2.5vw, 1.25rem);
        --body-size: clamp(0.65rem, 1vw, 0.85rem);
    }
}

/* Narrow viewports (< 600px width) */
@media (max-width: 600px) {
    :root {
        --title-size: clamp(1.25rem, 7vw, 2.5rem);
    }

    /* Stack columns vertically */
    .columns {
        grid-template-columns: 1fr;
    }
}

/* ===========================================
   REDUCED MOTION
   Respect user preferences
   =========================================== */
@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        transition-duration: 0.2s !important;
    }

    html {
        scroll-behavior: auto;
    }
}
```

### Overflow Prevention Checklist

Before generating any presentation, mentally verify:

1. Every `.frame` has `height: 100vh; height: 100dvh; overflow: hidden;`
2. All font sizes use `clamp(min, preferred, max)`
3. All spacing uses `clamp()` or viewport units
4. Content containers have `max-height` constraints
5. Images have `max-height: min(50vh, 400px)` or similar
6. Columns use `auto-fit` with `minmax()` for responsive layout
7. Breakpoints exist for heights: 700px, 600px, 500px
8. No fixed pixel heights on content elements
9. Content per frame respects density limits
10. Theorem/proof boxes have `max-height` constraints

### When Content Does Not Fit

If you find yourself with too much content:

**DO:**
- Split into multiple frames
- Reduce bullet points (max 4-5 per frame)
- Shorten text (aim for 1-2 lines per bullet)
- Break long proofs across "Proof (cont.)" frames
- Create a "continued" frame

**DO NOT:**
- Reduce font size below readable limits
- Remove padding/spacing entirely
- Allow any scrolling
- Cram content to fit

### Testing Viewport Fit

After generating, recommend the user test at these sizes:
- Desktop: 1920x1080, 1440x900, 1280x720
- Tablet: 1024x768, 768x1024 (portrait)
- Mobile: 375x667, 414x896
- Landscape phone: 667x375, 896x414

---

## Phase 0: Detect Mode

First, determine what the user wants:

**Mode A: New Academic Presentation**
- User wants to create slides from scratch for a talk, lecture, or defense
- Proceed to Phase 1 (Content Discovery)

**Mode B: PPT/PDF Conversion**
- User has an existing PowerPoint or PDF to convert to web slides
- Proceed to Phase 4 (PPT Extraction)

**Mode C: Existing Presentation Enhancement**
- User has an HTML presentation and wants to improve it
- Read the existing file, understand the structure, then enhance

---

## Phase 1: Content Discovery (New Presentations)

Before designing, understand the content. Ask via AskUserQuestion:

### Step 1.1: Presentation Context

**Question 1: Purpose**
- Header: "Purpose"
- Question: "What is this presentation for?"
- Options:
  - "Conference talk" -- Presenting research at a conference (ICML, NeurIPS, STOC, etc.)
  - "Lecture/Course" -- Teaching a class, course lecture, or tutorial
  - "Seminar/Workshop" -- Lab meeting, reading group, or invited talk
  - "Thesis Defense" -- Master's or doctoral thesis/dissertation defense

**Question 2: Length**
- Header: "Length"
- Question: "Approximately how many frames?"
- Options:
  - "Short (5-10)" -- Lightning talk, brief seminar
  - "Medium (10-20)" -- Standard conference talk
  - "Long (20+)" -- Full lecture, thesis defense

**Question 3: Content Readiness**
- Header: "Content"
- Question: "Do you have the content ready, or do you need help structuring it?"
- Options:
  - "I have all content ready" -- Just need to design the presentation
  - "I have rough notes" -- Need help organizing into frames
  - "I have a topic only" -- Need help creating the full outline

**Question 4: Academic Content Types**
- Header: "Content Types"
- Question: "What types of academic content will your presentation include?"
- multiSelect: true
- Options:
  - "Theorems/Proofs/Lemmas" -- Formal mathematical statements and their proofs
  - "Equations/Derivations" -- Display math, inline equations, step-by-step derivations
  - "Algorithms/Pseudocode" -- Algorithm descriptions, pseudocode blocks
  - "Citations/References" -- Bibliography entries, paper references

**Question 5: Field**
- Header: "Field"
- Question: "What academic field is this for?"
- Options:
  - "Mathematics/Statistics" -- Pure math, applied math, probability, statistics
  - "Computer Science/Engineering" -- Systems, ML, theory, hardware
  - "Natural Sciences" -- Physics, chemistry, biology
  - "Humanities/Social Sciences" -- Economics, linguistics, philosophy

If user has content, ask them to share it (text, bullet points, paper draft, etc.).

---

## Phase 2: Style Discovery (Visual Exploration)

**CRITICAL: This is the "show, don't tell" phase.**

Most people can't articulate design preferences in words. Instead of asking "do you want Madrid or Metropolis?", we generate mini-previews and let them react.

### How Users Choose Presets

Users can select a theme in **two ways**:

**Option A: Guided Discovery (Default)**
- User answers mood questions
- Skill generates 3 preview files based on their answers
- User views previews in browser and picks their favorite
- This is best for users who do not have a specific style in mind

**Option B: Direct Selection**
- If user already knows what they want, they can request a theme by name
- Example: "Use the Madrid theme" or "I want something like Metropolis"
- Skip to Phase 3 immediately

**Available Themes:**

| Preset | Vibe | Best For |
|--------|------|----------|
| Madrid | Classic Beamer, blue bars | Conference talks, general academic |
| Berlin | Sidebar navigation, structured | Course lectures, structured talks |
| Copenhagen | Clean minimal header | Seminars, workshops |
| Warsaw | Bold gradient bars | Keynotes, plenary talks |
| Metropolis | Modern sans-serif | CS/tech conferences |
| Classic Serif | Traditional LaTeX look | Mathematics, formal proofs |
| Cambridge | University formal, green+gold | Thesis defense, formal events |
| Lecture Notes | Warm, pedagogical | Teaching, tutorials |
| Technical Report | Engineering/CS style | Technical presentations |
| Thesis Defense | Formal, institution-branded | Thesis/dissertation defense |
| Seminar | Informal, relaxed | Lab meetings, reading groups |
| Journal Article | Paper-style layout | Research presentations |

### Step 2.0: Style Path Selection

First, ask how the user wants to choose their theme:

**Question: Style Selection Method**
- Header: "Theme"
- Question: "How would you like to choose your presentation theme?"
- Options:
  - "Show me options" -- Generate 3 previews based on my needs (recommended for most users)
  - "I know what I want" -- Let me pick from the theme list directly

**If "Show me options"** -> Continue to Step 2.1 (Mood Selection)

**If "I know what I want"** -> Show theme picker:

**Question: Pick a Theme**
- Header: "Theme"
- Question: "Which theme would you like to use?"
- Options:
  - "Madrid" -- Classic Beamer with blue bars, the standard academic look
  - "Berlin" -- Sidebar navigation for structured, multi-section talks
  - "Copenhagen" -- Clean minimal header, good for focused seminars
  - "Warsaw" -- Bold gradient bars for keynotes and plenary talks
  - "Metropolis" -- Modern sans-serif for CS and tech conferences
  - "Classic Serif" -- Traditional LaTeX look for mathematics and formal proofs
  - "Cambridge" -- University formal with green and gold accents
  - "Lecture Notes" -- Warm and pedagogical for teaching
  - "Technical Report" -- Engineering and CS style
  - "Thesis Defense" -- Formal, institution-branded
  - "Seminar" -- Informal and relaxed for lab meetings
  - "Journal Article" -- Paper-style layout for research talks

(If user picks one, skip to Phase 3.)

### Step 2.1: Mood Selection (Guided Discovery)

**Question 1: Feeling**
- Header: "Tone"
- Question: "What tone should your presentation convey?"
- Options:
  - "Authoritative/Rigorous" -- Formal, precise, mathematically grounded
  - "Clear/Pedagogical" -- Approachable, well-structured, easy to follow
  - "Modern/Minimal" -- Clean, contemporary, distraction-free
  - "Classic/Traditional" -- Timeless, scholarly, university-style
- multiSelect: true (can choose up to 2)

**Mood-to-Theme Mapping:**

| Mood | Theme Options |
|------|---------------|
| Authoritative/Rigorous | Madrid, Warsaw, Classic Serif, Thesis Defense |
| Clear/Pedagogical | Berlin, Copenhagen, Lecture Notes |
| Modern/Minimal | Metropolis, Technical Report, Seminar |
| Classic/Traditional | Cambridge, Classic Serif, Journal Article |

### Step 2.2: Generate Theme Previews

Based on their mood selection, generate **3 distinct theme previews** as mini HTML files in a temporary directory. Each preview should be a single title frame showing:

- Typography (font choices, heading/body hierarchy)
- Color palette (background, header/footer chrome, accent colors)
- Frame structure (header bar, footer bar, content area)
- Theorem environment styling (a sample theorem box)
- Overall academic feel

**IMPORTANT: Every preview must feel academically authentic:**
- Use Computer Modern or Source Serif 4 as primary fonts
- Include header/footer chrome (short title, section title, frame number)
- Show a sample theorem or definition box
- Include a sample inline equation rendered with KaTeX
- Muted, professional color palettes (no neon, no gradients for decoration)

### Step 2.3: Present Previews

Create the previews in: `.claude-design/frame-previews/`

```text
.claude-design/frame-previews/
    style-a.html   # First theme option
    style-b.html   # Second theme option
    style-c.html   # Third theme option
```

Each preview file should be:
- Self-contained (inline CSS/JS, KaTeX via CDN)
- A single title frame plus one content frame with a theorem box
- Showing the header/footer chrome style
- Around 80-120 lines, not a full presentation

Present to user:
```text
I have created 3 theme previews for you to compare:

**Theme A: [Name]** -- [1 sentence description]
**Theme B: [Name]** -- [1 sentence description]
**Theme C: [Name]** -- [1 sentence description]

Open each file to see them in action:
- .claude-design/frame-previews/style-a.html
- .claude-design/frame-previews/style-b.html
- .claude-design/frame-previews/style-c.html

Take a look and tell me:
1. Which theme resonates most?
2. What do you like about it?
3. Anything you would change?
```

Then use AskUserQuestion:

**Question: Pick Your Theme**
- Header: "Theme"
- Question: "Which theme preview do you prefer?"
- Options:
  - "Theme A: [Name]" -- [Brief description]
  - "Theme B: [Name]" -- [Brief description]
  - "Theme C: [Name]" -- [Brief description]
  - "Mix elements" -- Combine aspects from different themes

If "Mix elements", ask for specifics.

---

## Phase 3: Generate Presentation

Now generate the full presentation based on:
- Content from Phase 1
- Theme from Phase 2

### File Structure

For single presentations:
```text
presentation.html    # Self-contained presentation
assets/              # Images, if any
```

For projects with multiple presentations:
```text
[presentation-name].html
[presentation-name]-assets/
```

### HTML Architecture

Follow this structure for all presentations:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Presentation Title</title>

    <!-- Academic Fonts -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/computer-modern@0.1.2/cmu-serif.css">
    <!-- OR Google Fonts alternative -->
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Source+Serif+4:opsz,wght@8..60,400;8..60,600;8..60,700&display=swap">

    <!-- KaTeX for equation rendering -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16/dist/katex.min.css">
    <script defer src="https://cdn.jsdelivr.net/npm/katex@0.16/dist/katex.min.js"></script>
    <script defer src="https://cdn.jsdelivr.net/npm/katex@0.16/dist/contrib/auto-render.min.js"
        onload="renderMathInElement(document.body, {delimiters: [{left: '$$', right: '$$', display: true},{left: '$', right: '$', display: false}]});"></script>

    <style>
        /* ===========================================
           CSS CUSTOM PROPERTIES (THEME)
           Easy to modify: change these to change the whole look.
           =========================================== */
        :root {
            /* Typography */
            --font-heading: 'CMU Serif', 'Source Serif 4', Georgia, serif;
            --font-body: 'CMU Serif', 'Source Serif 4', Georgia, serif;
            --font-mono: 'CMU Typewriter Text', 'Source Code Pro', 'Courier New', monospace;

            /* Frame Chrome */
            --header-bg: #2c3e50;
            --header-fg: #ffffff;
            --footer-bg: #2c3e50;
            --footer-fg: #ffffff;

            /* Environment Colors */
            --theorem-bg: #e8f4f8;
            --theorem-border: #2980b9;
            --definition-bg: #fdf2e9;
            --definition-border: #e67e22;
            --proof-bg: #f9f9f9;
            --proof-border: #95a5a6;
            --example-bg: #eafaf1;
            --example-border: #27ae60;
            --alert-bg: #fdedec;
            --alert-border: #e74c3c;

            /* Viewport fitting vars (must use clamp) */
            --title-size: clamp(1.5rem, 5vw, 3.5rem);
            --h2-size: clamp(1.25rem, 3.5vw, 2.25rem);
            --body-size: clamp(0.75rem, 1.5vw, 1.125rem);
            --small-size: clamp(0.65rem, 1vw, 0.875rem);
            --frame-padding: clamp(1rem, 4vw, 4rem);
            --content-gap: clamp(0.5rem, 2vw, 2rem);
            --h3-size: clamp(1rem, 2.5vw, 1.75rem);
            --element-gap: clamp(0.25rem, 1vw, 1rem);

            /* Frame chrome */
            --chrome-height-top: clamp(2.5rem, 5vh, 3.5rem);
            --chrome-height-bottom: clamp(1.5rem, 3vh, 2.5rem);

            /* Animation */
            --ease-subtle: ease-in-out;
            --duration-normal: 0.3s;
        }

        /* ===========================================
           BASE STYLES
           =========================================== */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        html {
            scroll-behavior: smooth;
            scroll-snap-type: y mandatory;
            height: 100%;
        }

        body {
            font-family: var(--font-body);
            background: #ffffff;
            color: #2c3e50;
            overflow-x: hidden;
            height: 100%;
        }

        /* ===========================================
           FRAME CONTAINER
           CRITICAL: Each frame MUST fit exactly in viewport.
           - Use height: 100vh (NOT min-height).
           - Use overflow: hidden to prevent scroll.
           - Content must scale with clamp() values.
           =========================================== */
        .frame {
            width: 100vw;
            height: 100vh; /* Exact viewport height, no scrolling. */
            height: 100dvh; /* Dynamic viewport height for mobile. */
            scroll-snap-align: start;
            display: flex;
            flex-direction: column;
            position: relative;
            overflow: hidden; /* Prevent any content overflow. */
        }

        /* Content wrapper that prevents overflow. */
        .frame-content {
            flex: 1;
            display: flex;
            flex-direction: column;
            justify-content: center;
            max-height: 100%;
            overflow: hidden;
            padding: var(--frame-padding);
            padding-top: calc(var(--chrome-height-top) + var(--frame-padding));
            padding-bottom: calc(var(--chrome-height-bottom) + var(--frame-padding));
        }

        /* ===========================================
           FRAME HEADER AND FOOTER (Beamer-style chrome)
           =========================================== */
        .frame-header {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            height: var(--chrome-height-top);
            background: var(--header-bg);
            color: var(--header-fg);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 var(--frame-padding);
            font-size: var(--small-size);
            z-index: 100;
        }

        .frame-footer {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            height: var(--chrome-height-bottom);
            background: var(--footer-bg);
            color: var(--footer-fg);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 var(--frame-padding);
            font-size: var(--small-size);
            z-index: 100;
        }

        /* ===========================================
           TITLE FRAME
           =========================================== */
        .title-frame .frame-content {
            text-align: center;
            align-items: center;
        }

        .title-frame h1 {
            font-family: var(--font-heading);
            font-size: var(--title-size);
            font-weight: 700;
            line-height: 1.2;
            margin-bottom: var(--content-gap);
        }

        .title-frame .subtitle {
            font-size: var(--h2-size);
            color: var(--theorem-border);
            margin-bottom: var(--content-gap);
        }

        .author-block {
            font-size: var(--body-size);
            line-height: 1.6;
        }

        .author-block .author {
            font-weight: 600;
        }

        .author-block .institute {
            font-style: italic;
        }

        /* ===========================================
           SECTION DIVIDER FRAME
           =========================================== */
        .section-frame .frame-content {
            text-align: center;
            align-items: center;
        }

        .section-frame .section-number {
            font-size: var(--title-size);
            font-weight: 700;
            color: var(--theorem-border);
            opacity: 0.3;
        }

        /* ===========================================
           THEOREM ENVIRONMENTS
           Beamer-style environments for formal content.
           =========================================== */
        .theorem-box, .definition-box, .example-box, .lemma-box, .corollary-box {
            border-left: 4px solid var(--theorem-border);
            background: var(--theorem-bg);
            padding: clamp(0.5rem, 1.5vw, 1rem) clamp(0.75rem, 2vw, 1.5rem);
            margin: clamp(0.25rem, 0.5vw, 0.5rem) 0;
            max-height: min(40vh, 350px);
            overflow: hidden;
        }

        .definition-box {
            border-left-color: var(--definition-border);
            background: var(--definition-bg);
        }

        .example-box {
            border-left-color: var(--example-border);
            background: var(--example-bg);
        }

        .theorem-box .env-title,
        .definition-box .env-title,
        .example-box .env-title,
        .lemma-box .env-title,
        .corollary-box .env-title {
            font-weight: 700;
            font-size: var(--body-size);
            color: var(--theorem-border);
            margin-bottom: clamp(0.2rem, 0.5vw, 0.5rem);
        }

        .definition-box .env-title {
            color: var(--definition-border);
        }

        .example-box .env-title {
            color: var(--example-border);
        }

        .proof-box {
            border-left: 2px solid var(--proof-border);
            background: var(--proof-bg);
            padding: clamp(0.4rem, 1vw, 0.75rem) clamp(0.75rem, 2vw, 1.5rem);
            font-style: italic;
            margin: clamp(0.25rem, 0.5vw, 0.5rem) 0;
        }

        .proof-box::after {
            content: '\25A1'; /* QED square. */
            float: right;
            font-style: normal;
        }

        .algorithm-box {
            border: 1px solid var(--theorem-border);
            background: var(--proof-bg);
            padding: clamp(0.5rem, 1.5vw, 1rem);
            font-family: var(--font-mono);
            font-size: var(--small-size);
            line-height: 1.6;
            margin: clamp(0.25rem, 0.5vw, 0.5rem) 0;
        }

        /* ===========================================
           EQUATION BLOCK
           For centered display equations with annotations.
           =========================================== */
        .equation-block {
            text-align: center;
            margin: var(--content-gap) 0;
            font-size: clamp(1rem, 2vw, 1.5rem);
        }

        /* ===========================================
           CITATION BLOCK
           For reference lists.
           =========================================== */
        .citation-block {
            font-size: var(--small-size);
            line-height: 1.5;
            padding-left: 2em;
            text-indent: -2em;
        }

        .citation-block p {
            margin-bottom: clamp(0.15rem, 0.3vw, 0.3rem);
        }

        /* ===========================================
           COLUMNS (replaces .grid)
           Multi-column layouts for side-by-side content.
           =========================================== */
        .columns {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(min(100%, 250px), 1fr));
            gap: clamp(0.5rem, 1.5vw, 1rem);
        }

        .columns.two-col {
            grid-template-columns: 1fr 1fr;
        }

        .columns.three-col {
            grid-template-columns: 1fr 1fr 1fr;
        }

        /* ===========================================
           ITEM LIST AND ENUM LIST
           =========================================== */
        .item-list {
            list-style: none;
            padding: 0;
        }

        .item-list li {
            font-size: var(--body-size);
            line-height: 1.4;
            padding-left: 1.5em;
            position: relative;
            margin-bottom: clamp(0.2rem, 0.5vw, 0.5rem);
        }

        .item-list li::before {
            content: '\25B8'; /* Small right triangle, similar to Beamer. */
            position: absolute;
            left: 0;
            color: var(--theorem-border);
            font-weight: bold;
        }

        .enum-list {
            padding-left: 1.5em;
        }

        .enum-list li {
            font-size: var(--body-size);
            line-height: 1.4;
            margin-bottom: clamp(0.2rem, 0.5vw, 0.5rem);
        }

        /* ===========================================
           RESPONSIVE BREAKPOINTS
           Adjust content for different screen sizes.
           =========================================== */
        @media (max-height: 700px) {
            :root {
                --frame-padding: clamp(0.75rem, 3vw, 2rem);
                --content-gap: clamp(0.4rem, 1.5vw, 1rem);
                --title-size: clamp(1.25rem, 4.5vw, 2.5rem);
                --h2-size: clamp(1rem, 3vw, 1.75rem);
            }
        }

        @media (max-height: 600px) {
            :root {
                --frame-padding: clamp(0.5rem, 2.5vw, 1.5rem);
                --content-gap: clamp(0.3rem, 1vw, 0.75rem);
                --title-size: clamp(1.1rem, 4vw, 2rem);
                --body-size: clamp(0.7rem, 1.2vw, 0.95rem);
            }

            .frame-nav, .keyboard-hint, .decorative {
                display: none;
            }
        }

        @media (max-height: 500px) and (orientation: landscape) {
            :root {
                --title-size: clamp(1rem, 3.5vw, 1.5rem);
                --frame-padding: clamp(0.4rem, 2vw, 1rem);
            }
        }

        @media (max-width: 600px) {
            :root {
                --title-size: clamp(1.25rem, 7vw, 2.5rem);
            }

            .columns {
                grid-template-columns: 1fr;
            }
        }

        /* ===========================================
           ANIMATIONS
           Subtle fade only. Academic presentations
           do not need flashy entrance effects.
           =========================================== */
        .reveal {
            opacity: 0;
            transition: opacity var(--duration-normal) var(--ease-subtle);
        }

        .frame.visible .reveal {
            opacity: 1;
        }

        /* Stagger children subtly. */
        .reveal:nth-child(1) { transition-delay: 0.05s; }
        .reveal:nth-child(2) { transition-delay: 0.1s; }
        .reveal:nth-child(3) { transition-delay: 0.15s; }
        .reveal:nth-child(4) { transition-delay: 0.2s; }

        /* ===========================================
           PROGRESSIVE DISCLOSURE (Beamer \pause equivalent)
           Elements with data-pause="N" are hidden until
           the user advances to pause step N.
           =========================================== */
        [data-pause] {
            opacity: 0;
            transition: opacity var(--duration-normal) var(--ease-subtle);
        }

        [data-pause].paused-visible {
            opacity: 1;
        }

        /* ===========================================
           REDUCED MOTION
           Respect user preferences.
           =========================================== */
        @media (prefers-reduced-motion: reduce) {
            .reveal {
                transition: opacity 0.2s ease;
            }

            html {
                scroll-behavior: auto;
            }
        }

        /* ... more theme-specific styles ... */
    </style>
</head>
<body>
    <!-- Frame Header (Beamer-style) -->
    <header class="frame-header">
        <span class="short-title">Paper Title</span>
        <span class="section-title">Current Section</span>
    </header>

    <!-- Frames -->
    <section class="frame title-frame">
        <div class="frame-content">
            <h1 class="reveal">Presentation Title</h1>
            <p class="reveal subtitle">Subtitle or Conference Name</p>
            <div class="author-block reveal">
                <p class="author">Author Name</p>
                <p class="institute">University / Institution</p>
                <p class="date">Conference, Date</p>
            </div>
        </div>
    </section>

    <section class="frame section-frame">
        <div class="frame-content">
            <span class="section-number">1</span>
            <h2>Section Title</h2>
        </div>
    </section>

    <section class="frame">
        <div class="frame-content">
            <h2 class="reveal">Frame Title</h2>
            <div class="theorem-box reveal" data-pause="1">
                <span class="env-title">Theorem 1 (Main Result)</span>
                <p>The theorem statement using $inline math$ and display:</p>
                <p>$$\int_a^b f(x)\,dx = F(b) - F(a)$$</p>
            </div>
            <div class="proof-box reveal" data-pause="2">
                <span class="env-title">Proof.</span>
                <p>By the fundamental theorem of calculus...</p>
            </div>
        </div>
    </section>

    <!-- More frames... -->

    <!-- Frame Footer -->
    <footer class="frame-footer">
        <span class="author-short">A. Name</span>
        <span class="title-short">Short Title</span>
        <span class="frame-number"></span>
    </footer>

    <script>
        /* ===========================================
           ACADEMIC PRESENTATION CONTROLLER
           Handles frame navigation, progressive disclosure
           (pause), animations, and keyboard/touch input.
           =========================================== */

        class AcademicPresentation {
            constructor() {
                this.frames = document.querySelectorAll('.frame');
                this.currentFrame = 0;
                this.currentPause = 0;
                this.init();
            }

            init() {
                this.setupNavigation();
                this.setupIntersectionObserver();
                this.setupProgressiveDisclosure();
                this.updateFrameCounter();
                this.showFrame(0);
            }

            /* ----- Navigation ----- */

            setupNavigation() {
                /* Keyboard: arrows, space, page up/down. */
                document.addEventListener('keydown', (e) => {
                    switch(e.key) {
                        case 'ArrowRight':
                        case 'ArrowDown':
                        case ' ':
                        case 'PageDown':
                            e.preventDefault();
                            this.next();
                            break;
                        case 'ArrowLeft':
                        case 'ArrowUp':
                        case 'PageUp':
                            e.preventDefault();
                            this.prev();
                            break;
                        case 'Home':
                            e.preventDefault();
                            this.goToFrame(0);
                            break;
                        case 'End':
                            e.preventDefault();
                            this.goToFrame(this.frames.length - 1);
                            break;
                    }
                });

                /* Touch/swipe support. */
                let touchStartY = 0;
                document.addEventListener('touchstart', (e) => {
                    touchStartY = e.touches[0].clientY;
                });
                document.addEventListener('touchend', (e) => {
                    const deltaY = touchStartY - e.changedTouches[0].clientY;
                    if (Math.abs(deltaY) > 50) {
                        deltaY > 0 ? this.next() : this.prev();
                    }
                });

                /* Mouse wheel. */
                let wheelTimeout;
                document.addEventListener('wheel', (e) => {
                    e.preventDefault();
                    clearTimeout(wheelTimeout);
                    const delta = e.deltaY;
                    wheelTimeout = setTimeout(() => {
                        delta > 0 ? this.next() : this.prev();
                    }, 50);
                }, { passive: false });
            }

            /* ----- Frame Display ----- */

            next() {
                /* Advance pause first, then next frame. */
                if (this.advancePause()) return;
                if (this.currentFrame < this.frames.length - 1) {
                    this.showFrame(this.currentFrame + 1);
                }
            }

            prev() {
                if (this.currentFrame > 0) {
                    this.showFrame(this.currentFrame - 1);
                }
            }

            goToFrame(index) {
                this.showFrame(index);
            }

            showFrame(index) {
                /* Reset pause state on the frame being left.
                   Design choice: backward navigation resets to step 0,
                   matching Beamer's default \pause behavior. */
                const leaving = this.frames[this.currentFrame];
                if (leaving) {
                    leaving.querySelectorAll('[data-pause]').forEach(el =>
                        el.classList.remove('paused-visible'));
                }
                this.currentFrame = index;
                this.currentPause = 0;
                this.frames[index].scrollIntoView({ behavior: 'smooth' });
                this.updateFrameCounter();
            }

            /* ----- Progressive Disclosure (\pause) ----- */

            setupProgressiveDisclosure() {
                this.frames.forEach(frame => {
                    frame.querySelectorAll('[data-pause]').forEach(el => {
                        el.classList.remove('paused-visible');
                    });
                });
            }

            advancePause() {
                const frame = this.frames[this.currentFrame];
                const pauseElements = frame.querySelectorAll('[data-pause]');
                const nextPause = this.currentPause + 1;
                let found = false;

                pauseElements.forEach(el => {
                    if (parseInt(el.dataset.pause) === nextPause) {
                        el.classList.add('paused-visible');
                        found = true;
                    }
                });

                if (found) {
                    this.currentPause = nextPause;
                    return true; /* Consumed the advance. */
                }
                return false; /* No more pauses, advance frame. */
            }

            /* ----- Intersection Observer ----- */

            setupIntersectionObserver() {
                const observer = new IntersectionObserver((entries) => {
                    entries.forEach(entry => {
                        if (entry.isIntersecting) {
                            entry.target.classList.add('visible');
                        }
                    });
                }, { threshold: 0.5 });

                this.frames.forEach(frame => observer.observe(frame));
            }

            /* ----- Frame Counter ----- */

            updateFrameCounter() {
                const counter = document.querySelector('.frame-number');
                if (counter) {
                    counter.textContent = `${this.currentFrame + 1} / ${this.frames.length}`;
                }
            }
        }

        /* Initialize presentation. */
        new AcademicPresentation();
    </script>
</body>
</html>
```

### Required JavaScript Features

Every presentation should include:

1. **AcademicPresentation Class** -- Main controller
   - Keyboard navigation (arrows, space, page up/down, home/end)
   - Touch/swipe support
   - Mouse wheel navigation
   - Progress indicator (frame counter "X / Y" in footer)

2. **Intersection Observer** -- For scroll-triggered animations
   - Add `.visible` class when frames enter viewport
   - Trigger CSS fade-in efficiently

3. **Progressive Disclosure** -- Beamer `\pause` equivalent
   - Elements with `data-pause="N"` are hidden initially
   - Each "next" press reveals the next pause group before advancing the frame
   - Allows step-by-step theorem proofs, equation derivations, algorithm walkthroughs

4. **Frame Counter** -- "Frame X of Y" display in the footer

### Code Quality Requirements

**Comments:**
Every section should have clear comments explaining:
- What it does
- Why it exists
- How to modify it

```javascript
/* ===========================================
   PROGRESSIVE DISCLOSURE
   Implements Beamer's \pause command for HTML.
   - Elements with data-pause="N" are hidden initially.
   - Pressing next reveals pause group N before advancing.
   - This allows step-by-step reveals within a single frame.
   =========================================== */
```

**Accessibility:**
- Semantic HTML (`<section>`, `<nav>`, `<header>`, `<footer>`)
- Keyboard navigation works
- ARIA labels where needed
- Reduced motion support

```css
@media (prefers-reduced-motion: reduce) {
    .reveal {
        transition: opacity 0.2s ease;
    }
}
```

**Responsive and Viewport Fitting (CRITICAL):**

**See the "CRITICAL: Viewport Fitting Requirements" section above for complete CSS and guidelines.**

Quick reference:
- Every `.frame` must have `height: 100vh; height: 100dvh; overflow: hidden;`
- All typography and spacing must use `clamp()`
- Respect content density limits (max 4-5 bullets, max 4-line theorems, etc.)
- Include breakpoints for heights: 700px, 600px, 500px
- When content does not fit -> split into multiple frames, never scroll

---

## Phase 4: PPT Conversion

When converting PowerPoint files:

### Step 4.1: Extract Content

Use Python with `python-pptx` to extract:

```python
from pptx import Presentation
from pptx.util import Inches, Pt
import json
import os
import base64

def extract_pptx(file_path, output_dir):
    """
    Extract all content from a PowerPoint file.
    Returns a JSON structure with slides, text, images, and equation objects.
    """
    prs = Presentation(file_path)
    frames_data = []

    # Create assets directory.
    assets_dir = os.path.join(output_dir, 'assets')
    os.makedirs(assets_dir, exist_ok=True)

    for frame_num, slide in enumerate(prs.slides):
        frame_data = {
            'number': frame_num + 1,
            'title': '',
            'content': [],
            'images': [],
            'equations': [],
            'notes': ''
        }

        for shape in slide.shapes:
            # Extract title.
            if shape.has_text_frame:
                if shape == slide.shapes.title:
                    frame_data['title'] = shape.text
                else:
                    frame_data['content'].append({
                        'type': 'text',
                        'content': shape.text
                    })

            # Extract images.
            if shape.shape_type == 13:  # Picture.
                image = shape.image
                image_bytes = image.blob
                image_ext = image.ext
                image_name = f"frame{frame_num + 1}_img{len(frame_data['images']) + 1}.{image_ext}"
                image_path = os.path.join(assets_dir, image_name)

                with open(image_path, 'wb') as f:
                    f.write(image_bytes)

                frame_data['images'].append({
                    'path': f"assets/{image_name}",
                    'width': shape.width,
                    'height': shape.height
                })

            # Detect equation objects (OLE or EMF).
            # PowerPoint equations are often embedded as OLE objects.
            # Flag these for manual KaTeX conversion.
            if hasattr(shape, 'ole_format') or (hasattr(shape, 'image') and shape.image and shape.image.ext == 'emf'):
                frame_data['equations'].append({
                    'type': 'equation_object',
                    'note': 'Detected equation object. Convert to KaTeX manually.'
                })

        # Extract notes.
        if slide.has_notes_slide:
            notes_frame = slide.notes_slide.notes_text_frame
            frame_data['notes'] = notes_frame.text

        frames_data.append(frame_data)

    return frames_data
```

### Step 4.2: Confirm Content Structure

Present the extracted content to the user:

```text
I have extracted the following from your PowerPoint:

**Frame 1: [Title]**
- [Content summary]
- Images: [count]
- Equations detected: [count, if any]

**Frame 2: [Title]**
- [Content summary]
- Images: [count]

...

All images have been saved to the assets folder.
Note: [N] equation objects were detected. These will need manual conversion to KaTeX syntax.

Does this look correct? Should I proceed with theme selection?
```

### Step 4.3: Theme Selection

Proceed to Phase 2 (Style Discovery) with the extracted content in mind.

### Step 4.4: Generate HTML

Convert the extracted content into the chosen theme, preserving:
- All text content
- All images (referenced from assets folder)
- Frame order
- Any speaker notes (as HTML comments or separate file)
- Convert detected equations to KaTeX `$...$` or `$$...$$` syntax where possible

---

## Phase 5: Delivery

### Final Output

When the presentation is complete:

1. **Clean up temporary files**
   - Delete `.claude-design/frame-previews/` if it exists

2. **Open the presentation**
   - Use `open [filename].html` to launch in browser

3. **Provide summary**
```text
Your presentation is ready.

File: [filename].html
Theme: [Theme Name]
Frames: [count]
Equations: KaTeX rendering enabled

Navigation:
- Arrow keys or Space to advance (including progressive disclosure steps)
- Page Up / Page Down for frame-level navigation
- Home / End to jump to first or last frame
- Scroll and swipe also work
- Frame counter displayed in footer

To customize:
- Colors and fonts: Edit the :root CSS variables at the top of the file
- Theorem styles: Modify .theorem-box, .definition-box, .proof-box variables
- Header/footer chrome: Change --header-bg, --header-fg, --footer-bg, --footer-fg
- Equations: Use $...$ for inline and $$...$$ for display math (KaTeX syntax)

Would you like me to make any adjustments?
```

---

## Style Reference: Tone-to-Effect Mapping

Use this guide to match visual treatment to intended tone:

### Formal / Traditional
- Serif fonts (Computer Modern, Source Serif 4)
- Muted, institutional colors (navy, burgundy, forest green)
- Structured header/footer chrome with bars
- Generous whitespace, precise alignment
- Best themes: Madrid, Warsaw, Cambridge, Thesis Defense

### Modern / Clean
- Sans-serif fonts (Source Sans 3, Inter for body only)
- High contrast, minimal decoration
- Thin or absent header/footer bars
- Generous whitespace, content-focused
- Best themes: Metropolis, Technical Report

### Warm / Pedagogical
- Serif or rounded sans-serif fonts
- Warmer palette (cream backgrounds, warm grays, soft blues)
- Approachable, larger font sizes
- Visible structure aids (section numbers, outlines)
- Best themes: Lecture Notes, Seminar, Copenhagen

### Dense / Technical
- Compact spacing, smaller base font
- Monospace accents for code and algorithms
- Equation-heavy layouts, multi-column support
- Structured environments (theorem, definition, algorithm boxes)
- Best themes: Berlin, Classic Serif, Journal Article

---

## Animation Patterns Reference

Academic presentations use restrained, purposeful animation. The only entrance effect is a subtle fade.

### Entrance Animation

```css
/* Subtle fade -- the only entrance animation for academic use. */
.reveal {
    opacity: 0;
    transition: opacity 0.3s ease-in-out;
}

.frame.visible .reveal {
    opacity: 1;
}

/* Stagger children subtly. */
.reveal:nth-child(1) { transition-delay: 0.05s; }
.reveal:nth-child(2) { transition-delay: 0.1s; }
.reveal:nth-child(3) { transition-delay: 0.15s; }
.reveal:nth-child(4) { transition-delay: 0.2s; }
```

### Progressive Disclosure (Beamer \pause)

```css
/* Elements hidden until their pause step is reached. */
[data-pause] {
    opacity: 0;
    transition: opacity 0.3s ease-in-out;
}

[data-pause].paused-visible {
    opacity: 1;
}
```

### Reduced Motion

```css
@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        transition-duration: 0.2s !important;
    }

    html {
        scroll-behavior: auto;
    }
}
```

**Do not use any of the following in academic presentations:**
- Scale, slide, or blur entrance animations
- Gradient mesh or noise texture backgrounds
- Grid pattern overlays
- 3D tilt or parallax effects
- Custom cursor with trail
- Particle system backgrounds
- Magnetic buttons or hover effects
- Counter animations

---

## Troubleshooting

### Common Issues

**KaTeX equations not rendering:**
- Verify the KaTeX CDN scripts are loaded (check `<head>` for katex.min.js and auto-render.min.js)
- Ensure the `onload` attribute on auto-render.min.js calls `renderMathInElement`
- Check delimiter configuration: `$...$` for inline, `$$...$$` for display
- Escape special characters in HTML (use `&amp;` for `&` inside equations if needed)

**Computer Modern fonts not loading:**
- Check that the CDN link to computer-modern CSS is present and accessible
- Ensure the font-family fallback chain includes Source Serif 4 and Georgia
- Test with the browser's network inspector to confirm the CSS file loads

**Theorem box overflow:**
- Verify `max-height: min(40vh, 350px)` is set on the theorem box
- Reduce theorem statement length (max 4 lines)
- Split long proofs across multiple frames with "Proof (cont.)" headings

**Frame numbering incorrect:**
- Check that `updateFrameCounter()` is called on navigation events
- Verify the `.frame-number` element exists in the footer
- Ensure `this.frames` selects all `.frame` elements correctly

**Progressive disclosure (pause) not working:**
- Verify `data-pause="N"` attributes are on the correct elements (N = 1, 2, 3, ...)
- Check that `advancePause()` is called before frame advance in `next()`
- Ensure `.paused-visible` class is being added to revealed elements

**Speaker notes display issues:**
- Speaker notes are stored as HTML comments: `<!-- NOTES: ... -->`
- For a dedicated speaker view, open browser developer tools to read comments
- Alternatively, generate a separate `-notes.html` file with notes visible

**Header/footer not appearing on all frames:**
- The header and footer are global elements outside individual frames
- They use `position: fixed` to stay visible across all frames
- Ensure they have `z-index: 100` above frame content

---

## Example Session Flows

### Conference Talk

1. User: "I need slides for my ICML 2026 talk on attention mechanisms"
2. Skill asks about purpose (Conference talk), length (Medium 10-20), content readiness, content types (Theorems, Equations), field (Computer Science)
3. User shares their paper abstract and key results
4. Skill asks about desired tone (Modern/Minimal)
5. Skill generates 3 theme previews (Metropolis, Technical Report, Copenhagen)
6. User picks Theme A (Metropolis), asks for darker header bars
7. Skill generates full presentation with theorem environments and KaTeX equations
8. Skill opens the presentation in browser
9. User requests adjustments to specific frames
10. Final presentation delivered

### Course Lecture

1. User: "Create lecture slides for my algorithms course, covering dynamic programming"
2. Skill asks about purpose (Lecture/Course), length (Long 20+), content readiness (rough notes), content types (Algorithms, Equations), field (Computer Science)
3. User shares their lecture outline and key algorithms
4. Skill asks about desired tone (Clear/Pedagogical)
5. Skill generates 3 theme previews (Berlin, Lecture Notes, Copenhagen)
6. User picks Theme B (Lecture Notes)
7. Skill generates full presentation with algorithm boxes and progressive disclosure for step-by-step walkthroughs
8. Final presentation delivered

### PPT Conversion

1. User: "Convert my research paper PPT to web slides"
2. Skill extracts content and images from PPT, flags detected equation objects
3. Skill confirms extracted content with user
4. Skill asks about desired tone/theme
5. Skill generates theme previews
6. User picks a theme
7. Skill generates HTML presentation with preserved assets and equations converted to KaTeX
8. Final presentation delivered
