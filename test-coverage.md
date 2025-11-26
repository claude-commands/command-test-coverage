---
argument-hint: "[file-or-directory]"
description: "Analyze test coverage and generate missing tests"
model: claude-opus-4-5-20251101
allowed-tools: ["Bash", "Read", "Write", "Edit", "Glob", "Grep", "AskUserQuestion"]
---

**If `$ARGUMENTS` is empty or not provided:**

Analyze test coverage for the entire project.

**Usage:** `/test-coverage [file-or-directory]`

**Examples:**

- `/test-coverage` - Full project coverage analysis
- `/test-coverage src/` - Coverage for src directory
- `/test-coverage src/auth.ts` - Coverage for specific file
- `/test-coverage --generate` - Generate tests for uncovered code

**Workflow:**

1. Detect test framework and coverage tool
2. Run coverage analysis
3. Identify coverage gaps
4. Prioritize by code criticality
5. Generate missing tests (optional)

Proceed with full project coverage analysis.

---

**If `$ARGUMENTS` is provided:**

Analyze coverage for the specified scope and optionally generate tests.

## Configuration

- **Target**: `$ARGUMENTS` (file, directory, or flag)
  - `--generate`: Auto-generate tests for uncovered code
  - `--critical`: Focus on critical paths only
  - Path: Analyze specific file/directory

## Steps

1. **Detect Test Environment**

   Check for test frameworks:
   - **Node.js**: Jest, Vitest, Mocha, AVA
   - **Go**: go test with coverage
   - **Python**: pytest-cov, coverage.py
   - **Rust**: cargo-tarpaulin, cargo-llvm-cov

   ```bash
   # Check for test config files
   ls -la jest.config.* vitest.config.* pytest.ini setup.cfg .coveragerc 2>/dev/null
   ```

2. **Run Coverage Analysis**

   **Node.js (Jest)**:

   ```bash
   npx jest --coverage --coverageReporters=json-summary --coverageReporters=text 2>/dev/null
   ```

   **Node.js (Vitest)**:

   ```bash
   npx vitest run --coverage --reporter=json 2>/dev/null
   ```

   **Go**:

   ```bash
   go test -coverprofile=coverage.out ./...
   go tool cover -func=coverage.out
   ```

   **Python**:

   ```bash
   pytest --cov=. --cov-report=term-missing --cov-report=json 2>/dev/null
   ```

   **Rust**:

   ```bash
   cargo tarpaulin --out Json 2>/dev/null
   ```

3. **Parse Coverage Report**

   Extract:
   - Overall coverage percentage
   - Per-file coverage
   - Uncovered lines and branches
   - Functions with no tests

4. **Identify Coverage Gaps**

   Analyze uncovered code:
   - **Critical paths**: Auth, payments, data mutations
   - **Complex functions**: High cyclomatic complexity
   - **Error handlers**: Catch blocks, error paths
   - **Edge cases**: Boundary conditions, null checks

   ```bash
   # Find files with lowest coverage
   # Parse coverage report and sort by coverage %
   ```

5. **Prioritize by Criticality**

   Score each uncovered area:

   | Factor | Weight | Examples |
   |--------|--------|----------|
   | Security-related | High | Auth, crypto, permissions |
   | Data integrity | High | Saves, updates, deletes |
   | Business logic | Medium | Core domain functions |
   | User-facing | Medium | API handlers, UI logic |
   | Utilities | Low | Helpers, formatters |

6. **Generate Coverage Report**

   ```markdown
   # Test Coverage Analysis

   **Project**: my-app
   **Overall Coverage**: 72.3%

   ## Summary

   | Metric | Value |
   |--------|-------|
   | Lines | 72.3% (1,445/2,000) |
   | Branches | 65.1% (412/633) |
   | Functions | 78.2% (125/160) |
   | Statements | 71.8% (1,520/2,117) |

   ## Coverage by Directory

   | Directory | Coverage | Files | Gap |
   |-----------|----------|-------|-----|
   | src/auth/ | 45.2% | 8 | Critical |
   | src/api/ | 68.3% | 12 | Medium |
   | src/utils/ | 91.2% | 6 | Low |
   | src/models/ | 82.1% | 10 | Low |

   ## Files Needing Attention

   ### Critical (Security/Data)

   | File | Coverage | Uncovered Lines |
   |------|----------|-----------------|
   | src/auth/login.ts | 32% | 45-67, 89-102 |
   | src/api/payments.ts | 41% | 23-45, 78-99 |

   ### High Priority (Complex Logic)

   | File | Coverage | Complexity |
   |------|----------|------------|
   | src/services/order.ts | 55% | 15 |
   | src/utils/parser.ts | 48% | 12 |

   ## Uncovered Critical Paths

   ### 1. Authentication Flow
   - `src/auth/login.ts:45-67` - Error handling not tested
   - `src/auth/session.ts:89-102` - Token refresh logic

   ### 2. Payment Processing
   - `src/api/payments.ts:23-45` - Refund logic
   - `src/api/payments.ts:78-99` - Webhook handling

   ## Recommendations

   1. **Immediate**: Add tests for auth error paths
   2. **This sprint**: Cover payment webhook handling
   3. **Planned**: Increase branch coverage in order service
   ```

7. **Generate Missing Tests (if --generate)**

   For each uncovered critical path:

   a. Read the source file
   b. Analyze function signatures and logic
   c. Identify test cases needed:
      - Happy path
      - Error conditions
      - Edge cases
      - Boundary values

   d. Generate test file following project patterns:

   ```typescript
   // Example generated test
   describe('login', () => {
     describe('error handling', () => {
       it('should return 401 for invalid credentials', async () => {
         // Generated test...
       });

       it('should handle database connection errors', async () => {
         // Generated test...
       });

       it('should rate limit after 5 failed attempts', async () => {
         // Generated test...
       });
     });
   });
   ```

8. **Verify Generated Tests**

   ```bash
   # Run only the new tests
   npx jest --testPathPattern="generated" --coverage
   ```

   Check:
   - Tests pass
   - Coverage improved
   - No false positives

## Output Structure

```markdown
# Test Coverage Analysis

## Summary
[Overall metrics and health indicators]

## Coverage by Area
[Breakdown by directory/module]

## Critical Gaps
[Files/functions that need immediate attention]

## Recommendations
[Prioritized list of what to test next]

## Generated Tests (if applicable)
[List of new test files created]
```

## Coverage Targets

| Code Type | Minimum | Target |
|-----------|---------|--------|
| Critical (auth, payments) | 80% | 95% |
| Business logic | 70% | 85% |
| API handlers | 60% | 80% |
| Utilities | 50% | 70% |

## Notes

- Branch coverage is often more important than line coverage
- Focus on testing behavior, not implementation
- Mock external dependencies appropriately
- Generated tests should be reviewed and refined
- Coverage != quality, but gaps indicate risk
