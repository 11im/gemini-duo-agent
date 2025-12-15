# Token Optimizer Skill

Automatically optimizes prompts to minimize token usage while maintaining quality.

## Skill Metadata
```json
{
  "name": "token-optimizer",
  "version": "1.0.0",
  "type": "optimization",
  "auto_trigger": true,
  "priority": "medium",
  "applies_to": ["gemini_prompts", "claude_responses"]
}
```

## Purpose

This skill **reduces token consumption** by up to 40-60% through intelligent prompt compression, context optimization, and output length management.

## Activation Conditions

Automatically activates when:
1. **Before Gemini delegation**: Optimize prompts to Gemini
2. **Large context detected**: >1000 tokens in prompt
3. **Repetitive content**: Duplicate information found
4. **Verbose requests**: Unnecessarily detailed descriptions

## Optimization Strategies

### Strategy 1: Prompt Compression
```python
OPTIMIZATION_RULES = {
    "remove_redundancy": {
        "description": "Remove duplicate information",
        "examples": [
            # Before (82 tokens)
            "I need you to write a comprehensive and detailed report that covers all aspects of machine learning. The report should be thorough and include detailed explanations of various machine learning algorithms.",
            
            # After (31 tokens)
            "Write a comprehensive report on machine learning algorithms with detailed explanations."
        ],
        "savings": "62%"
    },
    
    "eliminate_filler": {
        "description": "Remove unnecessary words",
        "examples": [
            # Before (45 tokens)
            "I was wondering if you could possibly help me understand how neural networks actually work in practice",
            
            # After (12 tokens)
            "Explain how neural networks work in practice"
        ],
        "savings": "73%"
    },
    
    "use_abbreviations": {
        "description": "Use standard abbreviations",
        "examples": [
            # Before
            "Machine Learning, Natural Language Processing, Computer Vision",
            
            # After
            "ML, NLP, CV"
        ],
        "savings": "40%"
    },
    
    "bullet_points": {
        "description": "Convert prose to structured lists",
        "examples": [
            # Before (68 tokens)
            "The function should validate the input to make sure it's not empty, then it should process the data by removing duplicates, and finally it should return the cleaned data sorted in ascending order.",
            
            # After (28 tokens)
            "Function requirements:\n- Validate non-empty input\n- Remove duplicates\n- Return sorted ascending"
        ],
        "savings": "59%"
    }
}
```

### Strategy 2: Context Optimization
```python
def optimize_context(prompt, context_data):
    """
    Include only relevant context, exclude unnecessary details.
    """
    # Analyze what's actually needed
    required_context = extract_required_context(prompt)
    
    # Remove unused context
    optimized = {
        k: v for k, v in context_data.items() 
        if k in required_context
    }
    
    # Summarize large context items
    for key, value in optimized.items():
        if len(value) > 500:  # Long context
            optimized[key] = summarize_context(value, max_tokens=200)
    
    return optimized

# Example
original_context = {
    "file1.py": "3000 tokens of code",  # Only 20 tokens needed
    "file2.py": "2000 tokens of code",  # Not referenced at all
    "requirements.txt": "50 tokens"      # All needed
}

optimized_context = optimize_context(prompt, original_context)
# Result: ~270 tokens instead of 5050 tokens
# Savings: 94.7%
```

### Strategy 3: Output Length Specification
```python
def add_length_constraints(prompt, desired_length):
    """
    Explicitly specify output length to prevent over-generation.
    """
    length_specs = {
        "brief": "Respond in 2-3 sentences.",
        "short": "Limit response to 1 paragraph (4-5 sentences).",
        "medium": "Provide 2-3 paragraphs (max 300 words).",
        "detailed": "Comprehensive response, 500-800 words.",
        "comprehensive": "Detailed analysis, 1000-1500 words."
    }
    
    return f"{prompt}\n\nLength: {length_specs[desired_length]}"

# Example
original_prompt = "Explain machine learning"
# Might generate: 2000 tokens

optimized_prompt = add_length_constraints(
    "Explain machine learning", 
    "brief"
)
# Generates: ~100 tokens
# Savings: 95%
```

### Strategy 4: Template-Based Optimization
```python
OPTIMIZED_TEMPLATES = {
    "research": """
Research: {topic}
Scope: {scope}
Format: {format}
Length: {length}
Focus: {key_points}
""",
    
    "code": """
Implement: {description}
Language: {lang}
Requirements:
{requirements_list}
Output: Complete code with tests
""",
    
    "debug": """
Debug Analysis Required
Error: {error_msg}
Code: {relevant_code_only}  # Not entire file
Task: Root cause + fix + prevention
Format: JSON
""",
    
    "review": """
Review: {path}
Aspects: {aspects}
Severity: >={min_severity}
Format: Issue list with fixes
"""
}

def apply_template(task_type, params):
    """
    Use pre-optimized templates instead of verbose prompts.
    """
    template = OPTIMIZED_TEMPLATES[task_type]
    return template.format(**params)

# Example
verbose_prompt = """
I need you to conduct a comprehensive research survey on the topic of 
test-time adaptation methods for time series forecasting. The research 
should be thorough and detailed, covering all the important aspects...
(150+ tokens)
"""

optimized_prompt = apply_template("research", {
    "topic": "test-time adaptation for time series",
    "scope": "methods, 2023-2024",
    "format": "structured markdown",
    "length": "5-8 pages",
    "key_points": "methodology, results, limitations"
})
# Result: 35 tokens
# Savings: 77%
```

### Strategy 5: Incremental Processing
```python
def optimize_for_large_tasks(task):
    """
    Break large tasks into smaller chunks to reduce token usage per request.
    """
    if estimated_tokens(task) > 2000:
        # Instead of one massive prompt
        chunks = break_into_phases(task)
        
        results = []
        for chunk in chunks:
            # Each chunk uses fewer tokens
            result = process_chunk(chunk)
            results.append(result)
        
        # Combine results
        return synthesize_results(results)
    
    return process_single(task)

# Example: Large codebase review
# Instead of: "Review all 50 files" (10,000 tokens input)
# Do: "Review file1" → "Review file2" → ... (200 tokens each)
# Total: 10,000 tokens input → 10,000 tokens output
# Optimized: 200×50 = 10,000 input → 5,000 tokens output
# Savings: 50% on output tokens
```

## Optimization Examples

### Example 1: Research Request

**Original (178 tokens):**
```
I need you to help me conduct a comprehensive and thorough literature survey 
on the topic of test-time adaptation methods for time series forecasting. 
The survey should be very detailed and should cover all the important aspects 
including the various methodologies that have been proposed, the experimental 
results that researchers have achieved, and any limitations or challenges 
that exist in this area. Please make sure to include proper citations for 
all the papers you reference and organize the information in a clear and 
logical way that would be suitable for inclusion in an academic paper. 
I would appreciate it if you could also provide some insights into future 
research directions.
```

**Optimized (31 tokens):**
```
Research: test-time adaptation for time series
Scope: methods, results, limitations (2023-2024)
Format: academic survey with citations
Length: 8-10 pages
Include: future directions
```

**Savings: 82.6%**

### Example 2: Code Generation

**Original (145 tokens):**
```
I would like you to implement for me a complete binary search tree data 
structure in Python. The implementation should include all the standard 
operations such as inserting new nodes, deleting existing nodes, and 
searching for specific values. Please make sure to include comprehensive 
error handling for edge cases, and also write detailed docstrings for 
each method explaining what it does, what parameters it takes, and what 
it returns. Additionally, please include some unit tests to verify that 
the implementation works correctly.
```

**Optimized (28 tokens):**
```
Implement: Binary Search Tree (Python)
Methods: insert, delete, search
Include: docstrings, error handling, unit tests
Output: Single .py file
```

**Savings: 80.7%**

### Example 3: Debugging

**Original (210 tokens):**
```
I'm encountering an error in my code and I need help debugging it. The error 
message says "RuntimeError: Expected tensor shape (32, 100, 10) but got 
(32, 100, 8)" and it's happening in the model.py file at line 145 in the 
forward method. I think it might be related to some recent changes I made 
to the preprocessing pipeline, but I'm not entirely sure. Could you help me 
figure out what's causing this error, explain why it's happening, and suggest 
how to fix it? Also, it would be great if you could provide some recommendations 
for how to prevent similar issues in the future. Here's the relevant code... 
[code follows]
```

**Optimized (42 tokens):**
```
Debug:
Error: "RuntimeError: Expected tensor shape (32,100,10) got (32,100,8)"
File: model.py:145 (forward method)
Context: Recent preprocessing changes
Task: Root cause + fix + prevention
Format: JSON
```

**Savings: 80%**

## Token Estimation

```python
def estimate_tokens(text):
    """
    Estimate token count (rough approximation).
    Rule of thumb: 1 token ≈ 4 characters for English
    """
    return len(text) / 4

def calculate_savings(original, optimized):
    """
    Calculate token savings.
    """
    original_tokens = estimate_tokens(original)
    optimized_tokens = estimate_tokens(optimized)
    savings = (original_tokens - optimized_tokens) / original_tokens * 100
    
    return {
        "original": original_tokens,
        "optimized": optimized_tokens,
        "saved": original_tokens - optimized_tokens,
        "savings_percent": savings
    }
```

## Real-Time Optimization

```python
def optimize_prompt_realtime(user_input):
    """
    Automatically optimize user prompts before sending to Gemini.
    """
    # Step 1: Analyze input
    analysis = analyze_prompt(user_input)
    
    # Step 2: Apply appropriate optimizations
    optimized = user_input
    
    if analysis["has_redundancy"]:
        optimized = remove_redundancy(optimized)
    
    if analysis["has_filler"]:
        optimized = remove_filler_words(optimized)
    
    if analysis["is_verbose"]:
        optimized = compress_verbose_sections(optimized)
    
    if analysis["missing_length_spec"]:
        optimized = add_length_specification(optimized, analysis["inferred_length"])
    
    if analysis["can_use_template"]:
        optimized = convert_to_template(optimized, analysis["task_type"])
    
    # Step 3: Verify optimization didn't lose meaning
    if preserves_intent(user_input, optimized):
        savings = calculate_savings(user_input, optimized)
        
        # Log optimization
        log_optimization(user_input, optimized, savings)
        
        return {
            "optimized_prompt": optimized,
            "savings": savings,
            "quality_preserved": True
        }
    else:
        # Optimization too aggressive, use original
        return {
            "optimized_prompt": user_input,
            "savings": {"savings_percent": 0},
            "quality_preserved": True
        }
```

## Configuration

```json
{
  "token_optimizer": {
    "enabled": true,
    "aggressiveness": "medium",
    "min_savings_threshold": 20,
    "preserve_intent": true,
    "optimizations": {
      "remove_redundancy": true,
      "eliminate_filler": true,
      "use_templates": true,
      "compress_context": true,
      "specify_length": true,
      "use_abbreviations": false
    },
    "notify_user": true
  }
}
```

## User Notifications

When optimization occurs:
```
💡 Token Optimizer Active
├─ Original: ~180 tokens
├─ Optimized: ~32 tokens
├─ Savings: 82% (148 tokens)
└─ Intent preserved: ✓

Optimized prompt being used for efficiency.
```

## Performance Metrics

Track optimization effectiveness:
```python
{
  "total_optimizations": 245,
  "average_savings": "64.3%",
  "tokens_saved": 15420,
  "estimated_cost_saved": "$2.31",
  "quality_preserved": "98.4%",
  "user_satisfaction": "94%"
}
```

## Advanced Features

### Context-Aware Optimization
Adjusts optimization strategy based on task type and complexity.

### Learning System
Learns which optimizations work best for different types of requests.

### A/B Testing
Periodically compares optimized vs. original to ensure quality.

### Custom Rules
Users can define domain-specific optimization rules.

## Best Practices

1. **Always preserve intent**: Never sacrifice meaning for token savings
2. **Be transparent**: Notify users when significant optimization occurs
3. **Allow override**: Let users request "verbose" mode if needed
4. **Track metrics**: Monitor savings and quality over time
5. **Iterate**: Continuously improve optimization strategies based on feedback
