# Contributing to Prompt Packs

Guidelines for creating portable prompt packs.

## Creating a New Prompt Pack

### 1. Directory Structure
```
your-pack/
├── CLAUDE.md           # Main entry point (required)
├── README.md           # Pack documentation (required)
└── [your-files]/       # Additional files as needed
```

### 2. Portability Rules

#### ✅ DO:
- **Use relative @ references within your pack**
  ```markdown
  Good: @README.md
  Good: @prompts/agent.md
  Good: @../shared/utils.md
  ```

- **Keep all pack files self-contained**
  - Everything needed should be in the pack directory
  - No dependencies on external files (except Claude's built-in tools)

- **Document your entry point clearly**
  - CLAUDE.md should be the main import file
  - List all available features/prompts

#### ❌ DON'T:
- **Never use absolute paths in @ references**
  ```markdown
  Bad: @~/.claude/something.md
  Bad: @/home/user/project/file.md
  ```

- **Don't reference files outside your pack**
  - Exception: Standard Claude Code tools and features

- **Avoid hardcoded paths or user-specific content**

### 3. Testing Portability

Before submitting, test your pack works when copied to different locations:

```bash
# Test 1: Global installation
cp -r your-pack ~/.claude/prompt-packs/
# Add @prompt-packs/your-pack/CLAUDE.md to global CLAUDE.md

# Test 2: Project installation
cp -r your-pack /tmp/test-project/.claude/prompt-packs/
# Add @prompt-packs/your-pack/CLAUDE.md to project CLAUDE.md

# Both should work identically!
```

### 4. Documentation Requirements

Each pack must include:

1. **README.md** with:
   - What the pack does (elevator pitch)
   - When to use it
   - Basic usage example
   - Any special requirements

2. **CLAUDE.md** with:
   - Clear imports of all pack components
   - Instructions or entry points for Claude

3. **Example usage** showing real-world application

### 5. Naming Conventions

- Pack names should be lowercase with hyphens: `awesome-pack`
- Be descriptive but concise
- Avoid generic names like "utils" or "helpers"

### 6. Version Updates

When updating an existing pack:
1. Update the main README.md if adding to the collection
2. Update CHANGELOG.md with your changes
3. Maintain backward compatibility when possible
4. Document breaking changes clearly

### 7. The Portability Checklist 📋

Before submitting your PR, verify:

- [ ] All @ references are relative
- [ ] No absolute paths anywhere
- [ ] Pack works when copied to any location
- [ ] README.md explains the pack clearly
- [ ] CLAUDE.md serves as proper entry point
- [ ] Example usage is provided
- [ ] No user-specific or system-specific content
- [ ] Pack is self-contained

## Example: Creating a "Code Review Pack"

```bash
code-review-pack/
├── CLAUDE.md           # Imports all review prompts
├── README.md           # Explains the review process
├── prompts/
│   ├── reviewer.md     # Main reviewer prompt
│   ├── security.md     # Security-focused review
│   └── performance.md  # Performance review
└── examples/
    └── usage.md        # How to invoke reviews
```

In `CLAUDE.md`:
```markdown
# Code Review Pack

## Available Prompts
- @prompts/reviewer.md - General code review
- @prompts/security.md - Security-focused review
- @prompts/performance.md - Performance optimization review

## Usage
See @examples/usage.md for detailed examples.
```

## Questions?

If you're unsure about something, just ask! Open an issue and we'll help you improve your pack.