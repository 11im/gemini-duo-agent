# Auto-Delegate Skill

Automatically detects tasks suitable for Gemini CLI delegation and triggers appropriate workflow.

## Skill Metadata
```json
{
  "name": "auto-delegate",
  "version": "1.0.0",
  "type": "analysis",
  "auto_trigger": true,
  "priority": "high"
}
```

## Activation Conditions

This skill automatically activates when:
1. **Token-intensive tasks** detected:
   - "write a comprehensive..."
   - "analyze all files in..."
   - "generate complete documentation..."
   - "research recent papers on..."

2. **Research keywords** present:
   - "survey", "literature review", "state of the art"
   - "compare approaches", "benchmark"
   - "recent developments", "trends"

3. **Large-scale code generation**:
   - Multiple files mentioned
   - Complex system architecture
   - Boilerplate generation
   - Test suite creation

4. **Debugging complex issues**:
   - Stack traces provided
   - Multiple error sources
   - Performance profiling needed

## Detection Patterns

```python
DELEGATION_TRIGGERS = {
    "research": [
        r"research (recent|latest|current) (papers|work|developments?|trends?)",
        r"literature (review|survey|analysis)",
        r"state of the art in",
        r"comprehensive (overview|analysis|survey)",
        r"compare .+ approaches?",
    ],
    
    "code_generation": [
        r"implement (complete|full|entire) (system|module|class)",
        r"generate (all|complete) (files?|modules?|components?)",
        r"create (comprehensive|complete) test suite",
        r"write .+ and .+ and .+",  # Multiple items
    ],
    
    "documentation": [
        r"document (all|entire|complete) (codebase|project|system)",
        r"generate (API|technical) documentation",
        r"write (comprehensive|detailed) (guide|tutorial|manual)",
    ],
    
    "debugging": [
        r"debug .+ (error|issue|problem|bug)",
        r"analyze (why|how) .+ (fails?|errors?)",
        r"find (root cause|source) of",
        r"trace execution",
    ],
    
    "analysis": [
        r"analyze (all|entire|complete)",
        r"summarize .+ files?",
        r"compare .+ (implementations?|approaches?|methods?)",
        r"evaluate .+ performance",
    ]
}

TOKEN_THRESHOLDS = {
    "research": 1000,      # Estimated tokens
    "code": 500,
    "documentation": 800,
    "debugging": 600,
    "analysis": 1000,
}
```

## Decision Logic

```python
def should_delegate(user_message, context):
    """
    Determine if task should be delegated to Gemini CLI.
    
    Returns:
        (bool, str, dict): (should_delegate, task_type, metadata)
    """
    # Extract task characteristics
    estimated_tokens = estimate_token_usage(user_message, context)
    task_type = classify_task(user_message)
    complexity = assess_complexity(user_message, context)
    
    # Decision factors
    factors = {
        "high_token_usage": estimated_tokens > TOKEN_THRESHOLDS.get(task_type, 500),
        "requires_research": matches_any(user_message, DELEGATION_TRIGGERS["research"]),
        "large_scale_generation": matches_any(user_message, DELEGATION_TRIGGERS["code_generation"]),
        "comprehensive_analysis": matches_any(user_message, DELEGATION_TRIGGERS["analysis"]),
        "complex_debugging": complexity["debugging"] > 0.7,
    }
    
    # Delegate if any strong factor or multiple weak factors
    strong_factors = ["high_token_usage", "requires_research", "large_scale_generation"]
    delegate = (
        any(factors[f] for f in strong_factors) or
        sum(factors.values()) >= 2
    )
    
    metadata = {
        "estimated_tokens": estimated_tokens,
        "task_type": task_type,
        "complexity_score": complexity.get(task_type, 0),
        "factors": factors,
    }
    
    return delegate, task_type, metadata
```

## Workflow

```
User Message
     ↓
[Auto-Delegate Skill Activated]
     ↓
Analyze → Should Delegate?
     ↓          ↓
    No         Yes
     ↓          ↓
Claude    Classify Task Type
Handles        ↓
           Research | Code | Debug | Report
                ↓
         Select Appropriate Command
                ↓
         /gemini:research
         /gemini:code
         /gemini:debug
         /gemini:report
                ↓
         Execute with Gemini Pro
                ↓
         Claude Validates Result
                ↓
         Present to User
```

## Example Activations

### Example 1: Research Task
```
User: "Can you research recent papers on test-time adaptation for time series forecasting and summarize the key approaches?"

Skill Detection:
- Matches: "research recent papers"
- Task type: research
- Estimated tokens: ~1500
- Decision: DELEGATE

Action:
→ Automatically trigger /gemini:research with optimized prompt
```

### Example 2: Large Code Generation
```
User: "Implement a complete time series forecasting framework with data preprocessing, model training, test-time adaptation, and evaluation metrics."

Skill Detection:
- Matches: "implement complete"
- Multiple components: 4+ modules
- Estimated tokens: ~2000
- Decision: DELEGATE

Action:
→ Automatically trigger /gemini:code with architectural spec
```

### Example 3: Complex Debugging
```
User: "This error happens intermittently when running the forecasting model. Here's the stack trace: [long trace]. Can you figure out what's wrong?"

Skill Detection:
- Matches: "figure out what's wrong"
- Stack trace provided
- Complexity: high
- Decision: DELEGATE

Action:
→ Automatically trigger /gemini:debug with full context
```

### Example 4: No Delegation
```
User: "What's the difference between supervised and unsupervised learning?"

Skill Detection:
- Simple factual question
- Estimated tokens: ~100
- No delegation triggers
- Decision: NO DELEGATION

Action:
→ Claude handles directly
```

## User Notifications

When delegation occurs, notify user:

```
🔄 Delegating to Gemini Pro for comprehensive analysis...
├─ Task: Research
├─ Reason: High token requirement (~1500 tokens)
└─ Model: Gemini Pro (optimal for extensive research)

⏳ Processing... (this may take 30-60 seconds)
```

When complete:
```
✅ Analysis complete! Gemini Pro analyzed 15 papers and generated a 5-page summary.
📊 Claude is now reviewing for quality and formatting...
```

## Configuration

Users can adjust delegation behavior:

```json
// .claude/settings.json
{
  "plugins": {
    "gemini-duo-agent": {
      "auto_delegate": {
        "enabled": true,
        "token_threshold": 500,
        "always_ask": false,  // Set true to confirm before delegating
        "prefer_gemini_for": ["research", "documentation"],
        "prefer_claude_for": ["quick_fixes", "simple_questions"]
      }
    }
  }
}
```

## Success Metrics

Track delegation effectiveness:
- Delegation accuracy (% correct decisions)
- User satisfaction (accept/reject rate)
- Token savings
- Time efficiency

## Fallback Strategy

If delegation fails:
1. Claude attempts the task directly
2. If still too complex, break into subtasks
3. Request user guidance if needed

## Integration with Other Skills

Works with:
- `token-optimizer`: Ensures efficient prompting
- `result-validator`: Validates delegated outputs
- `quality-supervisor`: Maintains quality standards
