# Report Command

Generate comprehensive reports using Gemini Pro's extensive context window.

## Usage
```
/gemini:report <topic> [--type=<report_type>] [--sources=<files>] [--format=<output_format>]
```

## Description
This command generates:
- Research summaries
- Code documentation
- Project status reports
- Technical specifications
- Literature reviews
- Experiment results

## Parameters
- `topic`: Report topic or title (required)
- `--type`: Report type (default: general)
  - `research`: Academic research report
  - `technical`: Technical specification
  - `status`: Project status update
  - `documentation`: Code documentation
  - `experiment`: Experimental results
  - `literature`: Literature review
- `--sources`: Input files/directories to analyze (comma-separated)
- `--format`: Output format (default: markdown)
  - `markdown`: Markdown (.md)
  - `pdf`: PDF document
  - `html`: HTML page
  - `latex`: LaTeX source

## Workflow
1. **Claude structures** the report:
   - Defines sections
   - Outlines content flow
   - Specifies formatting
   - Sets quality criteria
2. **Collects all sources**:
   - Reads specified files
   - Extracts relevant data
   - Organizes by section
3. **Generates Gemini prompt**:
   - Includes all context
   - Specifies format requirements
   - Provides examples
4. **Gemini drafts** the report:
   ```bash
   gemini -m pro -p "REPORT_PROMPT" -o text > draft_report.md
   ```
5. **Claude refines**:
   - Checks completeness
   - Improves clarity
   - Ensures consistency
   - Adds visual elements (if needed)
6. **Formats final output**

## Report Templates

### Research Report
```markdown
# {Title}

## Executive Summary
[2-3 paragraphs: Key findings, implications, recommendations]

## 1. Introduction
### 1.1 Background
### 1.2 Objectives
### 1.3 Scope

## 2. Methodology
### 2.1 Approach
### 2.2 Data Sources
### 2.3 Analysis Methods

## 3. Findings
### 3.1 Main Results
### 3.2 Supporting Evidence
### 3.3 Statistical Analysis

## 4. Discussion
### 4.1 Interpretation
### 4.2 Comparison with Prior Work
### 4.3 Limitations

## 5. Conclusions
### 5.1 Summary
### 5.2 Implications
### 5.3 Future Work

## References
[Formatted citations]

## Appendices
### A. Detailed Data
### B. Code Samples
```

### Technical Specification
```markdown
# {System/Feature Name} - Technical Specification

## Document Information
- Version: X.Y.Z
- Date: YYYY-MM-DD
- Author: {Name}
- Status: [Draft/Review/Approved]

## 1. Overview
### 1.1 Purpose
### 1.2 Scope
### 1.3 Definitions & Acronyms

## 2. System Architecture
### 2.1 High-Level Design
### 2.2 Component Diagram
### 2.3 Data Flow

## 3. Detailed Design
### 3.1 Module Specifications
### 3.2 Interfaces
### 3.3 Data Structures
### 3.4 Algorithms

## 4. Implementation Details
### 4.1 Technology Stack
### 4.2 Dependencies
### 4.3 Configuration
### 4.4 Deployment

## 5. Testing Strategy
### 5.1 Unit Tests
### 5.2 Integration Tests
### 5.3 Performance Tests

## 6. Security Considerations
## 7. Performance Requirements
## 8. Maintenance & Support
## 9. References
```

### Experiment Results
```markdown
# Experiment Report: {Title}

## Metadata
- Experiment ID: EXP-{ID}
- Date: {Date}
- Researcher: {Name}

## 1. Hypothesis
{Clear statement of hypothesis}

## 2. Experimental Setup
### 2.1 Dataset
- Name: {Dataset}
- Size: {Samples}
- Features: {Description}
- Split: Train/Val/Test

### 2.2 Model Configuration
```python
{Configuration code}
```

### 2.3 Hyperparameters
| Parameter | Value | Rationale |
|-----------|-------|-----------|
| ...       | ...   | ...       |

## 3. Results
### 3.1 Quantitative Results
| Metric | Baseline | Ours | Improvement |
|--------|----------|------|-------------|
| MSE    | 0.045    | 0.032| +28.9%     |
| MAE    | 0.123    | 0.098| +20.3%     |

### 3.2 Qualitative Analysis
{Visualization and interpretation}

### 3.3 Statistical Significance
- p-value: {value}
- Confidence interval: [{lower}, {upper}]

## 4. Discussion
### 4.1 Key Findings
### 4.2 Comparison with Baselines
### 4.3 Error Analysis
### 4.4 Limitations

## 5. Conclusion
### 5.1 Summary
### 5.2 Next Steps

## 6. Reproducibility
### 6.1 Code Repository
### 6.2 Data Access
### 6.3 Environment Setup
```

## Example Generation

### Input
```bash
/gemini:report "Time Series Forecasting with Test-Time Adaptation" \
  --type=research \
  --sources=experiments/,results.csv,model.py \
  --format=markdown
```

### Claude's Prompt to Gemini
```
Generate a comprehensive research report on "Time Series Forecasting with Test-Time Adaptation"

CONTEXT:
I'm providing you with:
1. Experimental results from multiple runs
2. Model implementation code
3. Dataset statistics
4. Baseline comparisons

SOURCES:
{All file contents with clear labels}

REQUIREMENTS:
1. Follow the research report template structure
2. Include quantitative results with statistical significance
3. Provide clear visualizations (describe in markdown)
4. Compare with related work
5. Discuss limitations and future directions
6. Cite all sources in APA format

QUALITY STANDARDS:
- Academic writing style
- Clear and concise language
- Evidence-based conclusions
- Reproducible methodology
- Comprehensive but focused (8-12 pages)

OUTPUT FORMAT:
Complete markdown document with:
- Proper heading hierarchy
- Tables for results
- Code blocks for implementation details
- Inline citations [Author, Year]
- Reference section at the end

Begin the report now:
```

## Implementation

```python
# Conceptual implementation

def generate_report(topic, report_type, sources, output_format):
    # Step 1: Claude creates structure
    structure = claude_design_report_structure(topic, report_type)
    
    # Step 2: Collect and organize sources
    content_map = {}
    for source in sources:
        content_map[source] = read_and_analyze(source)
    
    # Step 3: Generate comprehensive prompt
    prompt = claude_create_report_prompt(
        topic=topic,
        structure=structure,
        content=content_map,
        template=get_template(report_type)
    )
    
    # Step 4: Gemini generates draft
    run_command(f'gemini -m pro -p "{prompt}" -o text > draft.md')
    
    # Step 5: Claude refines
    draft = read_file('draft.md')
    refined = claude_refine_report(draft, structure)
    
    # Step 6: Convert format if needed
    if output_format != 'markdown':
        refined = convert_format(refined, output_format)
    
    return refined
```

## Quality Checks

Claude performs these before finalizing:

1. **Completeness**: All sections present
2. **Consistency**: Terminology and style uniform
3. **Accuracy**: Facts and citations verified
4. **Clarity**: Language clear and precise
5. **Formatting**: Proper markdown/structure
6. **Citations**: All sources properly cited

## Success Criteria
- ✅ Comprehensive coverage of topic
- ✅ Well-structured and organized
- ✅ Professionally formatted
- ✅ Evidence-based conclusions
- ✅ Properly cited sources
- ✅ Ready for publication/presentation

## Additional Features

### Auto-visualization
Claude can automatically:
- Generate matplotlib code for charts
- Create mermaid diagrams
- Build comparison tables

### Multi-format Export
Support for:
- PDF via pandoc
- HTML with CSS styling
- LaTeX for academic submission

### Template Customization
Users can provide custom templates:
```bash
/gemini:report "My Topic" --template=custom_template.md
```
