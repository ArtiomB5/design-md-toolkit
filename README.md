
# OpenCode DESIGN.md Toolkit

**Consistent, on-brand UI generation for AI coding agents — powered by [DESIGN.md](https://github.com/google/design.md).**

This repository provides a complete set of extensions for [OpenCode](https://opencode.ai) that create, validate, and enforce design systems using the `DESIGN.md` format. Three components work together:

- **Agent** `design-md-wizard` — creates or imports `DESIGN.md` via interactive survey, codebase scan, or Figma token JSON.
- **Command** `/design-md-init` — one-liner to invoke the agent with the right mode.
- **Skill** `design-md-apply` — teaches the built‑in `build` agent how to apply tokens to UI code.

With these three pieces, your AI assistants automatically respect your brand colors, typography, spacing, and component rules — every time they generate a button, card, or entire screen.

---

## Why DESIGN.md?

Traditionally, design systems live in Figma, brand PDFs, or a designer’s head. AI coding agents can't read any of those.

`DESIGN.md` is a plain‑text, machine‑readable design system document that both humans and agents understand. It defines your visual identity — colors, fonts, spacing, components — in a single file that sits next to your code.

When an agent like Stitch, Cursor, or OpenCode reads your `DESIGN.md`, every screen and component it generates follows the same visual rules. No more off‑brand colors or random spacing.

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
  The wizard updates both `AGENTS.md` and `CLAUDE.md` (if they exist) with a compact design system reference, so all AI agents in the project immediately know where to find the tokens.

- **Enforcement skill**  
  The `design-md-apply` skill turns the built‑in `build` agent into a design‑conscious developer. It replaces hardcoded colors, fonts, and spacing with token references, checks contrast, and follows your “Do’s and Don’ts.”

- **Tech‑stack agnostic**  
  Works with CSS, Tailwind, styled‑components, Sass, and any framework. Agents adapt to your codebase conventions.

---

## Installation

1. **Prerequisites**  
   - [OpenCode](https://opencode.ai) v1.1 or later.  
   - Node.js (for the `@google/design.md` CLI — auto‑installed via `npx`).

2. **Copy the extensions into your project**  
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

   This repository already contains the final versions — just copy the `.opencode` folder into your project root.

3. **Start using the toolkit**  
   Open your project in OpenCode and type `/design-md-init`. That’s it.

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

---

## Usage

### Creating a new design system (interactive survey)

Just run the command with no arguments:

```
/design-md-init
```

The wizard will ask you five questions:

1. Aesthetic & personality  
2. Color palette  
3. Typography  
4. Spacing  
5. Shapes & components

After you answer, it generates a fully‑spec’d `DESIGN.md`, validates it, and inserts a design system reference into your `AGENTS.md` and `CLAUDE.md`.

### Extracting tokens from an existing codebase

```
/design-md-init code
```

The wizard scans your project for design tokens — Tailwind config, CSS custom properties, theme files, Sass variables, component defaults — and builds a `DESIGN.md` that matches your existing UI. It only asks you if there are conflicts.

### Importing from Figma

Export your Figma tokens (e.g., using the [Figma Tokens](https://github.com/lukasoppermann/figma-tokens) plugin or the REST API) to a JSON file, then run:

```
/design-md-init path/to/figma-tokens.json
```

The wizard converts colors, typography, spacing, and radii into a valid `DESIGN.md`.

### Applying the design system when coding

When the `build` agent starts working on UI, it automatically loads the `design-md-apply` skill (if allowed). The skill teaches it to:

- Replace hardcoded `#2665fd` with `var(--color-primary)` or `theme.colors.primary`
- Use semantic spacing tokens instead of `16px`
- Follow your “Do’s and Don’ts” (e.g., never mix rounded and sharp corners)
- Verify WCAG contrast ratios

You can also invoke it manually by asking the agent:  
`@apply design system to this component`

---

## How it works together

```
/design-md-init   →   design-md-wizard agent   →   DESIGN.md   +   AGENTS.md/CLAUDE.md
                                     ↓
                            validation (lint)
                                     ↓
                          automatic block update
```

Then, every future coding session:

```
build agent   →   loads design-md-apply skill   →   reads DESIGN.md   →   applies tokens to generated UI
```

No hardcoded colors. No guessing. Every button, card, and form looks like it belongs.

---

## Repository structure

```
.
├── .opencode/
│   ├── agents/
│   │   └── design-md-wizard.md      # The wizard agent (all creation modes)
│   ├── commands/
│   │   └── design-md-init.md        # The slash command (routes to correct mode)
│   └── skills/
│       └── design-md-apply/
│           └── SKILL.md             # Enforcement skill for the build agent
├── README.md
└── LICENSE
```

---

## Related

- [DESIGN.md specification](https://github.com/google/design.md) — the open standard for AI‑readable design systems.
- [`@google/design.md` CLI](https://www.npmjs.com/package/@google/design.md) — linting, diffing, and exporting design tokens.
- [OpenCode Documentation](https://docs.opencode.ai) — agents, commands, skills, and customisation.

---

## Contributing

Found a bug, want to improve the prompts, or add a new mode? Pull requests are welcome. Please discuss significant changes in an issue first.

---

## License

MIT — see the [LICENSE](LICENSE) file for details.

