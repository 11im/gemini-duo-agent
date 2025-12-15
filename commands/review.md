# Review Command

Comprehensive code review using Gemini Pro's extended context for large codebases.

## Usage
```
/gemini:review <path> [--aspects=<review_aspects>] [--severity=<level>] [--fix]
```

## Description
This command handles:
- Full codebase reviews
- Pull request analysis
- Security audits
- Performance assessments
- Code quality evaluation
- Best practices compliance

## Parameters
- `path`: File or directory to review (required)
- `--aspects`: What to review (default: all)
  - `all`: Complete review
  - `security`: Security vulnerabilities
  - `performance`: Performance bottlenecks
  - `style`: Code style and conventions
  - `tests`: Test coverage and quality
  - `documentation`: Documentation completeness
  - `architecture`: Design patterns and structure
- `--severity`: Minimum issue severity (default: medium)
  - `critical`: Only critical issues
  - `high`: High and critical
  - `medium`: Medium, high, and critical
  - `low`: All issues
- `--fix`: Automatically apply safe fixes (default: false)

## Workflow
1. **Claude scans** the codebase:
   - Identifies file structure
   - Detects languages/frameworks
   - Maps dependencies
   - Analyzes complexity
2. **Creates review prompt** for Gemini:
   - Full code context
   - Review criteria
   - Severity thresholds
   - Output format
3. **Gemini analyzes**:
   ```bash
   gemini -m pro -p "REVIEW_PROMPT" -o json > review_report.json
   ```
4. **Claude processes** findings:
   - Categorizes issues
   - Prioritizes by severity
   - Suggests fixes
   - Creates action items
5. **Generates report** with:
   - Executive summary
   - Issue breakdown
   - Fix recommendations
   - Metrics and stats

## Review Aspects in Detail

### Security Review
Checks for:
- SQL injection vulnerabilities
- XSS vulnerabilities
- Authentication/authorization flaws
- Sensitive data exposure
- Insecure dependencies
- Cryptographic issues

### Performance Review
Identifies:
- O(n²) or worse algorithms
- Memory leaks
- Unnecessary computations
- Database query inefficiencies
- Network call optimizations
- Caching opportunities

### Style Review
Evaluates:
- Naming conventions
- Code formatting
- Comment quality
- Function length
- Complexity metrics
- Consistency

### Test Review
Assesses:
- Test coverage percentage
- Test quality and assertions
- Edge case handling
- Mock/stub usage
- Integration test presence
- Test maintainability

### Documentation Review
Checks:
- Docstring completeness
- API documentation
- README quality
- Code comments
- Architecture docs
- Examples/tutorials

### Architecture Review
Analyzes:
- Design patterns
- SOLID principles
- Separation of concerns
- Dependency management
- Scalability considerations
- Maintainability

## Example Review Sessions

### Example 1: Full Codebase Review
```bash
/gemini:review src/ --aspects=all --severity=medium
```

**Gemini's Prompt (Auto-generated):**
```
Comprehensive Code Review Request

CODEBASE:
[Full directory tree and file contents]

REVIEW CRITERIA:
1. Security vulnerabilities (OWASP Top 10)
2. Performance bottlenecks (algorithmic complexity)
3. Code quality (maintainability, readability)
4. Best practices (language-specific)
5. Test coverage and quality
6. Documentation completeness

SEVERITY LEVELS:
- CRITICAL: Security holes, data loss risks
- HIGH: Performance issues, major bugs
- MEDIUM: Code smells, minor bugs
- LOW: Style issues, suggestions

OUTPUT FORMAT (JSON):
{
  "summary": {
    "total_files": N,
    "total_issues": N,
    "critical": N,
    "high": N,
    "medium": N,
    "low": N
  },
  "issues": [
    {
      "file": "path/to/file.py",
      "line": 123,
      "severity": "high",
      "category": "security",
      "issue": "SQL injection vulnerability",
      "description": "User input directly concatenated to SQL query",
      "recommendation": "Use parameterized queries",
      "code_snippet": "...",
      "fixed_code": "..."
    }
  ],
  "metrics": {
    "code_quality_score": 7.5,
    "test_coverage": 65,
    "avg_complexity": 4.2
  },
  "recommendations": [
    "Priority action items"
  ]
}
```

**Claude's Output:**
```markdown
# Code Review Report: src/

## Executive Summary
- **Total Files Reviewed:** 42
- **Issues Found:** 87 (3 critical, 12 high, 45 medium, 27 low)
- **Code Quality Score:** 7.5/10
- **Test Coverage:** 65%
- **Estimated Fix Time:** 16 hours

## Critical Issues (MUST FIX)

### 1. SQL Injection in user_service.py:145
**Severity:** CRITICAL  
**Category:** Security  
**Description:** User input directly concatenated to SQL query

```python
# Current (VULNERABLE):
query = f"SELECT * FROM users WHERE username = '{username}'"

# Recommended:
query = "SELECT * FROM users WHERE username = ?"
cursor.execute(query, (username,))
```

**Impact:** Potential database compromise  
**Effort:** 10 minutes

[... more issues ...]

## Recommendations

### Immediate Actions (This Sprint)
1. Fix 3 critical security issues
2. Add input validation layer
3. Increase test coverage to 80%

### Short-term (Next Sprint)
1. Refactor high-complexity functions
2. Add comprehensive error handling
3. Update documentation

### Long-term (Next Quarter)
1. Implement caching layer
2. Migrate to async architecture
3. Add performance monitoring
```

### Example 2: Security-Only Review
```bash
/gemini:review src/api/ --aspects=security --severity=high
```

Focuses exclusively on security vulnerabilities in the API layer.

### Example 3: PR Review
```bash
/gemini:review --git-diff=main..feature-branch --aspects=all
```

Reviews only changed files in the PR.

### Example 4: Auto-Fix Mode
```bash
/gemini:review src/utils.py --aspects=style,documentation --fix
```

Automatically applies safe fixes for style and documentation issues.

## Review Templates

### Startup Code Review
```json
{
  "focus": ["security", "scalability", "best_practices"],
  "severity_threshold": "medium",
  "auto_fix": false,
  "report_format": "detailed"
}
```

### Pre-Production Review
```json
{
  "focus": ["security", "performance", "tests"],
  "severity_threshold": "high",
  "auto_fix": false,
  "require_zero_critical": true
}
```

### Maintenance Review
```json
{
  "focus": ["code_quality", "documentation", "tests"],
  "severity_threshold": "low",
  "auto_fix": true,
  "suggest_refactoring": true
}
```

## Implementation

```python
# Conceptual implementation

def execute_review(path, aspects, severity, fix_mode):
    # Step 1: Claude analyzes codebase
    codebase_info = claude_analyze_structure(path)
    
    # Step 2: Generate review prompt
    review_prompt = claude_create_review_prompt(
        codebase=codebase_info,
        aspects=aspects,
        severity=severity
    )
    
    # Step 3: Gemini performs review
    run_command(f'gemini -m pro -p "{review_prompt}" -o json > review.json')
    
    # Step 4: Claude processes results
    review_data = read_json('review.json')
    processed = claude_process_review(review_data)
    
    # Step 5: Apply fixes if requested
    if fix_mode:
        safe_fixes = filter_safe_fixes(processed['issues'])
        for fix in safe_fixes:
            apply_fix(fix)
    
    # Step 6: Generate report
    report = claude_generate_review_report(processed)
    return report
```

## Quality Metrics

### Code Quality Score (0-10)
Calculated from:
- Complexity (30%)
- Test coverage (25%)
- Documentation (20%)
- Security (15%)
- Performance (10%)

### Issue Severity Classification

**Critical:**
- Security vulnerabilities (CVSS 9-10)
- Data loss risks
- System crash potential

**High:**
- Security issues (CVSS 7-8)
- Major performance problems
- Significant bugs

**Medium:**
- Code smells
- Minor performance issues
- Missing tests

**Low:**
- Style inconsistencies
- Documentation gaps
- Suggestions

## Integration with CI/CD

```yaml
# .github/workflows/review.yml
name: Code Review
on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run Gemini Review
        run: |
          claude /gemini:review src/ --aspects=security,tests --severity=high
          if [ $? -ne 0 ]; then exit 1; fi
```

## Success Criteria
- ✅ All critical issues identified
- ✅ Actionable recommendations provided
- ✅ Metrics accurately calculated
- ✅ Report is comprehensive yet readable
- ✅ Fix suggestions are safe and tested

## Advanced Features

### Trend Analysis
Track code quality over time:
```bash
/gemini:review src/ --track-trends --baseline=v1.0.0
```

### Custom Rules
```bash
/gemini:review src/ --rules=.review-rules.yaml
```

### Team Standards
```bash
/gemini:review src/ --team-standards=.team-coding-standards.md
```

## Error Handling
- **Large codebase**: Automatically chunks into reviewable sections
- **Unknown language**: Falls back to general code review principles
- **No issues found**: Provides positive feedback and suggestions for improvement
- **Fix conflicts**: Reports conflicts without applying, requests manual review
