```markdown
# agent-prompt Development Patterns

> Auto-generated skill from repository analysis

## Overview
This skill teaches the core development patterns and conventions used in the `agent-prompt` TypeScript codebase. You'll learn the project's file naming, import/export styles, commit conventions, and how to write and run tests. The guide also provides suggested commands for common workflows to streamline your development process.

## Coding Conventions

### File Naming
- **Style:** kebab-case
- **Example:**  
  ```
  agent-core.ts
  prompt-builder.ts
  ```

### Import Style
- **Relative imports** are used throughout the codebase.
- **Example:**
  ```typescript
  import { buildPrompt } from './prompt-builder';
  ```

### Export Style
- **Named exports** are preferred.
- **Example:**
  ```typescript
  // In prompt-builder.ts
  export function buildPrompt() { ... }
  ```

### Commit Message Conventions
- **Type:** Conventional Commits
- **Prefix:** `feat` (feature)
- **Example:**
  ```
  feat: add support for dynamic prompt variables
  ```

## Workflows

### Creating a New Feature
**Trigger:** When adding a new functionality or module  
**Command:** `/new-feature`

1. Create a new file using kebab-case naming (e.g., `my-new-feature.ts`).
2. Implement your feature using named exports.
3. Use relative imports to include dependencies.
4. Write corresponding tests in a `*.test.ts` file.
5. Commit your changes using the conventional commit format:
   ```
   feat: short description of the new feature
   ```

### Writing and Running Tests
**Trigger:** When verifying code correctness  
**Command:** `/run-tests`

1. Create a test file named with the pattern `*.test.ts` (e.g., `prompt-builder.test.ts`).
2. Write your test cases using the project's preferred testing framework (unknown; refer to existing tests).
3. Run the tests using the project's test runner (refer to project documentation or package scripts).

## Testing Patterns

- **Test File Naming:**  
  Test files follow the `*.test.ts` pattern.
  ```
  prompt-builder.test.ts
  ```
- **Framework:**  
  The specific testing framework is not detected. Check existing test files for reference.
- **Placement:**  
  Tests are placed alongside or near the files they test.

## Commands

| Command        | Purpose                                      |
|----------------|----------------------------------------------|
| /new-feature   | Scaffold and commit a new feature/module     |
| /run-tests     | Run all test files matching `*.test.ts`      |
```
