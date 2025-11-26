# command-test-coverage

A Claude Code slash command for analyzing test coverage and generating missing tests.

## Installation

```bash
# Clone to your preferred location
git clone git@github.com:claude-commands/command-test-coverage.git <clone-path>/command-test-coverage

# Symlink (use full path to cloned repo)
ln -s <clone-path>/command-test-coverage/test-coverage.md ~/.claude/commands/test-coverage.md
```

## Usage

```text
/test-coverage              # Full project coverage analysis
/test-coverage src/         # Coverage for specific directory
/test-coverage --generate   # Generate tests for uncovered code
/test-coverage --critical   # Focus on critical paths only
```

## What it does

1. Detects test framework (Jest, Vitest, pytest, go test)
2. Runs coverage analysis
3. Identifies coverage gaps by criticality
4. Prioritizes what to test next
5. Optionally generates missing tests

## Output Format

```markdown
# Test Coverage Analysis

## Summary
| Metric | Value |
|--------|-------|
| Lines | 72.3% |
| Branches | 65.1% |
| Functions | 78.2% |

## Files Needing Attention
| File | Coverage | Priority |
|------|----------|----------|
| src/auth/login.ts | 32% | Critical |
| src/api/payments.ts | 41% | Critical |

## Recommendations
1. Add tests for auth error paths
2. Cover payment webhook handling
```

## Framework Support

| Framework | Coverage Tool | Notes |
|-----------|---------------|-------|
| Jest | Built-in | Uses --coverage flag |
| Vitest | c8/istanbul | Uses --coverage flag |
| Go | go test | Uses -coverprofile |
| pytest | pytest-cov | Uses --cov flag |
| Rust | tarpaulin | Uses cargo tarpaulin |

## Requirements

- Git repository with tests
- Test framework installed
- Claude Code with Opus 4.5 model access

## Updates

```bash
cd <clone-path>/command-test-coverage && git pull
```
