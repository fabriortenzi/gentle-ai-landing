# Skill Registry

**Delegator use only.** Any agent that launches sub-agents reads this registry to resolve compact rules, then injects them directly into sub-agent prompts. Sub-agents do NOT read this registry or individual SKILL.md files.

See `_shared/skill-resolver.md` for the full resolution protocol.

## User Skills

| Trigger | Skill | Path |
|---------|-------|------|
| When styling with Tailwind - cn(), theme variables, no var() in className | tailwind-4 | /Users/fabrizioortenzi/.claude/skills/tailwind-4/SKILL.md |
| When writing TypeScript code - types, interfaces, generics | typescript | /Users/fabrizioortenzi/.claude/skills/typescript/SKILL.md |
| When writing React components - no useMemo/useCallback needed | react-19 | /Users/fabrizioortenzi/.claude/skills/react-19/SKILL.md |
| When writing E2E tests - Page Objects, selectors, MCP workflow | playwright | /Users/fabrizioortenzi/.claude/skills/playwright/SKILL.md |
| When managing React state with Zustand | zustand-5 | /Users/fabrizioortenzi/.claude/skills/zustand-5/SKILL.md |
| When using Zod for validation - breaking changes from v3 | zod-4 | /Users/fabrizioortenzi/.claude/skills/zod-4/SKILL.md |
| When working with Next.js - routing, Server Actions, data fetching | nextjs-15 | /Users/fabrizioortenzi/.claude/skills/nextjs-15/SKILL.md |
| When building AI chat features - breaking changes from v4 | ai-sdk-5 | /Users/fabrizioortenzi/.claude/skills/ai-sdk-5/SKILL.md |
| When writing Go tests, using teatest, or adding test coverage | go-testing | /Users/fabrizioortenzi/.claude/skills/go-testing/SKILL.md |
| When creating a new skill, add agent instructions, or document patterns for AI | skill-creator | /Users/fabrizioortenzi/.claude/skills/skill-creator/SKILL.md |

## Compact Rules

Pre-digested rules per skill. Delegators copy matching blocks into sub-agent prompts as `## Project Standards (auto-resolved)`.

### tailwind-4
- NEVER use `var()` in className — always use Tailwind semantic classes (e.g., `bg-primary`, not `bg-[var(--color-primary)]`)
- NEVER use hex colors in className — always use Tailwind color classes (e.g., `text-white`, not `text-[#ffffff]`)
- Use `cn()` (clsx + tailwind-merge) for conditional or conflicting classes only
- Static-only classes → use `className="..."` directly, no `cn()` needed
- `var()` is OK only in `style` prop or JS constants for third-party libraries that don't accept `className`
- Truly dynamic values (percentages, runtime data) → use `style={{ prop: value }}`

### typescript
- ALWAYS create `const` object first, then extract type: `type X = (typeof OBJ)[keyof typeof OBJ]` — NEVER direct union literals
- Keep interfaces FLAT — nested objects get their own dedicated interface
- NEVER use `any` — use `unknown` for truly unknown, generics for flexible types
- Use `import type { X }` for type-only imports
- Utility types: Pick, Omit, Partial, Required, Readonly, Record, ReturnType, Parameters

### react-19
- No `useMemo` / `useCallback` — React Compiler handles memoization automatically
- Named imports only: `import { useState, useEffect } from "react"` — never default React import
- Server Components by default (no directive); add `"use client"` only for hooks, events, browser APIs
- `ref` is a regular prop — no `forwardRef` needed
- Use `useActionState` for form mutations; `use()` hook for promises and conditional context

### playwright
- Use MCP tools first (navigate → snapshot → interact) before writing any test code
- Selector priority: `getByRole` > `getByLabel` > `getByText` (sparingly) > `getByTestId` (last resort)
- NEVER use CSS class selectors or `#id` selectors
- All tests for a page go in ONE spec file — no splitting by scenario
- Always extend `BasePage`; reuse existing page objects before creating new ones
- Tag tests: `@critical/@high`, `@e2e`, `@feature-name`, `@FEAT-E2E-001`

### zustand-5
- Select specific fields with `(state) => state.field`; use `useShallow` for multiple fields
- NEVER select the entire store object — causes re-renders on any state change
- Use `persist` middleware for localStorage persistence; `devtools` for Redux DevTools
- Use slices pattern for large stores (separate files per domain)
- Access outside React via `useStore.getState()`

### zod-4
- Top-level validators: `z.email()`, `z.uuid()`, `z.url()` — NOT `z.string().email()` (that's Zod 3)
- Error param is `error`, not `message`: `z.string({ error: "..." })`
- Use `safeParse` for non-throwing validation; `parse` for throwing
- Use `z.discriminatedUnion` over `z.union` for better performance
- Use `z.coerce.*` for type conversion (e.g., string → number)

### nextjs-15
- Pages are async Server Components by default — no directive needed
- Server Actions require `"use server"` directive — use `revalidatePath` / `redirect` after mutations
- Use `Promise.all()` for parallel data fetching in layouts/pages
- Use `<Suspense>` for streaming/progressive loading of slow components
- Protect server-only code with `import "server-only"`
- Export `metadata` object for static metadata; `generateMetadata()` for dynamic

### ai-sdk-5
- Import `useChat` from `@ai-sdk/react` (NOT from `"ai"` — that's v4)
- Use `DefaultChatTransport` for transport config: `new DefaultChatTransport({ api: "/api/chat" })`
- Messages use `parts` array (NOT `content` string) — filter by `part.type === "text"` to extract text
- Use `sendMessage({ text: input })` (NOT `handleSubmit` + `handleInputChange`)
- Server side: `streamText()` returns `.toDataStreamResponse()`

### go-testing
- Trigger: Go tests or Bubbletea TUI testing — load full SKILL.md for complete patterns

### skill-creator
- Trigger: When creating a new AI skill — load full SKILL.md for complete patterns

## Project Conventions

| File | Path | Notes |
|------|------|-------|
| AGENTS.md (global) | /Users/fabrizioortenzi/.config/opencode/AGENTS.md | Global agent rules, personality, SDD orchestrator config, Engram protocol |

Read the convention files listed above for project-specific patterns and rules. All referenced paths have been extracted — no need to read index files to discover more.
