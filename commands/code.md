# Code Command

Delegate complex code generation to Gemini Pro with Claude's architectural guidance.

## Usage
```
/gemini:code <description> [--lang=<language>] [--style=<coding_style>] [--output=<filename>]
```

## Description
This command handles:
- Complex algorithm implementation
- Boilerplate code generation
- Data structure implementation
- API client generation
- Test suite creation
- Large-scale refactoring

## Parameters
- `description`: What to build (required)
- `--lang`: Programming language (auto-detect from context)
- `--style`: Coding style (default: clean)
  - `clean`: Clean code principles
  - `performant`: Performance-optimized
  - `minimal`: Minimal dependencies
  - `research`: Research/experimental code
- `--output`: Output file path (auto-generated if not provided)

## Workflow
1. **Claude creates architecture**:
   - Analyzes requirements
   - Designs module structure
   - Defines interfaces
   - Plans error handling
2. **Generates detailed spec** for Gemini:
   - Function signatures
   - Type definitions
   - Edge cases
   - Test requirements
3. **Gemini implements** the code:
   ```bash
   gemini -m pro -p "DETAILED_SPEC" --approval-mode auto_edit -o text > OUTPUT_FILE
   ```
4. **Claude reviews**:
   - Syntax check
   - Logic verification
   - Security audit
   - Performance assessment
5. **Iterative refinement** if needed

## Example Spec Generation

### Input
```
Create a time series forecasting model with test-time adaptation
```

### Claude-Generated Spec for Gemini
```python
"""
IMPLEMENTATION SPECIFICATION

Task: Time Series Forecasting with Test-Time Adaptation
Language: Python 3.10+
Framework: PyTorch 2.0+

Requirements:
1. Base forecasting model class
   - Input: (batch, seq_len, features)
   - Output: (batch, pred_len, features)
   
2. Test-time adapter
   - Online gradient updates
   - Efficient parameter selection
   - Loss computation on-the-fly

3. Key Methods:
   def __init__(self, input_dim, hidden_dim, output_dim, adapter_rank)
   def forward(self, x)
   def adapt(self, x_test, y_test, lr=0.001, steps=5)
   def reset_adapter(self)

4. Edge Cases:
   - Handle variable sequence lengths
   - Graceful degradation if adaptation fails
   - Memory-efficient batching

5. Testing:
   - Unit tests for each method
   - Integration test with synthetic data
   - Performance benchmark

6. Code Style:
   - Type hints
   - Docstrings (Google style)
   - Error handling with informative messages
   - No hardcoded values

Output Format: Single .py file with complete implementation
"""
```

## Implementation Pattern

```python
# Conceptual - actual execution by Claude Code

def execute_code_generation(description, lang, style, output):
    # Step 1: Claude analyzes and plans
    architecture = claude_design_architecture(description, lang)
    
    # Step 2: Generate detailed specification
    spec = claude_generate_spec(architecture, style)
    
    # Step 3: Create prompt for Gemini
    prompt = f"""
You are an expert {lang} developer. Implement the following specification:

{spec}

Requirements:
- Production-ready code
- Comprehensive error handling
- Type safety
- Well-documented
- Include example usage

Output only the complete, executable code.
"""
    
    # Step 4: Execute Gemini CLI
    run_command(f'gemini -m pro -p "{prompt}" -o text > {output}')
    
    # Step 5: Claude validates
    code = read_file(output)
    issues = claude_review_code(code)
    
    # Step 6: Fix issues if any
    if issues:
        fixed_code = claude_fix_issues(code, issues)
        write_file(output, fixed_code)
    
    return output
```

## Quality Checks (Automated)

Claude performs these checks before finalizing:

1. **Syntax**: `python -m py_compile {file}`
2. **Type hints**: `mypy --strict {file}`
3. **Security**: Check for common vulnerabilities
4. **Performance**: Identify obvious bottlenecks
5. **Style**: `ruff check {file}`

## Success Criteria
- ✅ Code compiles/runs without errors
- ✅ Handles edge cases properly
- ✅ Includes comprehensive tests
- ✅ Well-documented
- ✅ Follows language best practices
- ✅ No security vulnerabilities

## Error Recovery
- **Syntax errors**: Claude fixes directly
- **Logic errors**: Re-prompt Gemini with corrections
- **Missing features**: Append generation for missing parts
- **Performance issues**: Claude optimizes critical sections
