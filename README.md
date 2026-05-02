Here is the updated `README.md` incorporating the new **Cline** workflow and skill details.

***

# OpenCode & Cline DESIGN.md Toolkit

**Consistent, on-brand UI generation for AI coding agents — powered by [DESIGN.md](https://github.com/google/design.md).**

This repository provides a complete set of extensions for **OpenCode** and **Cline** that create, validate, and enforce design systems using the `DESIGN.md` format. It includes agents, commands, skills, and workflows to ensure your AI assistants respect your brand colors, typography, spacing, and component rules every time they generate code.

The toolkit offers two integration paths:
1.  **OpenCode Native**: Uses Agents, Commands, and Skills.
2.  **Cline Compatible**: Uses Rules, Skills, and Workflows (compatible with Cline, Roo Code, and other VS Code AI extensions).

---

## Why DESIGN.md?

Traditionally, design systems live in Figma, brand PDFs, or a designer’s head. AI coding agents can't read any of those.

`DESIGN.md` is a plain‑text, machine‑readable design system document that both humans and agents understand. It defines your visual identity — colors, fonts, spacing, components — in a single file that sits next to your code.

When an agent like Stitch, Cursor, OpenCode, or Cline reads your `DESIGN.md`, every screen and component it generates follows the same visual rules. No more off‑brand colors or random spacing.

This toolkit makes creating and maintaining that `DESIGN.md` file effortless, and then ensures every agent in your project actually uses it.

---

## Features

- **Three creation paths**  
  - *Survey mode* — answer 5 questions about your aesthetic, and the wizard builds a complete design system.  
  - *Codebase scan* — extract existing tokens from Tailwind, CSS, theme files, or component code (any language).  
  - *Figma import* — convert a Figma Tokens JSON export directly into `DESIGN.md`.

- **Automatic validation**  
  Every generated `DESIGN.md` is validated with the official [`@google/design.md`](https://www.npmjs.com/package/@google/design.md) CLI — zero errors before the file is saved.

- **Self‑documenting for agents**  
  The initialization process updates both `AGENTS.md` and `CLAUDE.md` (if they exist) with a compact design system reference, so all AI agents in the project immediately know where to find the tokens.

- **Enforcement skill**  
  The `design-md-apply` skill teaches your AI assistant how to apply tokens to UI code. It replaces hardcoded colors, fonts, and spacing with token references, checks contrast, and follows your “Do’s and Don’ts.”

- **Tech‑stack agnostic**  
  Works with CSS, Tailwind, styled‑components, Sass, and any framework. Agents adapt to your codebase conventions.

- **Dual Platform Support**  
  Ready-to-use configurations for both **OpenCode** (`.opencode/`) and **Cline/Roo Code** (`.clinerules/`).

---

## Installation

### Option 1: OpenCode Integration

1.  **Prerequisites**  
    - [OpenCode](https://opencode.ai) v1.1 or later.  
    - Node.js (for the `@google/design.md` CLI — auto‑installed via `npx`).

2.  **Copy the extensions into your project**  
    Place the following files in your repository:

    ```
    .opencode/
    ├── agents/
    │   └── design-md-wizard.md
    ├── commands/
    │   └── design-md-init.md
    └── skills/
        └── design-md-apply/
            └── SKILL.md
    ```

3.  **Start using the toolkit**  
    Open your project in OpenCode and type `/design-md-init`.

*(Optional)* To enable the skill automatically for the `build` agent, add this to your `opencode.json`:

```json
{
  "agent": {
    "build": {
      "permission": {
        "skill": {
          "design-md-apply": "allow"
        }
      }
    }
  }
}
```

### Option 2: Cline / Roo Code Integration

1.  **Prerequisites**  
    - [Cline](https://cline.bot) or [Roo Code](https://roocode.com) extension for VS Code.
    - Node.js (for the `@google/design.md` CLI).

2.  **Copy the rules into your project**  
    Place the following files in your repository root:

    ```
    .clinerules/
    ├── skills/
    │   └── design-md-apply/
    │       └── SKILL.md
    └── workflows/
        └── design-md-init.md
    ```

3.  **Start using the toolkit**  
    - **To Initialize**: Use the "Run Workflow" feature in Cline/Roo and select `design-md-init`, or simply ask the agent to "Initialize DESIGN.md".
    - **To Apply**: The `design-md-apply` skill is automatically available to the agent when working on UI tasks. You can explicitly invoke it by saying "Apply the design system to this component."

---

## Usage

### Creating a new design system

#### Via OpenCode Command
```
/design-md-init
```

#### Via Cline Workflow
Trigger the `design-md-init` workflow. The agent will guide you through the same three modes:

1.  **Interactive Survey**  
    The agent asks five questions:
    - Aesthetic & personality  
    - Color palette  
    - Typography  
    - Spacing  
    - Shapes & components
    
    After you answer, it generates a fully‑spec’d `DESIGN.md`, validates it, and inserts a design system reference into your `AGENTS.md` and `CLAUDE.md`.

2.  **Extracting tokens from an existing codebase**  
    *OpenCode:* `/design-md-init code`  
    *Cline:* Select "Analyze existing codebase" in the workflow.
    
    The agent scans your project for design tokens — Tailwind config, CSS custom properties, theme files, Sass variables, component defaults — and builds a `DESIGN.md` that matches your existing UI. It only asks you if there are conflicts.

3.  **Importing from Figma**  
    *OpenCode:* `/design-md-init path/to/figma-tokens.json`  
    *Cline:* Select "Import from Figma JSON file" in the workflow.
    
    Export your Figma tokens (e.g., using the [Figma Tokens](https://github.com/lukasoppermann/figma-tokens) plugin or the REST API) to a JSON file. The agent converts colors, typography, spacing, and radii into a valid `DESIGN.md`.

### Applying the design system when coding

When your AI assistant starts working on UI, it automatically loads the `design-md-apply` skill (if allowed/configured). The skill teaches it to:

- Replace hardcoded `#2665fd` with `var(--color-primary)` or `theme.colors.primary`
- Use semantic spacing tokens instead of `16px`
- Follow your “Do’s and Don’ts” (e.g., never mix rounded and sharp corners)
- Verify WCAG contrast ratios

You can also invoke it manually by asking the agent:  
`@apply design system to this component`

---

## How it works together

```
/init workflow   →   Wizard Agent/Workflow   →   DESIGN.md   +   AGENTS.md/CLAUDE.md
                                     ↓
                            validation (lint)
                                     ↓
                          automatic block update
```

Then, every future coding session:

```
Coding Agent   →   loads design-md-apply skill   →   reads DESIGN.md   →   applies tokens to generated UI
```

No hardcoded colors. No guessing. Every button, card, and form looks like it belongs.

---

## Repository structure

```
.
├── .opencode/                  # OpenCode specific extensions
│   ├── agents/
│   │   └── design-md-wizard.md
│   ├── commands/
│   │   └── design-md-init.md
│   └── skills/
│       └── design-md-apply/
│           └── SKILL.md
├── .clinerules/                # Cline/Roo Code specific extensions
│   ├── skills/
│   │   └── design-md-apply/
│   │       └── SKILL.md
│   └── workflows/
│       └── design-md-init.md
├── README.md
└── LICENSE
```

---

## Related

- [DESIGN.md specification](https://github.com/google/design.md) — the open standard for AI‑readable design systems.
- [`@google/design.md` CLI](https://www.npmjs.com/package/@google/design.md) — linting, diffing, and exporting design tokens.
- [OpenCode Documentation](https://docs.opencode.ai) — agents, commands, skills, and customisation.
- [Cline Documentation](https://docs.cline.bot) — skills, workflows, and rules.

---

## Contributing

Found a bug, want to improve the prompts, or add a new mode? Pull requests are welcome. Please discuss significant changes in an issue first.

---

## License

MIT — see the [LICENSE](LICENSE) file for details.
