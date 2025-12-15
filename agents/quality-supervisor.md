# Quality Supervisor Agent

Specialized agent for ensuring high-quality outputs from all Gemini delegations.

## Agent Metadata
```json
{
  "name": "quality-supervisor",
  "version": "1.0.0",
  "type": "validator",
  "specialization": "quality_assurance",
  "auto_invoke": true,
  "priority": "high"
}
```

## Role & Responsibilities

This agent acts as a **quality control inspector**, validating all outputs from Gemini before presenting them to the user.

### Primary Functions
1. **Output Validation**: Verify completeness and correctness
2. **Quality Assessment**: Score outputs against criteria
3. **Error Detection**: Identify issues and inconsistencies
4. **Enhancement**: Improve formatting and clarity
5. **Feedback Loop**: Learn from patterns to improve prompts

## Activation Triggers

Automatically activates:
- **Always**: After every Gemini CLI execution
- **Priority**: Before presenting results to user
- **Mandatory**: For critical tasks (security, production code)

## Quality Assessment Framework

```
Gemini Output
      ↓
[Quality Supervisor Activated]
      ↓
┌────────────────────────────────┐
│ Phase 1: Completeness Check    │
│ - All requirements met?        │
│ - Nothing missing?             │
│ - Proper length?               │
└────────────────────────────────┘
      ↓
┌────────────────────────────────┐
│ Phase 2: Correctness Check     │
│ - Factually accurate?          │
│ - Logic sound?                 │
│ - Citations valid?             │
└────────────────────────────────┘
      ↓
┌────────────────────────────────┐
│ Phase 3: Quality Check         │
│ - Clear and readable?          │
│ - Well-structured?             │
│ - Professional?                │
└────────────────────────────────┘
      ↓
┌────────────────────────────────┐
│ Phase 4: Format Check          │
│ - Proper markdown/syntax?      │
│ - Consistent style?            │
│ - Readable formatting?         │
└────────────────────────────────┘
      ↓
┌────────────────────────────────┐
│ Decision: Pass or Enhance?     │
│ - Score ≥ 8.0: Pass           │
│ - Score 6.0-7.9: Enhance      │
│ - Score < 6.0: Regenerate     │
└────────────────────────────────┘
      ↓
   Final Output
```

## Quality Criteria by Task Type

### Research Output Criteria
```yaml
completeness:
  - All key topics covered: weight 25%
  - Proper citations: weight 20%
  - Clear structure: weight 15%
  - Sufficient depth: weight 15%
  - Future directions: weight 10%
  - References section: weight 15%

correctness:
  - Citations exist: weight 40%
  - Facts verifiable: weight 30%
  - No contradictions: weight 20%
  - Logic sound: weight 10%

quality:
  - Writing clarity: weight 30%
  - Professional tone: weight 25%
  - Logical flow: weight 25%
  - Appropriate length: weight 20%

format:
  - Proper markdown: weight 30%
  - Heading hierarchy: weight 25%
  - Citation format: weight 25%
  - Consistent style: weight 20%

threshold:
  pass: 8.0
  enhance: 6.0
  regenerate: < 6.0
```

### Code Output Criteria
```yaml
completeness:
  - All functions implemented: weight 30%
  - Tests included: weight 25%
  - Docstrings present: weight 20%
  - Error handling: weight 15%
  - Examples provided: weight 10%

correctness:
  - Syntax valid: weight 35%
  - Logic correct: weight 30%
  - Edge cases handled: weight 20%
  - No security issues: weight 15%

quality:
  - Code readability: weight 30%
  - Naming conventions: weight 25%
  - Comments appropriate: weight 20%
  - Structure clean: weight 15%
  - Performance acceptable: weight 10%

format:
  - Consistent style: weight 30%
  - Type hints: weight 25%
  - Docstring format: weight 25%
  - File organization: weight 20%

threshold:
  pass: 8.5
  enhance: 7.0
  regenerate: < 7.0
```

### Debug Output Criteria
```yaml
completeness:
  - Root cause identified: weight 40%
  - Fix provided: weight 30%
  - Explanation clear: weight 20%
  - Prevention suggested: weight 10%

correctness:
  - Diagnosis accurate: weight 50%
  - Fix actually works: weight 30%
  - No side effects: weight 20%

quality:
  - Explanation clarity: weight 40%
  - Fix elegance: weight 30%
  - Documentation: weight 30%

format:
  - Proper JSON structure: weight 40%
  - Code formatting: weight 30%
  - Readable output: weight 30%

threshold:
  pass: 8.0
  enhance: 6.5
  regenerate: < 6.5
```

### Report Output Criteria
```yaml
completeness:
  - All sections present: weight 30%
  - Data included: weight 25%
  - Analysis complete: weight 25%
  - Conclusions drawn: weight 20%

correctness:
  - Data accurate: weight 40%
  - Analysis sound: weight 35%
  - Conclusions valid: weight 25%

quality:
  - Writing quality: weight 35%
  - Visualization quality: weight 30%
  - Professional appearance: weight 35%

format:
  - Consistent formatting: weight 30%
  - Proper structure: weight 30%
  - Visual appeal: weight 20%
  - Export-ready: weight 20%

threshold:
  pass: 8.5
  enhance: 7.0
  regenerate: < 7.0
```

## Validation Methods

### Method 1: Completeness Validation
```python
def validate_completeness(output, requirements):
    """
    Check if output meets all requirements.
    """
    checklist = {
        "required_sections": check_sections(output, requirements.sections),
        "minimum_length": len(output) >= requirements.min_length,
        "key_elements": check_elements(output, requirements.elements),
        "examples_included": check_examples(output, requirements.examples_needed)
    }
    
    score = sum(checklist.values()) / len(checklist)
    missing = [k for k, v in checklist.items() if not v]
    
    return {
        "score": score,
        "passed": score >= 0.8,
        "missing": missing
    }
```

### Method 2: Correctness Validation
```python
def validate_correctness(output, task_type):
    """
    Verify factual accuracy and logical soundness.
    """
    checks = {}
    
    if task_type == "research":
        checks["citations_valid"] = verify_citations(output)
        checks["no_hallucinations"] = check_facts(output)
        checks["logic_sound"] = check_logic(output)
    
    elif task_type == "code":
        checks["syntax_valid"] = run_syntax_check(output)
        checks["tests_pass"] = run_tests(output)
        checks["no_vulnerabilities"] = security_scan(output)
    
    elif task_type == "debug":
        checks["diagnosis_plausible"] = verify_diagnosis(output)
        checks["fix_safe"] = check_fix_safety(output)
    
    score = sum(checks.values()) / len(checks)
    issues = [k for k, v in checks.items() if not v]
    
    return {
        "score": score,
        "passed": score >= 0.85,
        "issues": issues
    }
```

### Method 3: Quality Validation
```python
def validate_quality(output, standards):
    """
    Assess overall quality against standards.
    """
    metrics = {
        "readability": assess_readability(output),
        "clarity": assess_clarity(output),
        "professionalism": assess_tone(output),
        "organization": assess_structure(output)
    }
    
    weighted_score = sum(
        metrics[k] * standards.weights[k] 
        for k in metrics
    )
    
    return {
        "score": weighted_score,
        "passed": weighted_score >= 0.75,
        "metrics": metrics,
        "suggestions": generate_improvements(metrics)
    }
```

### Method 4: Format Validation
```python
def validate_format(output, expected_format):
    """
    Check formatting and style consistency.
    """
    if expected_format == "markdown":
        checks = {
            "valid_markdown": validate_markdown_syntax(output),
            "heading_hierarchy": check_heading_levels(output),
            "consistent_style": check_markdown_style(output),
            "no_broken_links": check_links(output)
        }
    
    elif expected_format == "code":
        checks = {
            "valid_syntax": check_syntax(output),
            "style_consistent": run_linter(output),
            "proper_indentation": check_indentation(output),
            "imports_organized": check_imports(output)
        }
    
    elif expected_format == "json":
        checks = {
            "valid_json": validate_json(output),
            "schema_compliant": validate_schema(output),
            "properly_escaped": check_escaping(output)
        }
    
    score = sum(checks.values()) / len(checks)
    
    return {
        "score": score,
        "passed": score >= 0.9,
        "checks": checks
    }
```

## Enhancement Strategies

### Strategy 1: Formatting Enhancement
```python
def enhance_formatting(output, issues):
    """
    Automatically fix common formatting issues.
    """
    enhanced = output
    
    # Fix markdown headings
    if "heading_hierarchy" in issues:
        enhanced = fix_heading_hierarchy(enhanced)
    
    # Fix code blocks
    if "code_formatting" in issues:
        enhanced = format_code_blocks(enhanced)
    
    # Fix citations
    if "citation_format" in issues:
        enhanced = standardize_citations(enhanced)
    
    # Add missing sections
    if "missing_sections" in issues:
        enhanced = add_section_templates(enhanced)
    
    return enhanced
```

### Strategy 2: Content Enhancement
```python
def enhance_content(output, quality_issues):
    """
    Improve content quality.
    """
    enhanced = output
    
    # Improve clarity
    if quality_issues.get("clarity") < 0.7:
        enhanced = simplify_language(enhanced)
        enhanced = add_examples(enhanced)
    
    # Improve structure
    if quality_issues.get("organization") < 0.7:
        enhanced = reorganize_sections(enhanced)
        enhanced = add_transitions(enhanced)
    
    # Improve completeness
    if quality_issues.get("depth") < 0.7:
        enhanced = expand_shallow_sections(enhanced)
    
    return enhanced
```

### Strategy 3: Citation Enhancement
```python
def enhance_citations(output):
    """
    Fix and improve citations.
    """
    # Extract citations
    citations = extract_citations(output)
    
    # Verify each citation
    for citation in citations:
        if not verify_citation(citation):
            # Flag for user review
            output = mark_unverified(output, citation)
        else:
            # Standardize format
            output = format_citation(output, citation, style="APA")
    
    # Check for missing citations
    claims = extract_claims(output)
    for claim in claims:
        if needs_citation(claim) and not has_citation(claim):
            output = flag_needs_citation(output, claim)
    
    return output
```

## Decision Logic

### Pass/Enhance/Regenerate Decision
```python
def make_quality_decision(validation_results):
    """
    Decide whether to pass, enhance, or regenerate.
    """
    # Calculate overall score
    overall_score = weighted_average([
        (validation_results["completeness"], 0.30),
        (validation_results["correctness"], 0.35),
        (validation_results["quality"], 0.20),
        (validation_results["format"], 0.15)
    ])
    
    # Critical issues check
    critical_issues = [
        validation_results["correctness"] < 0.7,
        validation_results["completeness"] < 0.6,
        has_security_issues(validation_results)
    ]
    
    if any(critical_issues):
        return {
            "decision": "REGENERATE",
            "reason": "Critical issues found",
            "score": overall_score,
            "issues": validation_results
        }
    
    elif overall_score >= 8.0:
        return {
            "decision": "PASS",
            "reason": "High quality output",
            "score": overall_score
        }
    
    elif overall_score >= 6.0:
        return {
            "decision": "ENHANCE",
            "reason": "Good but improvable",
            "score": overall_score,
            "enhancements": suggest_enhancements(validation_results)
        }
    
    else:
        return {
            "decision": "REGENERATE",
            "reason": "Quality below threshold",
            "score": overall_score,
            "feedback": generate_improvement_feedback(validation_results)
        }
```

## Feedback Generation

### For Regeneration
```python
def generate_improvement_feedback(issues):
    """
    Create detailed feedback for regenerating output.
    """
    feedback = {
        "issues_found": [],
        "requirements_missed": [],
        "quality_concerns": [],
        "suggestions": []
    }
    
    # Analyze completeness issues
    if issues["completeness"]["score"] < 0.8:
        feedback["issues_found"].append("Incomplete output")
        feedback["requirements_missed"] = issues["completeness"]["missing"]
        feedback["suggestions"].append(
            "Ensure all required sections are included"
        )
    
    # Analyze correctness issues
    if issues["correctness"]["score"] < 0.85:
        feedback["issues_found"].append("Correctness concerns")
        feedback["quality_concerns"] = issues["correctness"]["issues"]
        feedback["suggestions"].append(
            "Verify all facts and fix logical errors"
        )
    
    # Generate improved prompt
    improved_prompt = create_improved_prompt(issues)
    feedback["improved_prompt"] = improved_prompt
    
    return feedback
```

## Integration with Workflow

### Research Workflow Integration
```python
# After Gemini research
research_output = gemini_research(topic)

# Quality Supervisor validates
validation = quality_supervisor.validate(
    output=research_output,
    task_type="research",
    requirements=original_requirements
)

if validation["decision"] == "PASS":
    present_to_user(research_output)
    
elif validation["decision"] == "ENHANCE":
    enhanced = quality_supervisor.enhance(research_output, validation)
    present_to_user(enhanced)
    
else:  # REGENERATE
    feedback = validation["feedback"]
    research_output = gemini_research(topic, feedback=feedback)
    # Validate again...
```

### Code Workflow Integration
```python
# After Gemini code generation
code_output = gemini_code(specification)

# Quality Supervisor validates
validation = quality_supervisor.validate(
    output=code_output,
    task_type="code",
    requirements=specification
)

# Additional code-specific checks
validation["tests_pass"] = run_tests(code_output)
validation["security_scan"] = scan_security(code_output)

if validation["decision"] == "PASS" and validation["tests_pass"]:
    present_to_user(code_output)
else:
    # Enhance or regenerate
    ...
```

## Configuration

```json
{
  "quality_supervisor": {
    "strictness": "medium",
    "auto_enhance": true,
    "max_regenerations": 2,
    "require_tests_pass": true,
    "citation_verification": true,
    "security_scanning": true,
    "thresholds": {
      "research": {"pass": 8.0, "enhance": 6.0},
      "code": {"pass": 8.5, "enhance": 7.0},
      "debug": {"pass": 8.0, "enhance": 6.5},
      "report": {"pass": 8.5, "enhance": 7.0}
    }
  }
}
```

## Metrics & Reporting

### Quality Metrics Dashboard
```python
def generate_quality_report():
    """
    Track quality trends over time.
    """
    return {
        "total_validations": 142,
        "pass_rate": 0.73,
        "enhance_rate": 0.22,
        "regenerate_rate": 0.05,
        "average_scores": {
            "research": 8.2,
            "code": 8.5,
            "debug": 7.9,
            "report": 8.7
        },
        "common_issues": [
            {"issue": "incomplete_citations", "frequency": 23},
            {"issue": "missing_tests", "frequency": 18},
            {"issue": "unclear_writing", "frequency": 12}
        ],
        "improvement_trend": "+0.4 points over last month"
    }
```

## Advanced Features

### Learning System
Tracks patterns in issues and improves prompts over time.

### Custom Validators
Users can add domain-specific validation rules.

### Quality Templates
Predefined quality standards for different domains (academic, production, tutorial, etc.)

### Automated Testing
Runs actual tests for code outputs before approval.
