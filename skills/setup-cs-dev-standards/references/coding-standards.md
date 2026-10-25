# Coding Standards

## Rules (refuse on violation)

1. **Strict mode.** No `any` unless the preceding line is `// @ts-expect-error: <reason>`.
2. **One module, one responsibility.** A file exports things that belong to one concept.
3. **Named exports only.**
4. **Naming.** Files kebab-case; variables and functions camelCase; types and classes PascalCase; module-level true constants SCREAMING_SNAKE.
5. **Function body ≤ 50 lines.** Extract past that.
6. **Comments carry why.** Delete the ones that restate the code.
7. **Errors.** Throw an `Error` subclass with a `.code: string` field. Never throw a string or a bare `Error`.
8. **Async.** Every async function either handles its rejections or its JSDoc says the caller must.
9. **Zod at every boundary.** Parse: API input, file reads, env vars, cross-process messages. Untyped JSON never flows past the boundary.
10. **Runtime output goes through a leveled logger.** `console.log` is for throwaway scripts, not committed code.

## Formatting

- Prettier + ESLint. Configs at repo root. Lint must pass before write.
- Import order: node builtins → external → internal → relative.

## Agent-generation rules

1. **Provenance header on every file:**
   ```ts
   // @generated-by: <agent-name>
   // @model: <model-id>
   // @date: <ISO-8601>
   // @task: <task-id or prompt-hash>
   // @version: 1
   ```
2. **Versioned regeneration.** Regenerating an existing file bumps `@version`. Silent overwrite is a violation.
3. **Human-edit lock.** A file whose top line is `// @human-edited: do not regenerate` is read-only to agents. Stop and ask.
4. **One task, one scoped output.** A prompt vague enough to touch many files triggers a scope question before any write.

## Refusal protocol

1. Name the rule and the location.
2. Do not write.
3. Offer the compliant alternative, or request the missing input (a reason for `any`, a narrower scope).
