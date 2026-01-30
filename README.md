# Copilot Instructions Generator

A GitHub Copilot skill and agent for creating high-quality `.github/copilot-instructions.md` files.

## What's Included

```
.github/
├── agents/
│   └── copilot-instructions-expert.agent.md   # Agent that uses the skill
└── skills/
    └── copilot-instructions-generator/        # The skill
        ├── SKILL.md                           # Main skill instructions
        ├── references/
        │   ├── template.md                    # File template
        │   ├── validation-checklist.md        # Quality checklist
        │   └── official-resources.md          # GitHub docs links
        └── assets/examples/                   # 24 tech-stack examples
            ├── dotnet-csharp.md
            ├── python.md
            ├── react-typescript.md
            └── ... (21 more)
```

## How It Works

### Skills vs Agents

- **Skills** are loaded **automatically** when Copilot determines they're relevant based on your prompt
- **Agents** are **manually selected** from the agents dropdown

### Two Ways to Use

#### Option 1: Use the Skill (Automatic)

Simply ask Copilot to create copilot instructions:
- "Create copilot instructions for this project"
- "Generate copilot-instructions.md"
- "Set up copilot config for my React app"

The skill will be automatically loaded based on these trigger phrases.

#### Option 2: Use the Agent (Manual Selection)

1. Open the agents dropdown in VS Code / IDE
2. Select `@copilot-instructions-expert`
3. Ask for help creating copilot instructions

The agent explicitly references and uses the skill's templates and examples.

## Installation

1. **Copy the `.github` folder** to your repository root:
   ```
   your-repo/
   └── .github/
       ├── agents/
       └── skills/
   ```

2. **Enable Agent Skills** in VS Code (if needed):
   - Open Settings
   - Search for `chat.useAgentSkills`
   - Enable the checkbox

3. **Verify installation**:
   - Ask Copilot: "Create copilot instructions for this project"
   - The skill should be loaded automatically

## Included Tech-Stack Examples

| Category | Technologies |
|----------|--------------|
| Backend | .NET/C#, Python, FastAPI, Java, Spring Boot, Go, Scala, Node.js |
| Frontend | TypeScript, React, Angular, AngularJS, Next.js, Blazor |
| Mobile | Android Kotlin, Android Java, Kotlin Multiplatform |
| Data | PySpark, Scala Spark, Python AI/ML |
| Infrastructure | Terraform, Terraform AWS, Terraform Azure, Bicep |
| Analytics | Tableau Cloud, Tableau Desktop |

## Agent References Skill

The agent (`copilot-instructions-expert.agent.md`) references the skill using Markdown links:

```markdown
1. **Main Instructions**: [SKILL.md](../skills/copilot-instructions-generator/SKILL.md)
2. **Template**: [template.md](../skills/copilot-instructions-generator/references/template.md)
```

This ensures the agent always follows the skill's best practices and templates.

## Can You Force an Agent to Use a Skill?

**No direct enforcement mechanism exists.** However:

1. **Skills are auto-loaded** when prompts match their description
2. **Agents can reference skills** via Markdown links (as done here)
3. **The agent's instructions** tell it to read the skill first

The agent in this package explicitly instructs Copilot to:
1. Read the SKILL.md first
2. Load relevant tech-stack examples
3. Use the template structure
4. Validate against the checklist

## License

MIT
