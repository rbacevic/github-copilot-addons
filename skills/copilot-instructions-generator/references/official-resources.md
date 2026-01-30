# Official Resources & Research Patterns

## Primary Official Documentation

Always reference these for the most current best practices:

### GitHub Official Docs
- **Main docs**: https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot
- **Best practices for coding agent**: https://docs.github.com/copilot/how-tos/agents/copilot-coding-agent/best-practices-for-using-copilot-to-work-on-tasks
- **Code review instructions**: https://docs.github.com/en/copilot/tutorials/use-custom-instructions

### GitHub Blog (Best Practices)
- **5 Tips article**: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
- **Code review mastery**: https://github.blog/ai-and-ml/unlocking-the-full-power-of-copilot-code-review-master-your-instructions-files/

### Community Resources
- **Awesome Copilot repo**: https://github.com/github/awesome-copilot
- **Instructions examples**: https://github.com/github/awesome-copilot/blob/main/docs/README.instructions.md

## Web Search Patterns

Use these search queries to find current best practices:

### General Copilot Instructions
```
site:docs.github.com copilot custom instructions
site:github.blog copilot instructions best practices
github copilot-instructions.md examples {year}
```

### Tech-Stack Specific
```
{language} coding standards best practices {year}
{framework} project structure conventions
{language} {framework} style guide official
github copilot instructions {language} {framework}
```

### Framework Official Docs
```
site:react.dev coding conventions
site:docs.python.org style guide
site:go.dev effective go
site:typescriptlang.org coding guidelines
```

### Community Examples
```
github awesome-copilot {framework}
github copilot-instructions {language} example
```

## Key Points from Official Documentation

### What Instructions Should Include (from GitHub docs)
1. Project overview - what the app does
2. Tech stack in use
3. Coding guidelines
4. Project structure
5. Build and test commands
6. Development workflow

### What Doesn't Work (from GitHub docs)
- External file references for style guides
- Response style instructions
- Length/detail constraints
- Personality/tone requests

### File Limits (from GitHub docs)
- Keep under ~1,000 lines for best results
- Quality degrades with very long files
- Use multiple *.instructions.md files for complex projects

### Path-Specific Instructions (from VS Code docs)
Use `applyTo` frontmatter for file-pattern matching:
```yaml
---
applyTo: "**/*.py"
---
```

Files go in `.github/instructions/` directory.

## Research Workflow

1. **First**: Check GitHub official docs for any updates
2. **Second**: Search for tech-stack specific best practices
3. **Third**: Look at community examples in awesome-copilot
4. **Fourth**: Search for recent blog posts/articles about the specific stack
5. **Always**: Verify information is current (check dates)
