# Debug Command

Delegate complex debugging tasks to Gemini Pro for comprehensive error analysis.

## Usage
```
/gemini:debug [--error=<error_file>] [--code=<code_file>] [--logs=<log_file>] [--fix]
```

## Description
This command handles:
- Error trace analysis
- Root cause identification
- Multi-file debugging
- Performance profiling
- Memory leak detection
- Race condition analysis

## Parameters
- `--error`: Error message or traceback file
- `--code`: Source code file to debug
- `--logs`: Log file for context
- `--fix`: Automatically apply fixes (default: false)

## Workflow
1. **Claude gathers context**:
   - Reads error messages
   - Analyzes stack traces
   - Reviews related code
   - Checks recent changes (git)
2. **Creates debugging prompt** for Gemini:
   - Full error context
   - Relevant code sections
   - System environment
   - Expected vs actual behavior
3. **Gemini analyzes**:
   ```bash
   gemini -m pro -p "DEBUG_PROMPT" -o json > analysis.json
   ```
4. **Claude validates** the analysis:
   - Checks plausibility
   - Verifies suggested fixes
   - Tests fix safety
5. **Applies fixes** (if --fix flag):
   - Creates backup
   - Applies changes
   - Runs tests
   - Rollback if tests fail

## Example Debug Session

### Input
```bash
/gemini:debug --error=error.log --code=forecaster.py --fix
```

### Claude's Context Gathering
```python
# Claude automatically collects:
{
  "error_message": "RuntimeError: Expected tensor shape (32, 100, 10) but got (32, 100, 8)",
  "stack_trace": "Full traceback...",
  "code_section": "Relevant code around line 145...",
  "git_recent": "Last 5 commits affecting this file",
  "environment": {
    "python": "3.10.12",
    "pytorch": "2.1.0",
    "cuda": "12.1"
  }
}
```

### Gemini's Debug Prompt (Auto-generated)
```
You are debugging a PyTorch time series forecasting model.

ERROR:
RuntimeError: Expected tensor shape (32, 100, 10) but got (32, 100, 8)

STACK TRACE:
[Full trace]

CODE CONTEXT:
```python
[Relevant code sections]
```

RECENT CHANGES:
- Commit abc123: Modified feature dimension handling
- Commit def456: Updated preprocessing pipeline

TASK:
1. Identify the root cause of the dimension mismatch
2. Explain why this happens
3. Provide a fix with explanation
4. Suggest preventive measures

OUTPUT FORMAT (JSON):
{
  "root_cause": "Detailed explanation",
  "why_it_happens": "Step-by-step reasoning",
  "fix": {
    "file": "filename",
    "changes": [
      {
        "line": 145,
        "old": "original code",
        "new": "fixed code",
        "reason": "why this fixes it"
      }
    ]
  },
  "prevention": ["Suggestion 1", "Suggestion 2"],
  "test_cases": ["Test to verify fix"]
}
```

### Gemini's Response (Example)
```json
{
  "root_cause": "The model expects 10 features but preprocessing outputs only 8 features after the recent commit abc123 changed the feature selection logic",
  "why_it_happens": "In commit abc123, the feature selection was updated to exclude 'feature_7' and 'feature_8' for correlation reasons, but the model architecture wasn't updated accordingly",
  "fix": {
    "file": "forecaster.py",
    "changes": [
      {
        "line": 45,
        "old": "self.input_dim = 10",
        "new": "self.input_dim = 8  # Updated to match preprocessed features",
        "reason": "Match the actual feature count from preprocessing"
      },
      {
        "line": 78,
        "old": "self.projection = nn.Linear(10, hidden_dim)",
        "new": "self.projection = nn.Linear(8, hidden_dim)",
        "reason": "Align input dimension with model architecture"
      }
    ]
  },
  "prevention": [
    "Add assertion: assert x.shape[-1] == self.input_dim",
    "Create integration test between preprocessing and model",
    "Add config validation at initialization"
  ],
  "test_cases": [
    "Test with random input of shape (32, 100, 8)",
    "Verify forward pass completes without errors",
    "Check output shape matches expected (32, pred_len, 8)"
  ]
}
```

### Claude's Validation & Application
```python
# Claude reviews the fix
def validate_fix(analysis):
    # 1. Check if fix addresses root cause
    if not logically_sound(analysis):
        return False
    
    # 2. Verify no breaking changes
    if introduces_new_issues(analysis['fix']):
        return False
    
    # 3. Safety check
    if affects_critical_sections(analysis['fix']):
        require_manual_review()
        return False
    
    return True

# If validated and --fix flag:
if validate_fix(analysis) and args.fix:
    backup_file('forecaster.py')
    apply_changes(analysis['fix'])
    run_tests(analysis['test_cases'])
```

## Debug Categories

### 1. Runtime Errors
- Exception analysis
- Traceback parsing
- State reconstruction

### 2. Logic Errors
- Unexpected output
- Edge case failures
- Algorithm correctness

### 3. Performance Issues
- Profiling analysis
- Bottleneck identification
- Optimization suggestions

### 4. Memory Issues
- Leak detection
- OOM debugging
- Memory profiling

### 5. Concurrency Issues
- Race conditions
- Deadlock analysis
- Thread safety

## Implementation

```bash
#!/bin/bash
# Conceptual implementation

# Gather all debugging context
CONTEXT=$(claude_gather_debug_context "$@")

# Create comprehensive debug prompt
PROMPT=$(claude_create_debug_prompt "$CONTEXT")

# Execute Gemini with extended reasoning
gemini -m pro -p "$PROMPT" -o json > debug_analysis.json

# Claude validates and optionally applies
claude_validate_and_apply debug_analysis.json "$@"
```

## Success Criteria
- ✅ Root cause correctly identified
- ✅ Fix is logically sound
- ✅ No regression introduced
- ✅ Tests pass after fix
- ✅ Prevention measures suggested

## Safety Features
1. **Automatic backup** before any changes
2. **Test execution** before finalizing
3. **Rollback mechanism** if tests fail
4. **Manual review** for critical sections
5. **Change summary** for review

## Error Handling
- **Ambiguous errors**: Request more context from user
- **Multiple possible causes**: Gemini provides ranked list
- **Fix validation fails**: Present analysis without applying
- **Tests fail post-fix**: Automatic rollback + report
