# CLAUDE.md — Spendora Web UI Prototype

> **Project:** Low-fidelity interactive wireframe prototype and financial dashboard architecture for Spendora.  
> **Stack:** TypeScript (Strict), React 18+, Vite, Tailwind CSS, Vitest / React Testing Library.

---

## 1. Project Overview & Workflow Commands

This repository hosts the interactive web prototype for Spendora, a subscription management and recurring spending dashboard. All builds, tests, and lints are executed locally via Node.js and `npm`.

### Essential CLI Commands
- **Development Server:** `npm run dev` (starts Vite local dev server at `http://localhost:5173`)
- **Type Checking:** `npm run typecheck` (`tsc --noEmit`)
- **Linting:** `npm run lint` (`eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0`)
- **Format Check:** `npm run format:check` (`prettier --check .`)
- **Run All Tests:** `npm test` (`vitest run`)
- **Run Tests in Watch Mode:** `npm run test:watch` (`vitest`)
- **Run Single Test File:** `npm test -- src/components/SubscriptionCard.test.tsx`
- **Production Build:** `npm run build` (`tsc && vite build`)

---

## 2. Architecture & File Structure

```text
src/
├── assets/          # Static icons and design tokens
├── components/      # Modular, reusable React UI components
│   ├── common/      # Primitives (Button, Modal, Input, Badge)
│   └── dashboard/   # Domain-specific components (MetricCard, SubscriptionList)
├── types/           # Global TypeScript definitions and domain models
├── utils/           # Pure utility functions (currency, date calculations)
├── hooks/           # Custom React hooks
└── test/            # Test setup, mocks, and custom render utilities
```

---

## 3. Strict Coding Standards

### TypeScript
- **Zero `any`:** `noImplicitAny` and `strict: true` are enforced. Never use `any`. Use `unknown` with type narrowing (e.g., `typeof`, `instanceof`, or Zod/type guards) when inputs are untyped.
- **Explicit Interfaces:** Always define explicit interfaces or type aliases for component props, state objects, and API/mock payloads.
- **Immutable Types:** Use `readonly` arrays and properties where data should not be mutated in-place:
  ```typescript
  export interface SubscriptionItem {
    readonly id: string;
    readonly name: string;
    readonly billingCycle: 'monthly' | 'annual';
    readonly amountInCents: number; // Integer cents to prevent float rounding
    readonly renewalDate: string;   // ISO-8601 string
    readonly category: string;
  }
  ```

### React & Component Architecture
- **Functional Components:** Use functional components exclusively. Do not use legacy class components.
- **Explicit Prop Contracts:** Explicitly type component props using dedicated interfaces:
  ```typescript
  interface SubscriptionCardProps {
    readonly subscription: SubscriptionItem;
    readonly onSelect: (id: string) => void;
  }

  export const SubscriptionCard = ({ subscription, onSelect }: SubscriptionCardProps) => {
    // Component logic
  };
  ```
- **Pure Helpers:** Keep business logic, currency formatting, and date math in pure, testable utility functions under `src/utils/` rather than inline in components.

### Defensive Validation & Financial Precision
- **Currency & Decimal Math:** Never store or calculate monetary amounts as raw floating-point numbers. Use integer cents (`amountInCents`) or dedicated decimal abstractions.
- **Null Safety:** Guard against `undefined` and `null` values using optional chaining (`?.`) and nullish coalescing (`??`). Never use the non-null assertion operator (`!`).
- **Date Handling:** Validate ISO dates defensively; handle invalid date boundaries gracefully without throwing uncaught runtime exceptions.

---

## 4. Agent Operating Guardrails (Claude Code Rules)

When working in this codebase, you must adhere strictly to the following rules:

### 1. Package & Dependency Lock
- **DO NOT** modify `package.json`, `package-lock.json`, or install new third-party dependencies without explicit, prior user approval.
- Use existing utilities and standard library solutions before proposing external packages.

### 2. Test-Driven Verification (TDD)
- **Always write or update a test before fixing a bug or adding a feature.**
- For bug fixes: Write a reproducing test demonstrating the failure first, verify that it fails, apply the minimal fix, and verify that all tests pass.
- For new features: Include unit tests covering normal execution, edge cases (empty states, zero amounts, negative numbers, boundary dates), and error states.

### 3. Atomic, Reversible Diffs
- Limit changes to the specific task or ticket assigned. Do not refactor unrelated files, reformat unchanged code, or add speculative features.
- Keep diffs small, focused, and structured for fast peer review and clean rollback.

### 4. Context & Secret Hygiene
- **NEVER** output, commit, or log API keys, tokens, environment variables (`.env`), or mock cardholder/credit card data.
- Do not add mock data containing realistic PII (Personally Identifiable Information) or payment card details.

### 5. Verification Checklist Before Completion
Before reporting any task as complete or suggesting a commit:
1. Run `npm run typecheck` and ensure zero type errors.
2. Run `npm test` and ensure all unit tests pass green.
3. Run `npm run lint` and confirm no lint warnings or style violations remain.
