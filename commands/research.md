# Research Command

Delegate research-intensive tasks to Gemini Pro for comprehensive analysis.

## Usage
```
/gemini:research <topic> [--depth=<shallow|medium|deep>] [--output=<filename>]
```

## Description
This command delegates research tasks to Gemini CLI (Pro model) for:
- Literature surveys
- Paper analysis
- Technology comparisons
- Trend analysis
- Large-scale data gathering

## Parameters
- `topic`: Research topic or query (required)
- `--depth`: Research depth level (default: medium)
  - `shallow`: Quick overview (1-2 pages)
  - `medium`: Comprehensive analysis (3-5 pages)
  - `deep`: In-depth investigation (5+ pages)
- `--output`: Output filename (default: research_TIMESTAMP.md)

## Workflow
1. **Claude analyzes** the research request
2. **Generates optimized prompt** for Gemini with:
   - Clear research objectives
   - Expected output structure
   - Citation requirements
   - Token budget guidelines
3. **Executes Gemini CLI**:
   ```bash
   gemini -m pro -p "OPTIMIZED_PROMPT" -y -o json > OUTPUT_FILE
   ```
4. **Validates results**:
   - Checks completeness
   - Verifies citations
   - Assesses quality
5. **Formats final output** in markdown

## Example Prompts (Auto-generated)

### For Shallow Depth
```
Research Request: {topic}
Scope: Quick overview with key findings
Format: Markdown with bullet points
Length: 1-2 pages
Include: Main concepts, 3-5 key references, practical implications
Exclude: Deep technical details
```

### For Deep Depth
```
Research Request: {topic}
Scope: Comprehensive analysis
Format: Structured markdown report
Sections Required:
1. Executive Summary
2. Background & Context
3. Current State of the Art
4. Detailed Analysis
5. Comparative Evaluation
6. Future Directions
7. References (APA format)
Length: 5-10 pages
Include: Technical details, methodology, empirical results, code examples
Citation Style: Academic (prefer recent papers from top venues)
```

## Implementation

```bash
#!/bin/bash
# This is conceptual - actual implementation will be handled by Claude Code

TOPIC="$1"
DEPTH="${2:-medium}"
OUTPUT="${3:-research_$(date +%s).md}"

# Claude generates the optimized prompt
PROMPT=$(claude_generate_research_prompt "$TOPIC" "$DEPTH")

# Execute Gemini CLI
gemini -m pro -p "$PROMPT" -y -o text > "$OUTPUT"

# Validate and format
claude_validate_research "$OUTPUT"
```

## Success Criteria
- ✅ Comprehensive coverage of topic
- ✅ Proper citations included
- ✅ Structured markdown format
- ✅ Actionable insights provided
- ✅ No hallucinations or unsupported claims

## Error Handling
- If Gemini fails: Retry with simplified prompt
- If output incomplete: Re-run with continuation prompt
- If quality issues: Claude reviews and requests revision
