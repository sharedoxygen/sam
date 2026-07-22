# 🤖 AI Assistant Guidelines - Agent Sales Activity Manager (ASAM)

This checklist is for AI assistants to ensure all contributions align with the Agent Sales Activity Manager (ASAM) project standards. Always refer to `docs/ENFORCEMENT_STACK.md` for a comprehensive overview of standards and enforcement mechanisms.

## 🤖 Core Directives for AI Assistants

1.  **🚨 DATABASE SAFETY FIRST**: **NEVER** execute destructive database commands without explicit user permission and backup procedures. See `docs/DATABASE_OPERATION_SAFETY.md`.
2.  **Understand the Goal**: Clarify the user's request if ambiguous before generating code or making changes.
3.  **Prioritize Adherence to Standards**: All generated code, documentation, and other artifacts MUST follow the project's established standards.
4.  **🆕 Ensure Data Integrity**: ALL user/data operations must maintain data consistency and quality through DataIntegrityService.
5.  **Consult Documentation**: Regularly refer to `docs/ENFORCEMENT_STACK.md` and the specific guides it points to (e.g., `STYLING_STANDARDS.md`, `CSS_MODULES_GUIDE.md`, this checklist).
6.  **Use Project Tooling**: Employ project-specific scripts (`npm run ...`) for linting, testing, formatting, etc.
7.  **Maintain Consistency**: Strive for consistency with existing code patterns, architecture, and style.
8.  **Incremental Changes**: For complex tasks, prefer smaller, verifiable changes.

## ✅ Pre-Task Checklist

- [ ] **🚨 DATABASE OPERATION CHECK**: If task involves ANY database operations, review `docs/DATABASE_OPERATION_SAFETY.md` protocols.
- [ ] **Review Request**: Fully understand the task requirements.
- [ ] **🆕 Identify Data Operations**: Determine if the task involves user management or data modifications.
- [ ] **Consult `ENFORCEMENT_STACK.md`**: Identify relevant standards and enforcement layers for the task.
- [ ] **🆕 Review Data Integrity Requirements**: If data-related, check `src/lib/services/dataIntegrityService.ts` patterns.
- [ ] **Check Specific Guides**: Review detailed guides pertinent to the task (e.g., `STYLING_STANDARDS.md` for UI work, `TESTING_GUIDE.md` for test creation).
- [ ] **Identify Existing Utilities/Components**: Plan to reuse existing code where appropriate.

## 🛠️ Development & Implementation Checklist

### General Code Quality:

- [ ] **Readable & Maintainable**: Code is clear, well-structured, and includes necessary comments for complex logic.
- [ ] **TypeScript Best Practices**: Strict typing is used; avoid `any` where possible. Adhere to `tsconfig.json` settings.
- [ ] **Error Handling**: Implement robust error handling for API calls, data processing, etc.
- [ ] **Security**: Follow secure coding best practices (e.g., input validation, avoiding common vulnerabilities).
- [ ] **Performance**: Consider performance implications; avoid inefficient algorithms or excessive re-renders.

### 🆕 Data Integrity & Quality:

- [ ] **DataIntegrityService Usage**: All user creation/modification through `DataIntegrityService.createUserWithIntegrity()`.
- [ ] **WeeklyActivity Operations**: All WeeklyActivity updates through `DataIntegrityService.updateWeeklyActivityWithIntegrity()`.
- [ ] **Type Safety**: Proper userId type handling (string vs number) using `parseInt(String(user.id), 10)`.
- [ ] **Dashboard Consistency**: Ensure dashboard and daily activity views show consistent metrics.
- [ ] **Current Week Data**: Verify all agents have current week WeeklyActivity data.
- [ ] **Duplicate Prevention**: Check for and prevent duplicate user creation.
- [ ] **Referential Integrity**: Validate proper relationships between users, activities, and hierarchy.
- [ ] **Role Hierarchy**: Ensure proper manager-report relationships and role-based access.

### Styling (CSS Modules):

- [ ] **CSS Modules ONLY**: All component styling in `ComponentName.module.css` files.
- [ ] **No Inline Styles**: Avoid `style={{}}` except for truly dynamic, single-property values (e.g., `backgroundColor` from a prop).
- [ ] **No `<style jsx>`**: This is strictly prohibited.
- [ ] **Use CSS Variables**: Leverage variables from `src/styles/globals.scss` for colors, fonts, spacing (as per `STYLING_STANDARDS.md` and `CSS_MODULES_GUIDE.md`).
- [ ] **Semantic Class Names**: Use clear, camelCase class names in `.module.css` files.
- [ ] **`classNames` Utility**: Use `@/lib/utils/classNames` for conditional styling.
- [ ] **Theme Testing**: Verify appearance and functionality in all supported themes (default, dark, State Farm).
- [ ] **Responsiveness**: Ensure components are responsive as per guidelines.

### Testing:

- [ ] **Unit Tests (Jest)**: New logic (utility functions, complex component logic) should be covered by unit tests.
- [ ] **🆕 Data Integrity Tests**: Include tests for data consistency scenarios and DataIntegrityService operations.
- [ ] **Integration Tests (Jest/RTL)**: Component interactions and smaller flows should be tested.
- [ ] **E2E Tests (Playwright)**: Critical user flows should have E2E test coverage (usually for larger features).
- [ ] **Tests Pass**: All relevant tests (`npm run test`, `npm run test:e2e`) must pass locally.

### Commits & Pull Requests (PRs):

- [ ] **Conventional Commits**: Commit messages MUST follow the Conventional Commits specification (e.g., `feat: ...`, `fix: ...`, `docs: ...`). _(Refer to `docs/COMMIT_CONVENTIONS.md` or established project practice)._
- [ ] **Atomic Commits**: Commits should be small, logical, and self-contained.
- [ ] **PRs**: Follow PR templates/guidelines; ensure CI checks pass before requesting review.

### Documentation:

- [ ] **Code Comments**: Add JSDoc or TSDoc comments for functions, classes, and complex logic.
- [ ] **🆕 Data Operations Documentation**: Document any data integrity considerations or DataIntegrityService usage.
- [ ] **Update Project Docs**: If changes impact overall architecture, setup, or user-facing features, propose updates to relevant files in `docs/`.

## 🔍 Post-Implementation & Pre-Commit Checklist

- [ ] **Run Linters & Formatters**: `npm run lint && npm run format` (or equivalent project scripts). Address all issues.
- [ ] **Run CSS Checks**: `npm run style:check`. Address all issues.
- [ ] **Run Tests**: `npm run test` (and `npm run test:e2e` if applicable). All tests must pass.
- [ ] **🆕 Verify Data Integrity**: If data operations were performed, verify dashboard consistency and WeeklyActivity generation.
- [ ] **Build Project**: `npm run build`. Ensure the project builds successfully.
- [ ] **Self-Review**: Review changes against this checklist and project standards.

## 🛑 Automatic Rejection Criteria (for PRs/Commits)

- **🚨 DESTRUCTIVE DATABASE OPERATIONS WITHOUT PERMISSION** (CRITICAL - IMMEDIATE REJECTION).
- **🚨 Any database reset, migration, or data deletion without explicit user approval**.
- Any `<style jsx>` blocks or prevalent inline styles (violating CSS Modules policy).
- Hardcoded colors/fonts/spacing instead of CSS variables for themeable elements.
- Missing `.module.css` file for new styled components.
- **🆕 User/data operations bypassing DataIntegrityService** (violating data integrity policy).
- **🆕 Dashboard inconsistencies or missing current week data for agents**.
- **🆕 Type mismatches in userId handling** (string vs number issues).
- Linter or formatter violations that were not auto-fixed.
- Failing automated tests (unit, integration, E2E) in CI.
- Build failures in CI.
- Non-conventional commit messages (if `commitlint` is active in pre-commit/CI).
- Regressions in functionality or major visual/usability issues.

## 📝 Standard AI Response Snippet (when starting a task)

"Understood. I will perform this task in accordance with the project standards outlined in `docs/ENFORCEMENT_STACK.md` and relevant specialized guides (e.g., `STYLING_STANDARDS.md`, `CSS_MODULES_GUIDE.md`). Key considerations will include [mention 2-3 relevant points like: using CSS Modules, **🆕 maintaining data integrity through DataIntegrityService**, writing unit tests, adhering to conventional commits]. I will run all necessary linters, formatters, and tests before concluding."

## 🔧 Key Tools & Scripts (Always Use)

- **Linters/Formatters**: ESLint, Prettier (via `npm run lint`, `npm run format`, or IDE integration).
- **CSS Check**: `npm run style:check`.
- **🆕 Data Integrity Service**: `src/lib/services/dataIntegrityService.ts` for all user/data operations.
- **Testing**: `npm run test`, `npm run test:e2e`.
- **Build**: `npm run build`.
- **🆕 Data Export**: `npx ts-node scripts/export-current-seed-data.ts` for approved state preservation.
- **Primary Documentation**: `docs/ENFORCEMENT_STACK.md` and linked files.
- **ClassNames Utility**: `import { classNames } from '@/lib/utils/classNames';`

## 🔗 Key Reference Documents

1.  **`docs/ENFORCEMENT_STACK.md`** (This is your primary guide to all standards)
2.  **🚨 `docs/DATABASE_OPERATION_SAFETY.md`** (CRITICAL - Database safety protocols, created post-incident)
3.  **🆕 `src/lib/services/dataIntegrityService.ts`** (Data integrity implementation patterns)
4.  **🆕 `docs/DATABASE_SEED_GUIDE.md`** (Data integrity and seeding standards)
5.  `docs/STYLING_STANDARDS.md`
6.  `docs/CSS_MODULES_GUIDE.md`
7.  `docs/README.md` (for setup, contributing, and general project info)
8.  _(Pending: `docs/TESTING_GUIDE.md`, `docs/API_DESIGN_GUIDE.md`, `docs/COMMIT_CONVENTIONS.md`)_

## ⚠️ Common AI Pitfalls to Avoid

1.  ❌ Overlooking project-specific configurations for linters/formatters.
2.  ❌ Generating styled components without a corresponding `.module.css` file.
3.  ❌ Hardcoding values that should come from CSS variables or constants.
4.  ❌ **🆕 Creating users or modifying data without using DataIntegrityService**.
5.  ❌ **🆕 Ignoring type safety in userId handling (string vs number mismatches)**.
6.  ❌ **🆕 Causing dashboard inconsistencies or missing current week data**.
7.  ❌ Neglecting to add or update tests for new features or fixes.
8.  ❌ Writing commit messages that don't follow project conventions.
9.  ❌ Not verifying changes across different themes or responsive breakpoints.
10. ❌ Introducing breaking changes to APIs or component props without discussion/planning.
11. ❌ **🚨 EXECUTING DESTRUCTIVE DATABASE COMMANDS WITHOUT PERMISSION** (CRITICAL VIOLATION).

## 🚨 Database Operation Safety Guidelines

**CRITICAL**: Any task involving database operations must follow strict safety protocols from `docs/DATABASE_OPERATION_SAFETY.md`:

### 🔴 PROHIBITED DATABASE COMMANDS:
**NEVER execute these without explicit user permission and backup:**
- `npx prisma migrate reset`
- `npx prisma migrate reset --force`
- `npx prisma db push --force-reset`
- `DROP DATABASE`
- `TRUNCATE TABLE`
- Mass `DELETE` operations

### 🟡 RESTRICTED DATABASE COMMANDS:
**ALWAYS ask permission first:**
- `npx prisma migrate dev`
- `npx prisma db push`
- `npm run db:unload`
- `npm run db:reload`
- `./scripts/db-seed-manager.sh unload`

### ✅ MANDATORY SAFETY PROTOCOL:
Before ANY destructive database operation:
1. **ASK EXPLICIT USER PERMISSION** with clear warning about data loss risk
2. **CREATE BACKUP** using `npx ts-node scripts/export-current-seed-data.ts`
3. **EXPLAIN IMPACT** - what data will be affected and how
4. **PROVIDE RECOVERY PLAN** - how to restore if something goes wrong
5. **WAIT FOR CLEAR "YES"** - do not proceed without explicit confirmation

### 📢 REQUIRED COMMUNICATION FORMAT:
```
⚠️  DATABASE OPERATION REQUIRED ⚠️

Operation: [specific command]
Risk Level: [HIGH/MEDIUM/LOW] 
Data at Risk: [specific data that could be lost]
Backup Required: YES
Recovery Time: [estimated time]

This operation could cause data loss.
I will create a backup first and verify recovery procedures.

Do you give explicit permission to proceed? (YES/NO)
```

## 🆕 Data Integrity Specific Guidelines

### For User Management Tasks:
- **ALWAYS** use `DataIntegrityService.createUserWithIntegrity()` for user creation
- **VERIFY** no duplicate users exist
- **ENSURE** proper role hierarchy relationships
- **GENERATE** current week WeeklyActivity data for new agents
- **VALIDATE** manager-report relationships

### For Activity/Data Tasks:
- **USE** `DataIntegrityService.updateWeeklyActivityWithIntegrity()` for WeeklyActivity updates
- **MAINTAIN** dashboard and daily activity consistency
- **HANDLE** userId type conversions properly (`parseInt(String(user.id), 10)`)
- **VERIFY** all agents have current week data
- **TEST** data integrity scenarios

### For Dashboard/Reporting Tasks:
- **ENSURE** consistent data sources between dashboard and daily activity views
- **VALIDATE** metric calculations align across all views
- **CHECK** WeeklyActivity data exists for the queried time periods
- **VERIFY** proper date range filtering logic

## 🚀 Definition of Done (for AI Contributions)

- ✅ Code implements the requested functionality or fix.
- ✅ Code adheres to all standards outlined in `docs/ENFORCEMENT_STACK.md` and this checklist.
- ✅ **🆕 Data operations use DataIntegrityService and maintain consistency**.
- ✅ All relevant linters and style checks pass.
- ✅ All relevant automated tests pass.
- ✅ **🆕 Data integrity verification completed (dashboard consistency, current week data)**.
- ✅ Project builds successfully.
- ✅ Documentation (code comments, project docs) updated as necessary.
- ✅ Commit messages are conventional.
- ✅ (If applicable) PR is created following project guidelines.

---

**AI Assistants**: Your adherence to these standards is crucial for maintaining project quality and consistency. When in doubt, ask for clarification or refer to the documentation. **🆕 Pay special attention to data integrity requirements - all user and data operations must maintain consistency and quality through the established DataIntegrityService patterns.**

**🔒 Data Integrity Version**: 2.0 - Comprehensive validation and quality enforcement
