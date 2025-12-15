# Result Validator Skill

Automatically validates all Gemini outputs before presenting to users.

## Skill Metadata
```json
{
  "name": "result-validator",
  "version": "1.0.0",
  "type": "validation",
  "auto_trigger": true,
  "priority": "high",
  "applies_to": ["all_gemini_outputs"]
}
```

## Purpose

This skill **ensures quality and correctness** of all Gemini-generated content through automated validation checks before any output reaches the user.

## Activation Conditions

**Automatically activates:**
- After EVERY Gemini CLI execution
- Before presenting ANY Gemini output to user
- Mandatory for ALL task types

**Cannot be disabled** (safety feature)

## Validation Framework

```
Gemini Output
      ↓
[Result Validator Activated]
      ↓
┌────────────────────────────┐
│ Quick Checks (< 1 second)  │
│ - Output exists            │
│ - Not empty                │
│ - Valid format             │
│ - No error messages        │
└────────────────────────────┘
      ↓
┌────────────────────────────┐
│ Format Validation          │
│ - Syntax correct           │
│ - Structure valid          │
│ - Encoding proper          │
└────────────────────────────┘
      ↓
┌────────────────────────────┐
│ Content Validation         │
│ - Completeness             │
│ - Relevance                │
│ - Coherence                │
└────────────────────────────┘
      ↓
┌────────────────────────────┐
│ Quality Assessment         │
│ - Meets requirements       │
│ - Professional quality     │
│ - Ready for use            │
└────────────────────────────┘
      ↓
   Pass / Flag / Reject
```

## Validation Checks by Type

### Research Output Validation
```python
RESEARCH_CHECKS = {
    "structure": [
        "has_title",
        "has_introduction",
        "has_main_sections",
        "has_conclusion",
        "has_references"
    ],
    
    "content": [
        "minimum_length_met",      # Usually 500+ words
        "citations_present",
        "no_placeholder_text",
        "coherent_flow",
        "appropriate_depth"
    ],
    
    "citations": [
        "citations_formatted",     # APA/MLA
        "no_fake_citations",       # Critical!
        "urls_valid",
        "years_plausible",         # Not 2050, etc.
        "authors_mentioned"
    ],
    
    "quality": [
        "professional_tone",
        "no_hallucinations",       # Cross-check facts
        "logical_consistency",
        "clear_language",
        "proper_grammar"
    ]
}

def validate_research(output):
    results = {}
    
    # Structure checks
    results["has_references"] = bool(re.search(r'##?\s*References?', output))
    results["has_sections"] = count_headings(output) >= 3
    
    # Citation checks (CRITICAL)
    citations = extract_citations(output)
    results["has_citations"] = len(citations) > 0
    results["suspicious_citations"] = check_for_hallucinated_citations(citations)
    
    # Content checks
    results["sufficient_length"] = len(output.split()) >= 500
    results["no_placeholders"] = not contains_placeholders(output)
    
    # Quality score
    results["quality_score"] = assess_quality(output)
    
    return {
        "valid": all([
            results["has_references"],
            results["has_sections"],
            results["has_citations"],
            not results["suspicious_citations"],
            results["sufficient_length"]
        ]),
        "checks": results,
        "issues": [k for k, v in results.items() if not v]
    }
```

### Code Output Validation
```python
CODE_CHECKS = {
    "syntax": [
        "valid_python_syntax",      # or other language
        "imports_valid",
        "indentation_correct",
        "no_syntax_errors"
    ],
    
    "completeness": [
        "all_functions_implemented",
        "docstrings_present",
        "type_hints_included",
        "error_handling_exists"
    ],
    
    "quality": [
        "follows_conventions",      # PEP 8, etc.
        "no_code_smells",
        "reasonable_complexity",
        "no_security_issues"
    ],
    
    "tests": [
        "tests_included",
        "tests_are_runnable",
        "edge_cases_covered"
    ]
}

def validate_code(output, language="python"):
    results = {}
    
    # Syntax validation (CRITICAL)
    try:
        if language == "python":
            compile(output, '<string>', 'exec')
            results["valid_syntax"] = True
        # Add other languages
    except SyntaxError as e:
        results["valid_syntax"] = False
        results["syntax_error"] = str(e)
    
    # Completeness checks
    results["has_docstrings"] = check_docstrings(output)
    results["has_error_handling"] = bool(re.search(r'try:|except:', output))
    
    # Quality checks
    results["follows_pep8"] = run_linter(output)
    results["complexity_ok"] = check_complexity(output) < 10
    
    # Security scan
    results["no_vulnerabilities"] = security_scan(output)
    
    # Test presence
    results["has_tests"] = contains_tests(output)
    
    return {
        "valid": all([
            results["valid_syntax"],
            results["has_docstrings"],
            results["no_vulnerabilities"]
        ]),
        "checks": results,
        "issues": [k for k, v in results.items() if not v]
    }
```

### Debug Output Validation
```python
DEBUG_CHECKS = {
    "format": [
        "valid_json_structure",
        "required_fields_present",
        "types_correct"
    ],
    
    "content": [
        "root_cause_identified",
        "fix_provided",
        "explanation_clear",
        "prevention_suggested"
    ],
    
    "quality": [
        "diagnosis_plausible",
        "fix_is_safe",
        "explanation_detailed",
        "actionable"
    ]
}

def validate_debug(output):
    results = {}
    
    # Parse JSON
    try:
        data = json.loads(output)
        results["valid_json"] = True
    except json.JSONDecodeError:
        return {
            "valid": False,
            "checks": {"valid_json": False},
            "issues": ["Invalid JSON format"]
        }
    
    # Required fields
    required = ["root_cause", "fix", "explanation", "prevention"]
    results["has_required_fields"] = all(k in data for k in required)
    
    # Content quality
    results["diagnosis_detailed"] = len(data.get("root_cause", "")) > 50
    results["fix_provided"] = "fix" in data and len(data["fix"]) > 0
    
    # Safety check on fix
    if "fix" in data:
        results["fix_is_safe"] = check_fix_safety(data["fix"])
    
    return {
        "valid": all([
            results["valid_json"],
            results["has_required_fields"],
            results["diagnosis_detailed"]
        ]),
        "checks": results,
        "issues": [k for k, v in results.items() if not v]
    }
```

### Report Output Validation
```python
REPORT_CHECKS = {
    "structure": [
        "has_title",
        "has_executive_summary",
        "has_main_sections",
        "has_conclusion",
        "proper_hierarchy"
    ],
    
    "content": [
        "data_included",
        "analysis_present",
        "conclusions_drawn",
        "visualizations_described"
    ],
    
    "format": [
        "valid_markdown",
        "consistent_formatting",
        "readable_tables",
        "export_ready"
    ],
    
    "quality": [
        "professional_appearance",
        "clear_language",
        "logical_flow",
        "sufficient_detail"
    ]
}

def validate_report(output):
    results = {}
    
    # Structure
    results["has_title"] = bool(re.search(r'^#\s+', output, re.MULTILINE))
    results["has_sections"] = count_headings(output) >= 4
    
    # Content
    results["has_data"] = contains_data_elements(output)  # Tables, numbers
    results["has_conclusions"] = "conclusion" in output.lower()
    
    # Format
    results["valid_markdown"] = validate_markdown(output)
    results["has_tables"] = bool(re.search(r'\|.*\|', output))
    
    # Quality
    results["sufficient_length"] = len(output.split()) >= 800
    results["professional"] = assess_professionalism(output)
    
    return {
        "valid": all([
            results["has_title"],
            results["has_sections"],
            results["valid_markdown"],
            results["sufficient_length"]
        ]),
        "checks": results,
        "issues": [k for k, v in results.items() if not v]
    }
```

## Critical Validation: Citation Verification

```python
def verify_citations(output):
    """
    CRITICAL: Detect hallucinated citations.
    LLMs sometimes invent fake papers/authors.
    """
    citations = extract_citations(output)
    
    suspicious = []
    for citation in citations:
        flags = []
        
        # Red flags
        if citation.year and int(citation.year) > 2024:
            flags.append("Future year")
        
        if citation.venue and citation.venue in KNOWN_FAKE_VENUES:
            flags.append("Fake venue")
        
        if citation.authors and contains_gibberish(citation.authors):
            flags.append("Suspicious author names")
        
        # Title checks
        if citation.title:
            if is_too_generic(citation.title):
                flags.append("Generic title")
            if contains_marketing_speak(citation.title):
                flags.append("Non-academic title")
        
        if flags:
            suspicious.append({
                "citation": str(citation),
                "flags": flags,
                "recommendation": "Verify manually or remove"
            })
    
    return {
        "total_citations": len(citations),
        "suspicious_count": len(suspicious),
        "suspicious_citations": suspicious,
        "verified": len(suspicious) == 0
    }

# Known patterns of hallucinated citations
KNOWN_FAKE_VENUES = [
    "International Journal of Everything",
    "Proceedings of AI Conference",  # Too generic
    # Add more patterns
]

def is_too_generic(title):
    """Check if title is suspiciously generic."""
    generic_patterns = [
        r"^A Study on",
        r"^Research on",
        r"^An Analysis of",
        r"^Introduction to"
    ]
    return any(re.match(p, title) for p in generic_patterns)
```

## Validation Actions

### Action 1: Pass (Quality OK)
```python
def handle_pass(output, validation_results):
    """
    Output passes all checks - present to user.
    """
    return {
        "status": "PASS",
        "output": output,
        "quality_score": validation_results["quality_score"],
        "message": "✅ Output validated and ready"
    }
```

### Action 2: Flag (Minor Issues)
```python
def handle_flag(output, validation_results):
    """
    Output has minor issues - flag but still present.
    """
    # Add warning markers
    flagged_output = add_warning_markers(output, validation_results["issues"])
    
    return {
        "status": "FLAGGED",
        "output": flagged_output,
        "issues": validation_results["issues"],
        "message": f"⚠️  Output has {len(validation_results['issues'])} minor issues"
    }
```

### Action 3: Reject (Major Issues)
```python
def handle_reject(output, validation_results):
    """
    Output fails validation - regenerate needed.
    """
    return {
        "status": "REJECTED",
        "output": None,
        "issues": validation_results["issues"],
        "message": "❌ Output failed validation - regenerating...",
        "action": "regenerate_with_feedback"
    }
```

## Automated Fixes

Some issues can be auto-fixed:

```python
def auto_fix_issues(output, issues):
    """
    Automatically fix common issues.
    """
    fixed = output
    
    # Fix markdown syntax errors
    if "invalid_markdown" in issues:
        fixed = fix_markdown_syntax(fixed)
    
    # Fix code formatting
    if "formatting_issues" in issues:
        fixed = auto_format_code(fixed)
    
    # Add missing docstrings (templates)
    if "missing_docstrings" in issues:
        fixed = add_docstring_templates(fixed)
    
    # Fix citation formatting
    if "citation_format" in issues:
        fixed = standardize_citations(fixed)
    
    # Remove placeholder text
    if "has_placeholders" in issues:
        fixed = remove_placeholders(fixed)
    
    return fixed
```

## Validation Pipeline

```python
def validate_gemini_output(output, task_type, requirements):
    """
    Main validation pipeline.
    """
    # Step 1: Quick sanity checks
    if not output or len(output.strip()) < 10:
        return {
            "status": "REJECTED",
            "reason": "Output too short or empty"
        }
    
    # Step 2: Task-specific validation
    validators = {
        "research": validate_research,
        "code": validate_code,
        "debug": validate_debug,
        "report": validate_report
    }
    
    validator = validators.get(task_type, validate_generic)
    validation = validator(output)
    
    # Step 3: Critical checks (always run)
    critical_checks = {
        "no_error_messages": not contains_error_messages(output),
        "appropriate_language": not contains_inappropriate_content(output),
        "meets_requirements": check_requirements(output, requirements)
    }
    
    validation["checks"].update(critical_checks)
    
    # Step 4: Determine action
    if not validation["valid"]:
        return handle_reject(output, validation)
    elif len(validation["issues"]) > 0:
        return handle_flag(output, validation)
    else:
        return handle_pass(output, validation)
```

## Integration Example

```python
# After Gemini execution
gemini_output = run_gemini_cli(prompt)

# Automatic validation
validation = result_validator.validate(
    output=gemini_output,
    task_type="research",
    requirements=original_requirements
)

if validation["status"] == "PASS":
    # Present to user
    present_to_user(validation["output"])
    
elif validation["status"] == "FLAGGED":
    # Try auto-fix first
    fixed = auto_fix_issues(gemini_output, validation["issues"])
    
    # Re-validate
    revalidation = result_validator.validate(fixed, task_type, requirements)
    
    if revalidation["status"] == "PASS":
        present_to_user(fixed)
    else:
        # Present with warnings
        present_to_user_with_warnings(fixed, validation["issues"])

elif validation["status"] == "REJECTED":
    # Regenerate with improved prompt
    improved_prompt = create_improved_prompt(prompt, validation["issues"])
    gemini_output = run_gemini_cli(improved_prompt)
    # Validate again...
```

## Configuration

```json
{
  "result_validator": {
    "enabled": true,
    "strictness": "medium",
    "auto_fix": true,
    "citation_verification": true,
    "security_scanning": true,
    "max_regenerations": 2,
    "validation_timeout": 30,
    "checks": {
      "syntax": true,
      "completeness": true,
      "quality": true,
      "security": true,
      "citations": true
    }
  }
}
```

## Performance Metrics

```python
{
  "total_validations": 1247,
  "pass_rate": 0.73,
  "flag_rate": 0.22,
  "reject_rate": 0.05,
  "auto_fix_success": 0.68,
  "average_validation_time": "1.2s",
  "issues_detected": {
    "syntax_errors": 23,
    "missing_citations": 45,
    "incomplete_output": 12,
    "hallucinated_citations": 8,
    "security_issues": 3
  }
}
```

## User Notifications

```
🔍 Result Validator
├─ Validation: Complete
├─ Status: ✅ PASS
├─ Quality Score: 8.7/10
└─ Issues: None

Output is ready for use.
```

Or with issues:
```
🔍 Result Validator
├─ Validation: Complete
├─ Status: ⚠️  FLAGGED
├─ Quality Score: 7.2/10
└─ Issues Found:
    • 2 citations need verification
    • Missing conclusion section
    
Auto-fixing: ✓ Added conclusion template
Manual review recommended for citations.
```

## Advanced Features

### Learning System
Tracks common validation failures and adjusts Gemini prompts proactively.

### Custom Validators
Users can add domain-specific validation rules.

### Validation History
Maintains history for trend analysis and improvement.

### Quality Trends
Reports on quality improvements over time.
