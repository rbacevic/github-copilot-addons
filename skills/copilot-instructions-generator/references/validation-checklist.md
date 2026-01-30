# Validation Checklist

Use this checklist before finalizing a copilot-instructions.md file.

## Structure Validation

- [ ] File is located at `.github/copilot-instructions.md`
- [ ] Total length is under 1,000 lines (ideally under 500)
- [ ] Uses clear headings to organize sections
- [ ] Uses bullet points for easy scanning
- [ ] No deeply nested structures (max 2 levels)

## Content Validation

### Required Sections
- [ ] Project overview/description present
- [ ] Tech stack documented
- [ ] Key development commands included
- [ ] At least 3 specific coding guidelines

### Quality Checks
- [ ] Instructions are short and imperative
- [ ] Each instruction is self-contained
- [ ] Specific rather than vague (e.g., "use double quotes" not "follow good practices")
- [ ] Includes concrete examples where helpful
- [ ] Commands are copy-pasteable

## Avoid These Anti-Patterns

### Instructions That Won't Work
- [ ] No references to external files/repos for style guides
- [ ] No response style instructions (friendly, formal, etc.)
- [ ] No length/detail constraints
- [ ] No instructions to use @terminal or other Copilot features

### Common Mistakes
- [ ] Not too verbose (prefer examples over explanations)
- [ ] Not too generic (project-specific, not general coding advice)
- [ ] Not contradictory instructions
- [ ] Not outdated information (check versions, deprecated patterns)

## Tech-Stack Alignment

- [ ] Build commands match actual project setup
- [ ] Testing framework matches project
- [ ] Linting/formatting tools match project config
- [ ] Directory structure matches actual layout
- [ ] Dependencies/tools mentioned actually exist in project

## Best Practices Applied

### From GitHub Official Documentation
- [ ] Instructions add context Claude doesn't inherently have
- [ ] Instructions supplement rather than replace chat questions
- [ ] Version controlled alongside source code
- [ ] Treated as living document (can evolve)

### Effectiveness Patterns
- [ ] Tool/dependency preferences specified
- [ ] Code style specifics included
- [ ] Required pre-commit commands documented
- [ ] Error handling patterns defined
- [ ] API design principles stated (if applicable)

## Final Review

Before delivering:
1. Read through entire file - does it flow logically?
2. Would a new developer understand the project from this?
3. Are there any assumptions that should be explicit?
4. Is anything missing that Copilot frequently gets wrong?

## Verification

After file is in place:
- In VS Code/IDE, make a Copilot Chat request
- Check References list - `.github/copilot-instructions.md` should appear
- Verify suggestions align with documented standards
