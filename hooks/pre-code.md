# Pre-Code Hook

Automatically runs before any code generation to ensure optimal setup and planning.

## Hook Metadata
```json
{
  "name": "pre-code",
  "version": "1.0.0",
  "type": "pre-execution",
  "trigger": "before_code_generation",
  "auto_run": true,
  "can_block": true
}
```

## Purpose

This hook **prepares the environment and validates requirements** before delegating code generation to Gemini, preventing common issues and ensuring high-quality output.

## Trigger Conditions

Activates before:
- `/gemini:code` command execution
- Any auto-delegated code generation
- Refactoring tasks
- Code review with fixes

## Pre-Flight Checklist

```
Code Request Received
        ↓
[Pre-Code Hook Activated]
        ↓
┌──────────────────────────────────┐
│ 1. Requirement Analysis          │
│ - Specifications clear?          │
│ - Ambiguities resolved?          │
│ - Edge cases identified?         │
└──────────────────────────────────┘
        ↓
┌──────────────────────────────────┐
│ 2. Environment Check             │
│ - Dependencies available?        │
│ - Conflicting code exists?       │
│ - Naming conflicts?              │
└──────────────────────────────────┘
        ↓
┌──────────────────────────────────┐
│ 3. Architecture Review           │
│ - Design pattern selected?       │
│ - Interfaces defined?            │
│ - Testing strategy planned?      │
└──────────────────────────────────┘
        ↓
┌──────────────────────────────────┐
│ 4. Best Practice Check           │
│ - Security considerations?       │
│ - Performance requirements?      │
│ - Error handling plan?           │
└──────────────────────────────────┘
        ↓
┌──────────────────────────────────┐
│ Decision: Proceed or Improve?    │
│ - All checks pass: Proceed       │
│ - Issues found: Enhance prompt   │
│ - Critical gaps: Request clarity │
└──────────────────────────────────┘
        ↓
    Code Generation
```

## Check Categories

### 1. Requirement Clarity Check

```python
def check_requirement_clarity(request):
    """
    Ensure requirements are clear and complete.
    """
    issues = []
    
    # Check for vague terms
    vague_terms = ["something", "some kind of", "maybe", "probably"]
    if any(term in request.lower() for term in vague_terms):
        issues.append({
            "type": "vague_requirement",
            "severity": "medium",
            "message": "Request contains vague terms. Need more specificity.",
            "suggestion": "Please specify exact requirements"
        })
    
    # Check for undefined acronyms
    acronyms = extract_acronyms(request)
    undefined = [a for a in acronyms if a not in KNOWN_ACRONYMS]
    if undefined:
        issues.append({
            "type": "undefined_terms",
            "severity": "high",
            "message": f"Acronyms need definition: {undefined}",
            "suggestion": f"Please define: {', '.join(undefined)}"
        })
    
    # Check for missing specs
    if not has_function_specs(request) and is_complex_task(request):
        issues.append({
            "type": "missing_specs",
            "severity": "high",
            "message": "Complex task needs detailed specifications",
            "suggestion": "Specify function signatures, inputs, outputs"
        })
    
    return {
        "clear": len(issues) == 0,
        "issues": issues,
        "score": calculate_clarity_score(issues)
    }
```

### 2. Environment Check

```python
def check_environment(language, requirements):
    """
    Verify development environment is ready.
    """
    checks = {}
    
    # Check language/framework availability
    if language == "python":
        checks["python_version"] = check_python_version()
        checks["pip_available"] = shutil.which("pip") is not None
    
    # Check for required dependencies
    required_deps = extract_dependencies(requirements)
    checks["dependencies_available"] = all(
        is_package_available(dep) for dep in required_deps
    )
    
    # Check for naming conflicts
    module_name = extract_module_name(requirements)
    if module_name:
        checks["no_name_conflict"] = not file_exists(f"{module_name}.py")
    
    # Check write permissions
    output_path = determine_output_path(requirements)
    checks["can_write"] = os.access(output_path, os.W_OK)
    
    return {
        "ready": all(checks.values()),
        "checks": checks,
        "issues": [k for k, v in checks.items() if not v]
    }
```

### 3. Architecture Review

```python
def review_architecture(request):
    """
    Ensure proper architecture planning.
    """
    suggestions = []
    
    # Check if design pattern would help
    if is_complex_system(request):
        patterns = suggest_design_patterns(request)
        if patterns:
            suggestions.append({
                "type": "design_pattern",
                "severity": "medium",
                "message": "Consider using design patterns",
                "patterns": patterns,
                "benefit": "Improves maintainability and extensibility"
            })
    
    # Check if interfaces are defined
    if needs_interfaces(request) and not has_interfaces(request):
        suggestions.append({
            "type": "missing_interfaces",
            "severity": "high",
            "message": "Define interfaces before implementation",
            "suggestion": "Specify public APIs and contracts"
        })
    
    # Check for testing strategy
    if not mentions_testing(request):
        suggestions.append({
            "type": "testing_strategy",
            "severity": "medium",
            "message": "Testing strategy not specified",
            "suggestion": "Add: 'Include unit tests for all public methods'"
        })
    
    # Check for error handling
    if not mentions_error_handling(request):
        suggestions.append({
            "type": "error_handling",
            "severity": "medium",
            "message": "Error handling not specified",
            "suggestion": "Specify how to handle edge cases and errors"
        })
    
    return {
        "well_planned": len(suggestions) == 0,
        "suggestions": suggestions
    }
```

### 4. Best Practice Check

```python
def check_best_practices(request, language):
    """
    Verify best practices are considered.
    """
    recommendations = []
    
    # Security checks
    if involves_user_input(request) and not mentions_validation(request):
        recommendations.append({
            "category": "security",
            "priority": "critical",
            "message": "Input validation not mentioned",
            "action": "Add: 'Validate and sanitize all user inputs'"
        })
    
    if involves_database(request) and not mentions_parameterization(request):
        recommendations.append({
            "category": "security",
            "priority": "critical",
            "message": "SQL injection risk",
            "action": "Add: 'Use parameterized queries'"
        })
    
    # Performance checks
    if is_data_intensive(request) and not mentions_optimization(request):
        recommendations.append({
            "category": "performance",
            "priority": "medium",
            "message": "Performance not considered",
            "action": "Specify performance requirements or constraints"
        })
    
    # Maintainability checks
    if is_large_codebase(request) and not mentions_documentation(request):
        recommendations.append({
            "category": "maintainability",
            "priority": "high",
            "message": "Documentation not specified",
            "action": "Add: 'Include comprehensive docstrings'"
        })
    
    # Language-specific best practices
    if language == "python":
        if not mentions_type_hints(request):
            recommendations.append({
                "category": "code_quality",
                "priority": "medium",
                "message": "Type hints not mentioned",
                "action": "Add: 'Use type hints for all functions'"
            })
    
    return {
        "follows_best_practices": len([r for r in recommendations if r["priority"] == "critical"]) == 0,
        "recommendations": recommendations
    }
```

## Prompt Enhancement

Based on checks, automatically enhance the prompt:

```python
def enhance_prompt_with_findings(original_request, findings):
    """
    Augment the original request with missing best practices.
    """
    enhancements = []
    
    # Add missing specifications
    if not findings["clarity"]["clear"]:
        for issue in findings["clarity"]["issues"]:
            if issue["type"] == "missing_specs":
                enhancements.append("\nDetailed Requirements:")
                enhancements.append("- Specify all function signatures")
                enhancements.append("- Define input/output types")
                enhancements.append("- List edge cases to handle")
    
    # Add architecture guidance
    if findings["architecture"]["suggestions"]:
        enhancements.append("\nArchitecture Guidelines:")
        for suggestion in findings["architecture"]["suggestions"]:
            if suggestion["type"] == "design_pattern":
                enhancements.append(f"- Consider: {suggestion['patterns'][0]}")
            elif suggestion["type"] == "testing_strategy":
                enhancements.append("- Include comprehensive unit tests")
    
    # Add best practices
    if findings["best_practices"]["recommendations"]:
        enhancements.append("\nBest Practices:")
        for rec in findings["best_practices"]["recommendations"]:
            if rec["priority"] in ["critical", "high"]:
                enhancements.append(f"- {rec['action']}")
    
    # Combine
    enhanced = original_request
    if enhancements:
        enhanced += "\n\n" + "\n".join(enhancements)
    
    return enhanced
```

## Examples

### Example 1: Vague Request

**Original Request:**
```
"Create some kind of user authentication system"
```

**Pre-Code Hook Findings:**
```yaml
clarity:
  clear: false
  issues:
    - type: vague_requirement
      message: "Request contains vague terms: 'some kind of'"
    - type: missing_specs
      message: "Authentication method not specified"

suggestions:
  - Specify authentication method (JWT, session, OAuth)
  - Define user model fields
  - Specify password requirements
  - List required endpoints
```

**Enhanced Request:**
```
Create a user authentication system

Requirements:
- Authentication: JWT-based
- User model: email, password_hash, created_at
- Password: Min 8 chars, bcrypt hashing
- Endpoints: /register, /login, /logout, /refresh
- Include: Input validation, rate limiting
- Tests: Unit tests for all endpoints
```

### Example 2: Security Concerns

**Original Request:**
```
"Build a search function that queries the database"
```

**Pre-Code Hook Findings:**
```yaml
best_practices:
  critical_issues:
    - category: security
      message: "SQL injection risk - no parameterization mentioned"
    - category: security
      message: "Input validation not specified"

environment:
  warnings:
    - "Database connection not specified"
```

**Enhanced Request:**
```
Build a search function that queries the database

Requirements:
- Use parameterized queries (prevent SQL injection)
- Validate and sanitize search input
- Database: Specify connection method
- Error handling: Database errors, empty results
- Performance: Add pagination for large results
- Tests: Include SQL injection attempt tests
```

### Example 3: Missing Architecture

**Original Request:**
```
"Implement a complete e-commerce backend"
```

**Pre-Code Hook Findings:**
```yaml
architecture:
  suggestions:
    - type: design_pattern
      patterns: ["Layered Architecture", "Repository Pattern"]
    - type: missing_interfaces
      message: "Define service interfaces before implementation"
    
clarity:
  issues:
    - type: too_broad
      message: "Scope too large - break into components"
```

**Enhanced Request:**
```
Implement e-commerce backend - Phase 1: Core Services

Architecture:
- Layered: API Layer → Service Layer → Repository Layer
- Patterns: Repository for data access, Service for business logic

Components (Phase 1):
1. Product Service
   - Interface: IProductService
   - Methods: create, get, update, delete, search
   
2. Cart Service
   - Interface: ICartService
   - Methods: add_item, remove_item, get_cart, clear
   
3. Order Service
   - Interface: IOrderService
   - Methods: create_order, get_order, cancel_order

For each service:
- Complete implementation with error handling
- Repository layer for data access
- Unit tests
- Input validation

Start with Product Service.
```

## User Interaction

### When Issues Are Found

```
⚠️  Pre-Code Analysis

Found 3 issues that could affect code quality:

1. [HIGH] Missing specifications
   → Please specify authentication method (JWT, session, OAuth?)

2. [CRITICAL] Security concern
   → Input validation not mentioned
   → Recommendation: Add input validation requirements

3. [MEDIUM] Testing strategy
   → No testing mentioned
   → Recommendation: Request unit tests

Would you like to:
a) Proceed with enhanced prompt (recommended)
b) Provide more details manually
c) Proceed anyway (not recommended)

[Enhanced prompt preview]:
{enhanced_prompt}
```

### When All Clear

```
✅ Pre-Code Analysis Complete

All checks passed:
✓ Requirements clear
✓ Environment ready
✓ Architecture planned
✓ Best practices considered

Proceeding with code generation...
```

## Configuration

```json
{
  "pre_code_hook": {
    "enabled": true,
    "auto_enhance": true,
    "require_user_approval": false,
    "strictness": "medium",
    "checks": {
      "clarity": true,
      "environment": true,
      "architecture": true,
      "best_practices": true,
      "security": true
    },
    "block_on_critical": true,
    "interactive_mode": true
  }
}
```

## Integration

```python
# Before code generation
@before_code_generation
def run_pre_code_hook(request, context):
    """
    Automatically runs before any code generation.
    """
    # Run all checks
    findings = {
        "clarity": check_requirement_clarity(request),
        "environment": check_environment(context.language, request),
        "architecture": review_architecture(request),
        "best_practices": check_best_practices(request, context.language)
    }
    
    # Determine action
    has_critical = any(
        issue["severity"] == "critical" 
        for check in findings.values() 
        for issue in check.get("issues", []) + check.get("recommendations", [])
    )
    
    if has_critical and config.block_on_critical:
        # Show issues to user, get input
        return handle_critical_issues(findings)
    
    # Auto-enhance prompt
    if config.auto_enhance:
        enhanced = enhance_prompt_with_findings(request, findings)
        return {
            "proceed": True,
            "enhanced_request": enhanced,
            "findings": findings
        }
    
    return {"proceed": True, "request": request}
```

## Performance Metrics

```python
{
  "total_runs": 523,
  "issues_caught": {
    "vague_requirements": 87,
    "security_concerns": 34,
    "missing_architecture": 56,
    "environment_issues": 12
  },
  "auto_enhancements": 412,
  "user_overrides": 15,
  "blocked_critical": 8,
  "average_check_time": "0.3s",
  "quality_improvement": "+23% fewer issues in generated code"
}
```

## Benefits

1. **Fewer iterations**: Get it right the first time
2. **Better security**: Catches security issues before code generation
3. **Clearer specs**: Forces requirement clarification
4. **Better architecture**: Encourages upfront planning
5. **Time savings**: Prevents rework from unclear requirements
