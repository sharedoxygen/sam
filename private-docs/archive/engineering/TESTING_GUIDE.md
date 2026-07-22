# 🧪 Testing Guide - Agent Sales Activity Manager (ASAM)

## 🎯 Purpose of Testing

Testing is a critical part of our development process for the Agent Sales Activity Manager (ASAM). It ensures:

- **Quality & Reliability**: That the application functions as expected for users.
- **Regression Prevention**: That new changes do not break existing functionality.
- **Maintainability**: That code can be refactored and updated with confidence.
- **Documentation**: Tests often serve as a form of executable documentation for how components and functions are intended to behave.

All new features and significant bug fixes should be accompanied by appropriate tests.

---

## 🧪 Types of Tests Used

We employ a multi-layered testing strategy:

### 1. Unit Tests

- **Tool**: Jest
- **Purpose**: To test the smallest individual units of code (e.g., functions, hooks, utility classes, or individual React components in isolation) to verify they behave correctly given a set of inputs.
- **Focus**: Isolate the unit under test, often mocking its dependencies.

### 2. Integration Tests

- **Tools**: Jest with React Testing Library (RTL)
- **Purpose**: To test the interaction between several units or components. For frontend, this often means testing how multiple React components render and interact together to fulfill a piece of functionality (e.g., a form submission, a modal dialog).
- **Focus**: Test components from the user's perspective (how they find elements, interact with them) without testing deep implementation details.

### 3. End-to-End (E2E) Tests

- **Tool**: Playwright
- **Purpose**: To test complete user flows through the application from start to finish, simulating real user scenarios in a browser environment.
- **Focus**: Validate that entire features work as expected across different parts of the system (frontend, backend APIs, database interactions at a high level).

---

## 🛠️ Tools & Technologies

- **Jest**: JavaScript testing framework used for unit and integration tests.
- **React Testing Library (RTL)**: Utilities for testing React components in a user-centric way, used with Jest.
- **Playwright**: Framework for reliable end-to-end testing across modern browsers.

---

## 📁 Test File Location & Naming Conventions

- **Unit & Integration Tests (Jest/RTL)**:
  - Located in `__tests__` directories alongside the code they are testing (e.g., `src/components/MyComponent/__tests__/MyComponent.test.tsx`).
  - Files are named `*.test.ts` or `*.test.tsx`.
- **End-to-End Tests (Playwright)**:
  - Located in the root `e2e/` directory.
  - Files are named `*.spec.ts` (e.g., `e2e/login.spec.ts`).

---

## ✍️ Writing Tests - General Guidelines

- **Clarity & Readability**: Tests should be easy to understand. Use descriptive names for `describe` and `it` blocks.
- **Behavior, Not Implementation**: Test _what_ the code does from an external perspective, not _how_ it does it internally. This makes tests less brittle to refactoring.
- **Independence**: Each test case (`it` block) should be independent and not rely on the state or outcome of other tests. They should be runnable in any order.
- **AAA Pattern (Arrange, Act, Assert)**: Structure your tests clearly:
  - **Arrange**: Set up the necessary preconditions and inputs.
  - **Act**: Execute the function/component/interaction you are testing.
  - **Assert**: Verify that the outcome is as expected.
- **Mocking**: For unit/integration tests, mock external dependencies (e.g., API calls, browser APIs not available in Node) to isolate the unit under test and make tests deterministic and fast. Jest provides powerful mocking capabilities.
- **Avoid Over-Mocking**: For integration tests, try to use real component instances where possible to test their actual integration.

---

## ⏱️ When to Write Tests

- **New Features**: All new features must include:
  - Unit tests for new helper functions, complex logic, or specific component states.
  - Integration tests for new components and their interactions.
  - E2E tests for critical user flows introduced by the feature.
- **Bug Fixes**: Any bug fix should be accompanied by a regression test that specifically covers the scenario of the bug to ensure it doesn't reappear.
- **Refactoring**: When refactoring code, existing tests should still pass. If the refactor changes public APIs or user-visible behavior, tests may need to be updated accordingly.

---

## 🚀 Running Tests

Tests can be run using npm scripts defined in `package.json`:

- **`npm run test`**: Runs all Jest unit and integration tests.
- **`npm run test:watch`**: Runs Jest tests in watch mode, re-running tests related to changed files.
- **`npm run test:coverage`**: Runs Jest tests and generates a code coverage report (viewable in `coverage/lcov-report/index.html`).
- **`npm run test:e2e`**: Runs all Playwright end-to-end tests.

Automated tests are also a key part of our pre-commit hooks (potentially a subset for speed) and the CI/CD pipeline, where all tests must pass before code can be merged or deployed.

---

## 📊 Test Coverage

While we aim for high test coverage, the quality and relevance of tests are more important than raw percentage numbers. Use `npm run test:coverage` to view current coverage and identify untested areas of the codebase.
Focus on covering critical paths, complex logic, and user-facing functionality.

---

## 🤖 Best Practices for AI Assistants & Testing

- **Suggest Tests**: When generating new components, functions, or features, proactively suggest or include basic unit or integration tests that cover the core functionality.
- **Update Existing Tests**: If modifying existing code that has tests, ensure these tests are updated to reflect the changes. If new behavior is introduced, add new test cases.
- **Targeted Test Generation**: You can be prompted to write tests for specific pieces of logic, components, or user interactions.
- **Follow Patterns**: When generating tests, adhere to the existing testing patterns, tools, and file structure outlined in this guide.
- **Verify Testability**: If generating complex or untestable code, flag it or suggest alternative approaches that are more test-friendly.

---

Adhering to these testing guidelines is essential for maintaining a high-quality, robust, and reliable application.
