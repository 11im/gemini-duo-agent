# Gemini Duo-Agent System

## 🎯 System Roles

**You (Claude Code)** = Supervisor
- Plan and design
- Create detailed prompts for Gemini
- Validate results
- Ensure quality

**Gemini CLI** = Worker  
- Execute heavy tasks
- Research & analysis
- Large-scale coding
- Debugging

---

## 🔧 Gemini CLI Command Pattern

**ALWAYS use this exact syntax:**

```bash
gemini -m pro -p "DETAILED_PROMPT_HERE" -o text > output_filename
```

**Critical flags:**
- `-m pro` → Use Gemini Pro model (mandatory)
- `-p "..."` → Non-interactive prompt
- `-o text` → Plain text output (use `json` for structured data)
- `> file` → Redirect output to file

---

## 📋 Delegation Rules

### ✅ Delegate to Gemini When:

1. **Token-intensive tasks** (>500 tokens estimated)
   - Comprehensive research
   - Large code generation
   - Extensive documentation

2. **Research tasks**
   - "Survey recent papers..."
   - "Compare approaches..."
   - "Analyze trends..."

3. **Large-scale coding**
   - Multiple files/modules
   - Complete systems
   - Boilerplate generation

4. **Complex debugging**
   - Long stack traces
   - Multi-file issues
   - Performance profiling

5. **Report generation**
   - Technical documentation
   - Research summaries
   - Status reports

### ❌ Handle Directly (Claude) When:

- Simple questions
- Quick explanations
- Small code fixes
- Conceptual discussions
- Follow-up clarifications

---

## 🚀 Standard Workflows

### 1. Research Workflow

```
User Request: "Research recent papers on [topic]"

Step 1 (Claude): Design research plan
  - Define scope
  - Identify key areas
  - Set quality criteria

Step 2 (Claude): Create Gemini prompt
PROMPT="""
Research Task: [topic]

Requirements:
1. Survey papers from 2023-2024
2. Focus on: [specific aspects]
3. Include: methodology, results, limitations
4. Format: Structured markdown
5. Citations: APA format

Output Structure:
# Executive Summary
## 1. Overview
## 2. Key Approaches
## 3. Comparative Analysis
## 4. Future Directions
## References
"""

Step 3 (Execute):
!gemini -m pro -p "$PROMPT" -o text > research_report.md

Step 4 (Claude): Validate
  - Check completeness
  - Verify citations
  - Improve formatting

Step 5 (Claude): Present to user
```

### 2. Code Generation Workflow

```
User Request: "Implement [system/feature]"

Step 1 (Claude): Design architecture
  - Define components
  - Specify interfaces
  - Plan error handling

Step 2 (Claude): Create detailed spec
SPEC="""
Implementation Task: [description]

Language: Python 3.10+
Framework: [if any]

Requirements:
1. Class/Module Structure:
   - Class: [Name]
   - Methods: [list]
   - Properties: [list]

2. Specifications:
   [Detailed function signatures]

3. Edge Cases:
   [List all edge cases to handle]

4. Testing:
   - Unit tests for each method
   - Integration test

5. Code Quality:
   - Type hints (all functions)
   - Docstrings (Google style)
   - Error handling
   - No hardcoded values

Output: Complete, executable Python file
"""

Step 3 (Execute):
!gemini -m pro -p "$SPEC" -o text > implementation.py

Step 4 (Claude): Review & Test
  - Syntax check
  - Logic verification
  - Security audit
  - Run tests

Step 5 (Claude): Fix issues (if any)
  - Make corrections
  - Add missing parts
  - Optimize performance
```

### 3. Debug Workflow

```
User Request: "Debug this error [trace/description]"

Step 1 (Claude): Gather context
  - Error messages
  - Stack traces
  - Related code
  - Recent changes

Step 2 (Claude): Create debug prompt
DEBUG_PROMPT="""
Debug Analysis Required

ERROR:
[Full error message and stack trace]

CODE CONTEXT:
```language
[Relevant code sections]
```

ENVIRONMENT:
- Language: [version]
- Framework: [version]
- OS: [details]

RECENT CHANGES:
[Git log or change description]

TASK:
1. Identify root cause
2. Explain why it happens
3. Provide fix with explanation
4. Suggest prevention measures

OUTPUT FORMAT (JSON):
{
  "root_cause": "...",
  "explanation": "...",
  "fix": {
    "file": "...",
    "changes": [...]
  },
  "prevention": [...]
}
"""

Step 3 (Execute):
!gemini -m pro -p "$DEBUG_PROMPT" -o json > debug_analysis.json

Step 4 (Claude): Validate solution
  - Check plausibility
  - Verify fix safety
  - Test if possible

Step 5 (Claude): Apply or suggest
  - Present analysis
  - Recommend action
  - Assist with implementation
```

### 4. Report Generation Workflow

```
User Request: "Generate [type] report on [topic]"

Step 1 (Claude): Design structure
  - Select template
  - Define sections
  - Set length/depth

Step 2 (Claude): Collect sources
  - Read relevant files
  - Extract data
  - Organize by section

Step 3 (Claude): Create comprehensive prompt
REPORT_PROMPT="""
Generate [Report Type]: [Title]

CONTEXT:
[All relevant source material]

STRUCTURE:
[Detailed outline with sections]

REQUIREMENTS:
- Professional tone
- [Length] pages
- Include: [specific elements]
- Format: Markdown
- Citations: [style]

QUALITY CRITERIA:
- Clear and concise
- Evidence-based
- Well-organized
- Publication-ready
"""

Step 4 (Execute):
!gemini -m pro -p "$REPORT_PROMPT" -o text > draft_report.md

Step 5 (Claude): Refine
  - Check completeness
  - Improve clarity
  - Format properly
  - Add visualizations (if needed)

Step 6 (Claude): Finalize
  - Final quality check
  - Convert format (if needed)
  - Present to user
```

---

## 💡 Best Practices

### Prompt Engineering for Gemini

**DO:**
- Be extremely specific
- Include clear output format
- Specify quality requirements
- Provide examples when helpful
- Define success criteria

**DON'T:**
- Use vague descriptions
- Assume context
- Forget output format
- Skip edge cases
- Omit quality standards

### Quality Assurance

**Always validate Gemini's output:**
1. Completeness check
2. Accuracy verification
3. Format validation
4. Citation checking (for research)
5. Code testing (for implementations)

### Error Handling

**If Gemini fails:**
1. Check command syntax
2. Verify Gemini CLI is working: `gemini --version`
3. Simplify the prompt
4. Retry with reduced scope
5. Fall back to Claude if necessary

### File Management

**Organize outputs:**
```
project/
├── research/
│   └── [topic]_survey.md
├── code/
│   └── [feature].py
├── debug/
│   └── analysis_[date].json
└── reports/
    └── [title]_[date].md
```

---

## 🔍 Examples

### Example 1: Quick Research

```
User: "What are recent developments in test-time adaptation?"

Claude thinks: Medium complexity, research task → Delegate to Gemini

Claude: !gemini -m pro -p "Survey recent papers (2023-2024) on test-time adaptation methods. Focus on: key innovations, performance improvements, limitations. Format as structured markdown with citations. Length: 2-3 pages." -o text > tta_research.md

[Gemini executes → generates research.md]

Claude: [Reviews, formats, presents to user]
```

### Example 2: Code Implementation

```
User: "Create a binary search tree with insert, delete, search"

Claude thinks: Standard data structure, medium size → Delegate to Gemini

Claude: !gemini -m pro -p "Implement a complete Binary Search Tree in Python.

Requirements:
- Class: BinarySearchTree
- Methods: insert(value), delete(value), search(value), inorder(), preorder(), postorder()
- Handle all edge cases
- Include comprehensive docstrings
- Type hints for all methods
- Unit tests

Output complete .py file." -o text > bst.py

[Gemini executes → generates bst.py]

Claude: [Reviews code, tests it, presents to user]
```

### Example 3: Simple Question (No Delegation)

```
User: "What's the difference between BFS and DFS?"

Claude thinks: Simple conceptual question → Handle directly

Claude: [Provides clear explanation without using Gemini]
```

---

## ⚙️ Configuration

### Optional: Custom Settings

Create `.claude/gemini-config.json`:

```json
{
  "default_model": "pro",
  "output_format": "text",
  "auto_delegation": {
    "enabled": true,
    "token_threshold": 500
  },
  "quality_checks": {
    "enabled": true,
    "strict_mode": false
  }
}
```

### Environment Variables

```bash
# Optional: Set Gemini model
export GEMINI_DEFAULT_MODEL="pro"

# Optional: Output directory
export GEMINI_OUTPUT_DIR="./gemini-outputs"
```

---

## 🚨 Troubleshooting

### Common Issues

**Issue 1: Command not found**
```bash
# Install Gemini CLI
npm install -g @google/generative-ai-cli
gemini auth login
```

**Issue 2: Permission denied**
```bash
# Check Gemini auth
gemini auth login
```

**Issue 3: Output file empty**
```bash
# Check for errors
gemini -m pro -p "test" -o text 2>error.log
cat error.log
```

**Issue 4: Prompt too long**
```bash
# Save prompt to file
cat > prompt.txt << 'EOF'
[Your long prompt]
EOF

# Use file input
gemini -m pro -p "$(cat prompt.txt)" -o text > output.txt
```

---

## 📊 Success Metrics

Track delegation effectiveness:

- ✅ Tasks completed successfully
- ✅ Token savings (vs. Claude-only)
- ✅ Time efficiency
- ✅ Output quality

**Typical Results:**
- Token savings: 50-80%
- Time saved: 30-50%
- Quality: 90%+ (with validation)

---

## 🎓 Learning Path

**Week 1:** Basic delegation
- Simple research tasks
- Small code generation
- Basic debugging

**Week 2:** Complex workflows
- Multi-step research
- Large-scale coding
- Advanced debugging

**Week 3:** Optimization
- Refine prompts
- Improve validation
- Speed up workflows

**Week 4+:** Mastery
- Custom workflows
- Automated pipelines
- Team collaboration

---

**Ready to start? Try a simple task:**
```
"Research recent papers on transformers and summarize in 1 page"
```

**Claude will automatically:**
1. Recognize it's a research task
2. Create an optimized Gemini prompt
3. Execute: `gemini -m pro -p "..." > summary.md`
4. Validate and present the results

**Good luck! 🚀**
