# Design MD Init  
Create or import DESIGN.md with three modes: interactive survey, codebase analysis, or Figma token import.  
  
## Step 1: Choose initialization mode  
  
<ask_followup_question>  
<question>How would you like to create your DESIGN.md?</question>  
<options>["Interactive survey (answer questions)", "Analyze existing codebase", "Import from Figma JSON file"]</options>  
</ask_followup_question>  
  
## Step 2A: Interactive Survey mode  
Run this section only if the user chose "Interactive survey".  
  
Check for an existing DESIGN.md in the project root. If it exists, ask for confirmation before overwriting:  
  
<ask_followup_question>  
<question>DESIGN.md already exists. Overwrite it with the survey result?</question>  
<options>["Yes, overwrite", "Cancel"]</options>  
</ask_followup_question>  
  
Ask the following five questions one at a time. Wait for each answer before proceeding.  
  
**Question 1 — Aesthetic & personality**  
Describe the overall look and feel (e.g., "minimal and professional", "playful with warm colors", "dark & moody", "enterprise", "brutalist").  
  
**Question 2 — Color palette**  
What are the primary brand colors? Provide hex codes if known, or describe the scheme (e.g., "deep navy primary, coral accent").  
  
**Question 3 — Typography**  
Which font families should be used for headlines and body text? (e.g., Inter, Public Sans, system-ui) Mention any weight preferences.  
  
**Question 4 — Spacing**  
What base spacing unit and scale do you prefer? (e.g., 4px scale, 8px base padding, 16px gutters)  
  
**Question 5 — Shapes & components**  
How rounded should UI elements be (none, small, medium, pill)? Any component preferences (button style, card elevation, input borders)?  
  
After all five answers, synthesize a complete DESIGN.md conforming to the specification in Step 3 below, then proceed to Step 4.  
  
## Step 2B: Codebase Analysis mode  
Run this section only if the user chose "Analyze existing codebase".  
  
Check for an existing DESIGN.md in the project root. If it exists, ask for confirmation before overwriting:  
  
<ask_followup_question>  
<question>DESIGN.md already exists. Overwrite it with the codebase scan result?</question>  
<options>["Yes, overwrite", "Cancel"]</options>  
</ask_followup_question>  
  
Search the repository for all of the following, adapting file extensions to the project language:  
  
**Tailwind config** — locate tailwind.config.{js,ts,cjs,mjs} and extract:  
theme.colors, theme.fontFamily, theme.borderRadius, theme.spacing  
  
**CSS custom properties** — locate global.css, theme.css, variables.css, index.css, or similar. Extract :root custom properties for colors, fonts, radii, and spacing.  
  
**Theme providers and token files** — locate theme.{ts,js}, tokens.{json,yaml,yml}, colors.{ts,js}, ThemeProvider wrappers, theme.py, ThemeConfig.java, theme.rb, or any structured theme object. Extract all design token values.  
  
**Sass/SCSS variables** — locate _variables.scss, _theme.scss, or similar. Extract $ variables for colors, spacing, and radii.  
  
**Component patterns** — scan component files for Button, Card, Input, TextField (any extension: .tsx, .jsx, .vue, .svelte, .py, .rb, .java, .kt, .swift). Infer default values for padding, border-radius, elevation, and color usage.  
  
If conflicting patterns are found across sources, ask the user only for clarification on those specific conflicts. Do NOT ask the five survey questions.  
  
Produce a DESIGN.md that captures the existing design language conforming to the specification in Step 3 below, then proceed to Step 4.  
  
## Step 2C: Figma Import mode  
Run this section only if the user chose "Import from Figma JSON file".  
  
Ask the user for the path to their Figma token export file:  
  
<ask_followup_question>  
<question>Provide the path to your Figma JSON export file (e.g., ./tokens/figma-tokens.json or an absolute path):</question>  
<options>["I'll type the path below", "Cancel"]</options>  
</ask_followup_question>  
  
Wait for the user to provide the file path as a follow-up message.  
  
Verify the file exists. Read the file at the provided path. If the file does not exist or cannot be read, report the error clearly and ask the user to provide a valid path. Do not proceed until a readable JSON file is confirmed.  
  
Check for an existing DESIGN.md in the project root. If it exists, ask for confirmation before overwriting:  
  
<ask_followup_question>  
<question>DESIGN.md already exists. Overwrite it with the Figma import?</question>  
<options>["Yes, overwrite", "Cancel"]</options>  
</ask_followup_question>  
  
Parse the Figma JSON. Handle both Figma Tokens plugin format and raw Figma REST API responses:  
  
**Colors** — look for color or paints entries. Convert all color values to hex (sRGB). Map named color styles to the colors section using recommended token names (primary, secondary, surface, on-surface, error, etc.).  
**Typography** — map fontName.family → fontFamily, fontSize → fontSize (append px if unitless), fontWeight → fontWeight, lineHeightPx/lineHeightPercent → lineHeight (prefer unitless ratio), letterSpacing → letterSpacing (append em if unitless). Create scale levels: headline-display, headline-lg, headline-md, body-lg, body-md, body-sm, label-lg, label-md, label-sm, etc.  
**Spacing** — if spacing tokens exist, map to scale levels: xs, sm, md, lg, xl.  
**Borders / radii** — map corner radius values to rounded scale levels: sm, md, lg, full.  
  
If the Figma data is ambiguous or incomplete, ask the user to clarify only the specific ambiguous values — do not ask the five survey questions.  
  
Show the user a summary of what was extracted (color count, typography count, spacing and radius tokens, any ambiguous values), then ask for approval:  
  
<ask_followup_question>  
<question>Review the extracted tokens above. Proceed with generating DESIGN.md?</question>  
<options>["Yes, generate DESIGN.md", "Let me clarify something first", "Cancel"]</options>  
</ask_followup_question>  
  
Produce a DESIGN.md conforming to the specification in Step 3 below, then proceed to Step 4.  
  
## Step 3: DESIGN.md specification  
Every DESIGN.md must contain:  
  
**YAML front matter** (between --- lines):  
  
---  
version: alpha        # optional  
name: <string>  
description: <string> # optional  
colors:  
  <token-name>: "<hex>"         # e.g., "#1A1C1E"  
typography:  
  <token-name>:  
    fontFamily: <string>  
    fontSize: <Dimension>       # e.g., 16px  
    fontWeight: <number>  
    lineHeight: <number>        # unitless recommended, e.g., 1.6  
    letterSpacing: <Dimension>  
rounded:  
  <scale-level>: <Dimension>    # e.g., sm: 4px  
spacing:  
  <scale-level>: <Dimension>    # e.g., md: 16px  
components:  
  <component-name>:  
    backgroundColor: "{colors.primary}"  
    textColor: "{colors.on-surface}"  
    typography: "{typography.body-md}"  
    rounded: "{rounded.sm}"  
    padding: "8px"  
---  
  
**Markdown body** — sections in canonical order using ## headings:  
  
1. Overview  
2. Colors  
3. Typography  
4. Layout  
5. Elevation & Depth  
6. Shapes  
7. Components  
8. Do's and Don'ts  
  
Use American English spelling throughout. Keep prose concise but informative. Always include a Do's and Don'ts section with actionable guidelines. The YAML front matter is the source of truth — favor explicit token values over vague prose.  
  
## Step 4: Validate DESIGN.md  
Run the linter immediately after writing the file:  
  
npx @google/design.md lint DESIGN.md  
  
The CLI outputs JSON with a findings array and a summary (errors, warnings, infos).  
  
Required handling:  
  
- If summary.errors > 0 (exit code 1) — fix every error (broken-ref: unresolved token references) and re-run until errors reach 0. Do not keep the file with errors.  
- For warnings (missing-primary, contrast-ratio, orphaned-tokens, missing-typography, section-order) — address as many as possible.  
- info findings are informative only; no action required.  
- If the CLI reports errors that cannot be resolved (e.g., a tool bug), report the problem clearly and ask the user how to proceed.  
  
Repeat edit → lint → fix until zero errors.  
  
## Step 5: Update AGENTS.md  
Check for AGENTS.md in the project root.  
  
For each file that exists, locate the block:  
  
<!-- DESIGN_SYSTEM_START -->  
<!-- DESIGN_SYSTEM_END -->  
  
If the block exists, replace its content. If missing, append it at the end of the file.  
  
If neither file exists, create AGENTS.md with the title # Project Instructions for AI Agents and the block below it. Do not add any other content unless asked.  
  
Insert this block (fill in actual values from the generated DESIGN.md):  
  
<!-- DESIGN_SYSTEM_START -->  
## Design System  
  
- **Name:** [design system name]  
- **Primary Color:** [\`primary hex\`]  
- **Typography:** [headline font] for headlines, [body font] for body  
- **Spacing Scale:** [base unit]-based, with [brief scale]  
- **Corner Radius:** [default radius]  
- **Component patterns:** [brief note, e.g., "primary buttons solid, inputs 1px border"]  
- **Full tokens:** See [DESIGN.md](./DESIGN.md)  
  
AI agents: always apply this design system when generating UI. Do not invent new colors or spacing; refer to \`DESIGN.md\`.  
<!-- DESIGN_SYSTEM_END -->  
  
Ensure the block content is identical in both AGENTS.md after updating.  
  
## Step 6: Confirm completion  
Report:  
  
- Which mode was used (survey / codebase scan / Figma import)  
- Which token sources were found (codebase scan) or source file used (Figma import)  
- How many colors, typography tokens, spacing levels, and components were defined  
- Lint result (errors fixed, warnings remaining)  
- Which project files were updated (AGENTS.md)
