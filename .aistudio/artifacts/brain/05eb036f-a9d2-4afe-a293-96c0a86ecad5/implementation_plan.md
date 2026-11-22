# Bacc-AI Dashboard & Language Schools Upgrade

An interface and curriculum-localization upgrade that turns the nine subject progress indicators into individual interactive cards beside the Pomodoro timer, adds a dedicated JavaScript hover-border interactivity engine across dashboard cards, and adapts STEM subjects (`Mathematics`, `Integrated Science`, `Programming & AI`) for Egyptian Language Schools (`مدارس اللغات`).

---

## User Review & Critical Decisions

> [!IMPORTANT]
> Clarifying questions were dismissed without custom answers, so the plan applies the recommended defaults below. You can request changes to any choice before proceeding.

- **Progress Cards Layout**: Render the nine subject progress bars as a 3×3 grid of standalone mini-cards directly beside the Pomodoro timer card (replacing the single flat progress list container), preventing nested cards-within-cards while matching the Pomodoro card surface style.
- **Interactive Border Behavior**: Drive card hover borders through a dedicated JavaScript controller module that tracks pointer coordinates (`--card-mx`, `--card-my`) for an edge-reactive border spotlight, subtle border highlight, and lightweight 2px lift under 180ms (`prefers-reduced-motion` safe).
- **Language Schools Default & Toggle**: Enable **Language Schools Mode (`نظام مدارس اللغات`)** by default so **Mathematics**, **Integrated Science**, and **Programming & AI** display in English across all six pages, while keeping an instant toggle in **الإعدادات** if you ever need to switch back to Arabic subject titles.
- **AI Tutoring Style for Language Schools**: Explain concepts in clear Egyptian Arabic while preserving **all scientific, mathematical, and programming terms, formulas, and problem phrasing in English** (matching how Egyptian Language Schools teach STEM subjects).

---

## 1. Overview & Core Concept

- **What It Does**:
  1. Transforms the Dashboard overview row so the Pomodoro Timer sits beside a crisp grid of nine bordered Subject Progress Cards (showing subject name, completion count/ratio, percentage, and live progress bar) with quick click navigation to each subject.
  2. Equips KPI cards, the Pomodoro card, the Subject Progress Cards, and the nine curriculum Subject Cards with a unified bordered surface and a dedicated JavaScript pointer-interactivity module for responsive hover reactions.
  3. Localizes **Mathematics**, **Integrated Science**, and **Programming & AI** into English across navigation filters, selectors, cards, schedules, saved notes, and AI tutoring/extraction prompts.
- **Target Audience**: 1st-year Egyptian Baccalaureate students enrolled in Experimental/Language Schools (`مدارس اللغات`) studying STEM in English alongside Arabic humanities.
- **Key Value**: Cleaner visual rhythm next to the Pomodoro timer, tactile feedback when scanning subjects, and zero translation friction for English-medium STEM students.

---

## 2. User Experience & Visual Design

- **Key User Flows**:
  1. **Progress Scan & Quick Jump**: On opening **لوحة التحكم**, the student sees the 4 KPI cards on top, followed by the Pomodoro Timer alongside the 9 individual Subject Progress Cards. Hovering any card triggers its border spotlight and subtle elevation; clicking a Subject Progress Card smoothly scrolls to or opens that subject's curriculum card (or routes to Sources if empty).
  2. **Language School Subject Recognition**: Everywhere subjects appear (**لوحة التحكم**, **غرفة المذاكرة**, **مصادر المنهج**, **الجدول الذكي**, **مكتبة الملخصات**), Mathematics, Integrated Science, and Programming & AI appear cleanly in English with LTR isolation inside the RTL workspace (`dir="rtl"`).
  3. **Mode Switching**: In **الإعدادات**, under Study Preferences, the student can toggle **نظام التعليم** between `مدارس لغات (STEM بالإنجليزية)` (default) and `مدارس عربي` at any time without losing any sources, progress, or notes.
- **Visual Identity & Theme**:
  - *Aesthetic Direction*: Crisp dark-first academic workspace (`slate-950` canvas, `slate-900` elevated surfaces, matching light-mode pairs via `@custom-variant dark`), maintaining a strict 60-30-10 neutral/surface/accent balance (`indigo-600` primary action, `emerald-500` mastery progress).
  - *Typography & Numerals*: `Cairo` for Arabic prose, clean geometric sans/Cairo pairing for English STEM headings (`dir="ltr"` / `unicode-bidi: isolate`), and `IBM Plex Mono` (`tabular-nums`) for all completion counters (`0/0`, `0%`) and Pomodoro digits.
  - *Card & Border Architecture*: Single-level elevation using crisp `1px` hairline borders (`slate-200` light / `slate-800` dark) instead of nested boxes or thick left accent bars. Metadata stays unboxed (`0 فروع · 0 دروس`) with `·` separators and zero pill clutter.
- **Interactive Feedback & Motion**:
  - Dedicated JavaScript hover controller binds pointer listeners (`pointermove`, `pointerenter`, `pointerleave`) via delegation/refs using `requestAnimationFrame` throttling so hover glow and border highlight track smoothly without React re-renders.
  - Only compositor-friendly CSS transforms (`translateY(-2px)`), opacity, and custom-property border radial gradients animate within `150ms–180ms cubic-bezier(0.16, 1, 0.3, 1)`, automatically disabled under `prefers-reduced-motion: reduce`.

---

## 3. Key Product Decisions & Trade-Offs

- **Decision 1: Standalone Progress Cards Grid vs. Stacked Bars Inside One Wrapper Card**
  - *Chosen Approach*: Replace the right-hand stacked progress bar list container with an open grid of 9 individual bordered Subject Progress Cards beside the Pomodoro Timer card.
  - *Why*: Matches the card-based Pomodoro visual language the user requested while avoiding nested bordered boxes inside another bordered box.
  - *Alternatives Considered*: Putting mini-cards inside the existing bordered container (rejected because nested card borders create visual clutter).

- **Decision 2: Dedicated JavaScript Interactivity Module Using CSS Custom Properties**
  - *Chosen Approach*: Build a framework-agnostic JavaScript module that updates `--pointer-x`, `--pointer-y`, and `data-hover="true"` directly on card DOM nodes on pointer movement, paired with declarative CSS border/ring styling.
  - *Why*: Delivers zero-jitter 60fps border-glow and hover responsiveness without triggering React state re-renders on every pixel of mouse movement, directly fulfilling the request for a dedicated JS card-interaction file.
  - *Alternatives Considered*: Pure CSS `:hover` alone (cannot track cursor position along card borders) or React `useState` per card (unnecessary re-renders on mouse move).

- **Decision 3: Bilingual STEM Localization (`languageSchoolMode`) Rather Than Hardcoded Overwrite**
  - *Chosen Approach*: Add a `languageSchoolMode` setting (defaulting to `true`) and centralized subject-display helpers (`getSubjectDisplayTitle`, `getSubjectDisplayDescription`, `isEnglishStemSubject`) consumed across all views and AI prompts.
  - *Why*: Immediately delivers `Mathematics`, `Integrated Science`, and `Programming & AI` in English across the app and AI tutor, while preserving backward compatibility with existing saved `localStorage` backups and allowing toggling back to Arabic if needed.
  - *Alternatives Considered*: Permanently deleting Arabic names from the subject catalog (inflexible for mixed backups or switching).

---

## 4. Technical Architecture & Data Strategy *(Technical Reference)*

- **Architecture & Component Flow**:
```
┌──────────────────────────────────────────────────────────────────────┐
│                   App State (localStorage + Defaults)                │
│     subjects[9] · settings.languageSchoolMode (default: true)        │
└───────────────────────────────┬──────────────────────────────────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        ▼                       ▼                       ▼
┌───────────────────┐ ┌─────────────────────┐ ┌────────────────────────┐
│ Subject Display   │ │ Dedicated JS Card   │ │ AI Prompt & Extraction │
│ Localization      │ │ Hover Controller    │ │ Grounding Layer        │
│                   │ │ (.js DOM Engine)    │ │                        │
│ - Math -> English │ │ - Pointer tracking  │ │ - English STEM terms   │
│ - Science -> Eng  │ │ - rAF CSS vars      │ │ - Egyptian Arabic flow │
│ - Prog -> English │ │ - Border spotlight  │ │ - OCR / Study modes    │
└─────────┬─────────┘ └──────────┬──────────┘ └───────────┬────────────┘
          │                      │                        │
          └──────────────┬───────┴────────────────────────┘
                         ▼
┌──────────────────────────────────────────────────────────────────────┐
│                     6-Page UI Consumption                            │
│  Dashboard (Pomodoro + 9 Progress Cards Grid + 9 Subject Cards Grid) │
│  Study Room · Sources Hub · Smart Scheduler · Notes Library · Settings│
└──────────────────────────────────────────────────────────────────────┘
```

- **Data Model & State**:
  - Extend application settings with `languageSchoolMode: boolean` (default `true`), backward-compatible with older `localStorage` payloads via backup/hydration defaults.
  - Centralize subject presentation through helper functions (`getSubjectDisplayName`, `getSubjectDisplayDescription`, `isEnglishMediumSubject`) mapping `math` → `Mathematics`, `science` → `Integrated Science`, and `programming` → `Programming & AI` when `languageSchoolMode` is enabled.
  - Add pure unit tests covering zero-start defaults, backup hydration backward compatibility, Language School subject title/prompt resolution, and card hover coordinate math.

- **Interactive Component & State Mapping**:
  - **Dashboard Overview Split**: Left column (`lg:col-span-5`) hosts the interactive bordered Pomodoro Timer Card; right column (`lg:col-span-7`) renders the section header (`توزيع الإنجاز عبر المواد التسع` + overall `%`) above a responsive 3-column grid (`grid-cols-1 sm:grid-cols-2 xl:grid-cols-3`) of 9 interactive bordered Subject Progress Cards. Clicking any progress card smoothly scrolls to and highlights or expands its corresponding Subject Card below.
  - **JS Card Interactivity Controller**: Attaches to containers marked with interactive-card attributes (KPI cards, Pomodoro card, Subject Progress Cards, Subject Curriculum Cards) on mount/update, computing relative pointer coordinates inside each card's bounding box on `pointermove` (throttled via `requestAnimationFrame`), updating CSS variables for the radial border spotlight, and cleanly tearing down listeners on unmount.
  - **Cross-Page STEM Consistency & AI Grounding**: Update subject dropdowns, source filters, schedule selectors, notes filters, and AI prompt builders (`buildModeSystemPrompt`, OCR prompts, curriculum extraction) so Language School STEM subjects display English names and instruct the tutor/OCR engine to keep all scientific, mathematical, and programming terminology in English.
