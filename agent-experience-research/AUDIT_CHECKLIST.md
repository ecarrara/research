# AX Audit Checklist

**Evaluate and score your repository's Agent Experience readiness**

Use this checklist to audit repositories. Each section has a maximum score. A repository should aim for at least 70% of the total possible score before being considered "AX-ready."

---

## Scoring Summary

| Category | Max Points | Your Score |
|----------|------------|------------|
| Foundation | 25 | ___ |
| Agent Instructions | 30 | ___ |
| Testing & Verification | 20 | ___ |
| Context Management | 15 | ___ |
| Security | 15 | ___ |
| Observability | 10 | ___ |
| Human-in-the-Loop | 10 | ___ |
| API/Integration | 10 | ___ |
| **Total** | **135** | ___ |

**Minimum for AX-Ready: 95 points (70%)**

---

## Foundation (25 points)

### Essential Files

| Item | Points | Check |
|------|--------|-------|
| AGENTS.md exists at repository root | 5 | [ ] |
| README.md provides human-readable overview | 3 | [ ] |
| Build command documented and executes successfully | 5 | [ ] |
| Test command documented and executes successfully | 5 | [ ] |
| Tech stack specified with version numbers | 4 | [ ] |
| Directory structure documented | 3 | [ ] |

**Section Score: ___ / 25**

### How to Verify

```bash
# Check AGENTS.md exists
test -f AGENTS.md && echo "PASS" || echo "FAIL"

# Test build command (extract from AGENTS.md and run)
npm run build && echo "PASS" || echo "FAIL"

# Test test command
npm test && echo "PASS" || echo "FAIL"
```

---

## Agent Instructions (30 points)

### AGENTS.md Quality

| Item | Points | Check |
|------|--------|-------|
| Commands section with executable commands | 5 | [ ] |
| Tech stack with specific versions (not ranges) | 3 | [ ] |
| Directory structure with purpose descriptions | 3 | [ ] |
| At least one good/bad code example | 5 | [ ] |
| Boundary definitions (Always/Ask/Never) | 5 | [ ] |
| Dangerous operations explicitly listed | 3 | [ ] |
| Git conventions specified | 2 | [ ] |
| Instructions are concise (<200 total rules) | 2 | [ ] |
| Key entry points documented | 2 | [ ] |

**Section Score: ___ / 30**

### How to Verify

```bash
# Check for essential sections in AGENTS.md
grep -q "## Commands" AGENTS.md && echo "Commands: PASS" || echo "Commands: FAIL"
grep -q "## Tech Stack\|## Stack" AGENTS.md && echo "Tech Stack: PASS" || echo "Tech Stack: FAIL"
grep -q "## Boundaries\|### Always\|### Never" AGENTS.md && echo "Boundaries: PASS" || echo "Boundaries: FAIL"

# Check for code examples (look for code blocks)
grep -c '```' AGENTS.md | xargs -I {} test {} -ge 2 && echo "Code Examples: PASS" || echo "Code Examples: FAIL"

# Check file length (should be readable, not overwhelming)
wc -l AGENTS.md | awk '{if ($1 < 500) print "Length: PASS"; else print "Length: WARN - consider splitting"}'
```

---

## Testing & Verification (20 points)

### Test Infrastructure

| Item | Points | Check |
|------|--------|-------|
| Tests exist and pass | 5 | [ ] |
| Test commands documented in AGENTS.md | 3 | [ ] |
| Single-file test command documented | 2 | [ ] |
| Coverage threshold defined (recommend >70%) | 3 | [ ] |
| CI runs on every push/PR | 4 | [ ] |
| CI failures produce actionable error messages | 3 | [ ] |

**Section Score: ___ / 20**

### How to Verify

```bash
# Run tests
npm test && echo "Tests: PASS" || echo "Tests: FAIL"

# Check coverage
npm test -- --coverage
# Verify coverage meets threshold

# Check CI configuration exists
test -f .github/workflows/ci.yml -o -f .gitlab-ci.yml && echo "CI Config: PASS" || echo "CI Config: FAIL"
```

---

## Context Management (15 points)

### Repository Organization

| Item | Points | Check |
|------|--------|-------|
| Repository overview section in AGENTS.md | 4 | [ ] |
| Key entry points documented | 3 | [ ] |
| Files are focused (<500 lines typical) | 3 | [ ] |
| Monorepo has nested AGENTS.md files | 3 | [ ] |
| Architecture patterns documented | 2 | [ ] |

**Section Score: ___ / 15**

### How to Verify

```bash
# Check for repository overview
grep -q "## Overview\|## Architecture\|## Repository" AGENTS.md && echo "Overview: PASS" || echo "Overview: FAIL"

# Check file sizes (flag files > 500 lines)
find src -name "*.ts" -o -name "*.tsx" | xargs wc -l | sort -n | tail -10
# Review: Are most files under 500 lines?

# For monorepos, check nested AGENTS.md
find . -name "AGENTS.md" | wc -l
# Should be > 1 for monorepos
```

---

## Security (15 points)

### Security Boundaries

| Item | Points | Check |
|------|--------|-------|
| Secrets not in repository (check .gitignore) | 5 | [ ] |
| Forbidden paths documented in AGENTS.md | 3 | [ ] |
| .env.example exists (not .env) | 2 | [ ] |
| Agent account has minimal permissions | 3 | [ ] |
| Sandbox configuration exists (if executing agent code) | 2 | [ ] |

**Section Score: ___ / 15**

### How to Verify

```bash
# Check .gitignore includes secrets patterns
grep -q "\.env" .gitignore && echo ".env ignored: PASS" || echo ".env ignored: FAIL"
grep -q "credentials\|secrets" .gitignore && echo "Credentials ignored: PASS" || echo "Credentials ignored: WARN"

# Check no actual .env files committed
git ls-files | grep -E "^\.env$|^\.env\." && echo "WARNING: .env files in repo!" || echo ".env files: PASS"

# Check .env.example exists
test -f .env.example && echo ".env.example: PASS" || echo ".env.example: FAIL"

# Scan for hardcoded secrets (basic check)
grep -r "sk-\|api_key\s*=\s*['\"]" src/ && echo "WARNING: Possible hardcoded secrets!" || echo "Hardcoded secrets: PASS"
```

---

## Observability (10 points)

### Logging & Monitoring

| Item | Points | Check |
|------|--------|-------|
| Agent actions are logged | 3 | [ ] |
| Token usage tracking exists | 2 | [ ] |
| Decision reasoning can be captured | 3 | [ ] |
| Failure modes documented | 2 | [ ] |

**Section Score: ___ / 10**

### How to Verify

This is harder to automate. Review:
- Does the codebase have structured logging?
- Are there hooks for tracking agent interactions?
- Is there documentation about what to do when agents fail?

---

## Human-in-the-Loop (10 points)

### Oversight Mechanisms

| Item | Points | Check |
|------|--------|-------|
| Risk levels defined for operations | 3 | [ ] |
| Approval requirements documented | 3 | [ ] |
| Escalation paths exist for blocked agents | 2 | [ ] |
| Review checkpoints defined for complex tasks | 2 | [ ] |

**Section Score: ___ / 10**

### How to Verify

```bash
# Check for risk/approval documentation
grep -q "risk\|approval\|checkpoint\|review" AGENTS.md && echo "HITL docs: PASS" || echo "HITL docs: WARN"

# Check boundaries section exists
grep -q "### Ask First\|### Ask\|approval" AGENTS.md && echo "Approval gates: PASS" || echo "Approval gates: FAIL"
```

---

## API/Integration (10 points)

*Only applicable if repository includes APIs*

### API Documentation

| Item | Points | Check |
|------|--------|-------|
| OpenAPI spec exists and is complete | 4 | [ ] |
| Descriptions exist for all endpoints/parameters | 2 | [ ] |
| Examples provided for requests/responses | 2 | [ ] |
| Error codes documented | 2 | [ ] |

**Section Score: ___ / 10** *(or N/A if no APIs)*

### How to Verify

```bash
# Check for OpenAPI spec
find . -name "openapi*.json" -o -name "openapi*.yaml" -o -name "swagger*.json" | head -1

# If spec exists, validate it
npx @apidevtools/swagger-cli validate ./openapi.yaml
```

---

## Score Interpretation

| Score | Rating | Action Required |
|-------|--------|-----------------|
| 115-135 | Excellent | Maintain current practices |
| 95-114 | Good | Minor improvements needed |
| 70-94 | Adequate | Prioritize gaps before heavy agent use |
| 50-69 | Needs Work | Implement [Practical Guide](./PRACTICAL_GUIDE.md) basics |
| <50 | Critical | Stop and address fundamentals first |

---

## Quick Audit Script

Save this as `ax-audit.sh` and run in your repository:

```bash
#!/bin/bash

echo "=== AX Audit ==="
echo ""

SCORE=0

# Foundation checks
echo "## Foundation"
if [ -f "AGENTS.md" ]; then
  echo "✓ AGENTS.md exists (+5)"
  SCORE=$((SCORE + 5))
else
  echo "✗ AGENTS.md missing"
fi

if [ -f "README.md" ]; then
  echo "✓ README.md exists (+3)"
  SCORE=$((SCORE + 3))
else
  echo "✗ README.md missing"
fi

# Check for build/test commands in AGENTS.md
if [ -f "AGENTS.md" ]; then
  if grep -q "npm run build\|yarn build\|pnpm build" AGENTS.md; then
    echo "✓ Build command documented (+5)"
    SCORE=$((SCORE + 5))
  else
    echo "✗ Build command not found in AGENTS.md"
  fi

  if grep -q "npm test\|yarn test\|pnpm test" AGENTS.md; then
    echo "✓ Test command documented (+5)"
    SCORE=$((SCORE + 5))
  else
    echo "✗ Test command not found in AGENTS.md"
  fi
fi

# Agent Instructions checks
echo ""
echo "## Agent Instructions"
if [ -f "AGENTS.md" ]; then
  if grep -q "## Commands" AGENTS.md; then
    echo "✓ Commands section exists (+5)"
    SCORE=$((SCORE + 5))
  fi

  if grep -qi "never\|always\|boundaries" AGENTS.md; then
    echo "✓ Boundaries defined (+5)"
    SCORE=$((SCORE + 5))
  else
    echo "✗ Boundaries not defined"
  fi

  CODE_BLOCKS=$(grep -c '```' AGENTS.md)
  if [ "$CODE_BLOCKS" -ge 2 ]; then
    echo "✓ Code examples present (+5)"
    SCORE=$((SCORE + 5))
  else
    echo "✗ Need more code examples"
  fi
fi

# Security checks
echo ""
echo "## Security"
if [ -f ".gitignore" ]; then
  if grep -q "\.env" .gitignore; then
    echo "✓ .env in .gitignore (+5)"
    SCORE=$((SCORE + 5))
  else
    echo "✗ .env not in .gitignore"
  fi
fi

if [ -f ".env.example" ]; then
  echo "✓ .env.example exists (+2)"
  SCORE=$((SCORE + 2))
else
  echo "✗ .env.example missing"
fi

# CI checks
echo ""
echo "## CI/CD"
if [ -f ".github/workflows/ci.yml" ] || [ -f ".gitlab-ci.yml" ] || [ -f "Jenkinsfile" ]; then
  echo "✓ CI configuration exists (+4)"
  SCORE=$((SCORE + 4))
else
  echo "✗ No CI configuration found"
fi

echo ""
echo "=== Results ==="
echo "Score: $SCORE / 135"
PERCENT=$((SCORE * 100 / 135))
echo "Percentage: $PERCENT%"

if [ $SCORE -ge 95 ]; then
  echo "Rating: AX-Ready ✓"
elif [ $SCORE -ge 70 ]; then
  echo "Rating: Adequate - minor improvements needed"
else
  echo "Rating: Needs Work - see PRACTICAL_GUIDE.md"
fi
```

---

## Audit Report Template

Use this template to document audit results:

```markdown
# AX Audit Report

**Repository:** [name]
**Date:** [date]
**Auditor:** [name]

## Scores

| Category | Score | Max |
|----------|-------|-----|
| Foundation | X | 25 |
| Agent Instructions | X | 30 |
| Testing | X | 20 |
| Context Management | X | 15 |
| Security | X | 15 |
| Observability | X | 10 |
| Human-in-the-Loop | X | 10 |
| API (if applicable) | X | 10 |
| **Total** | **X** | **135** |

## Critical Gaps

1. [Gap 1]
2. [Gap 2]

## Recommendations

### Immediate (This Week)
- [ ] Action 1
- [ ] Action 2

### Short-term (This Month)
- [ ] Action 3
- [ ] Action 4

## Notes

[Additional observations]
```

---

*For implementation guidance on any failing items, see [Practical Guide](./PRACTICAL_GUIDE.md).*
