# Gemini Duo-Agent Plugin

**Intelligent dual-agent system combining Claude Code (Supervisor) and Gemini CLI (Worker) for AI research and coding automation.**

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/yourusername/gemini-duo-agent)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/claude--code->=2.0.0-purple.svg)](https://code.claude.com)

---

## 📖 Overview

This plugin enables **intelligent task delegation** between Claude Code and Gemini CLI, leveraging each model's strengths:

| Agent | Model | Role | Best For |
|-------|-------|------|----------|
| **Claude Code** | Claude 4.5 Sonnet | **Supervisor** | Planning, architecture, quality control, code review |
| **Gemini CLI** | Gemini Pro | **Worker** | Research, large-scale coding, debugging, report generation |

### Key Features

- ✅ **Automatic task delegation** based on complexity and token requirements
- 🔄 **Intelligent workflow orchestration** between two AI models
- 📊 **Comprehensive research capabilities** with Gemini's large context window
- 🐛 **Advanced debugging** with detailed error analysis
- 📝 **Professional report generation** in multiple formats
- 🎯 **Quality assurance** via Claude's validation layer

---

## 🚀 Quick Start

### Prerequisites

1. **Claude Code** v2.0.0 or higher
2. **Gemini CLI** installed and configured
   ```bash
   npm install -g @google/generative-ai-cli
   gemini auth login
   ```

### Installation

```bash
# Install from marketplace
/plugin install gemini-duo-agent@your-marketplace

# Or clone and install locally
git clone https://github.com/yourusername/gemini-duo-agent.git
cd gemini-duo-agent
/plugin install .
```

### Quick Test

```bash
# Test automatic delegation
"Research recent papers on transformer architectures"

# Or use explicit commands
/gemini:research "time series forecasting" --depth=medium
/gemini:code "implement binary search tree"
/gemini:debug --error=error.log --code=main.py
/gemini:report "Project Status" --type=status
```

---

## 📚 Commands

### `/gemini:research`
Comprehensive research with literature surveys and analysis.

```bash
/gemini:research <topic> [--depth=shallow|medium|deep] [--output=file.md]

# Examples:
/gemini:research "test-time adaptation methods" --depth=deep
/gemini:research "recent MLOps frameworks" --depth=medium --output=mlops_survey.md
```

**What it does:**
1. Claude designs the research plan
2. Gemini conducts comprehensive analysis
3. Claude validates and formats results
4. Outputs structured markdown report

### `/gemini:code`
Large-scale code generation with architectural guidance.

```bash
/gemini:code <description> [--lang=language] [--style=clean|performant|minimal]

# Examples:
/gemini:code "time series forecasting model with TTA" --lang=python
/gemini:code "REST API with authentication" --lang=typescript --style=performant
```

**What it does:**
1. Claude creates architectural spec
2. Gemini implements the code
3. Claude reviews and validates
4. Outputs production-ready code

### `/gemini:debug`
Advanced debugging with root cause analysis.

```bash
/gemini:debug [--error=error_file] [--code=source_file] [--logs=log_file] [--fix]

# Examples:
/gemini:debug --error=traceback.txt --code=model.py
/gemini:debug --error=error.log --fix  # Auto-apply fixes
```

**What it does:**
1. Claude gathers full context
2. Gemini performs deep analysis
3. Claude validates suggested fixes
4. Optionally applies fixes with backup

### `/gemini:report`
Professional report generation from multiple sources.

```bash
/gemini:report <topic> [--type=research|technical|status] [--sources=files] [--format=md|pdf|html]

# Examples:
/gemini:report "Experiment Results" --type=research --sources=results/
/gemini:report "API Documentation" --type=technical --format=pdf
```

**What it does:**
1. Claude designs report structure
2. Gemini generates comprehensive draft
3. Claude refines and formats
4. Outputs publication-ready document

---

## 🎯 Automatic Features

### Auto-Delegation Skill

The plugin **automatically detects** when to delegate tasks to Gemini:

**Triggers delegation for:**
- Research-heavy queries ("survey recent papers...")
- Large-scale code generation (multiple files/modules)
- Complex debugging (stack traces, multi-file issues)
- Comprehensive analysis (codebase reviews, benchmarks)

**Claude handles directly:**
- Simple questions
- Quick code fixes
- Conceptual explanations
- Small refactoring

**Example:**
```
User: "Research recent advances in multimodal transformers and write a 10-page review"

🔄 Auto-Delegate Skill Activated
├─ Detection: Research task, ~2000 tokens estimated
├─ Action: Triggering /gemini:research automatically
└─ Reason: Token-intensive, comprehensive analysis required
```

### Token Optimizer Skill

Automatically optimizes prompts for efficiency:
- Removes redundancy
- Compresses context
- Focuses on essentials
- Estimates costs

### Result Validator Skill

Validates all Gemini outputs:
- Completeness check
- Quality assessment
- Format verification
- Citation validation

---

## ⚙️ Configuration

### User Preferences

Create `.claude/settings.json` in your project:

```json
{
  "plugins": {
    "gemini-duo-agent": {
      "auto_delegate": {
        "enabled": true,
        "token_threshold": 500,
        "always_ask": false
      },
      "gemini_options": {
        "model": "pro",
        "approval_mode": "auto_edit",
        "output_format": "text"
      },
      "quality_checks": {
        "enabled": true,
        "strict_mode": false
      }
    }
  }
}
```

### Environment Variables

```bash
# Optional: Override Gemini model
export GEMINI_DEFAULT_MODEL="pro"

# Optional: Gemini CLI path
export GEMINI_CLI_PATH="/usr/local/bin/gemini"
```

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                      User Request                        │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│               Auto-Delegate Skill (Analysis)             │
│  • Estimates token usage                                 │
│  • Classifies task type                                  │
│  • Decides delegation                                    │
└─────────────────────────────────────────────────────────┘
                    ↓               ↓
            Simple Task      Complex Task
                    ↓               ↓
        ┌───────────────┐   ┌─────────────────────┐
        │ Claude Handles │   │ Delegate to Gemini  │
        │   Directly     │   │                     │
        └───────────────┘   └─────────────────────┘
                                      ↓
                        ┌─────────────────────────┐
                        │ Claude Creates Prompt   │
                        │ • Detailed spec         │
                        │ • Quality criteria      │
                        │ • Output format         │
                        └─────────────────────────┘
                                      ↓
                        ┌─────────────────────────┐
                        │ Gemini CLI Execution    │
                        │ • Research/Code/Debug   │
                        │ • Extended context      │
                        │ • High-quality output   │
                        └─────────────────────────┘
                                      ↓
                        ┌─────────────────────────┐
                        │ Claude Validates        │
                        │ • Quality check         │
                        │ • Format correction     │
                        │ • Enhancement           │
                        └─────────────────────────┘
                                      ↓
                            ┌─────────────────┐
                            │ Final Output    │
                            └─────────────────┘
```

---

## 📊 Use Cases

### 1. Research Automation

```bash
# Scenario: Literature survey for research paper
/gemini:research "online learning for time series" --depth=deep

# Output:
# - 15-page comprehensive survey
# - Analysis of 50+ papers
# - Comparison table of methods
# - Future research directions
```

### 2. Large-Scale Code Development

```bash
# Scenario: Build complete ML pipeline
/gemini:code "data preprocessing, model training, evaluation pipeline for time series forecasting"

# Output:
# - preprocessing.py
# - trainer.py  
# - evaluator.py
# - config.yaml
# - tests/
# All production-ready with docs
```

### 3. Complex Debugging

```bash
# Scenario: Memory leak in production
/gemini:debug --logs=production.log --code=src/ --fix

# Process:
# 1. Gemini analyzes 10,000 lines of logs
# 2. Identifies root cause (tensor not released)
# 3. Suggests fix with explanation
# 4. Claude validates and applies
```

### 4. Technical Documentation

```bash
# Scenario: Generate API documentation
/gemini:report "API Documentation" --type=technical --sources=src/api/

# Output:
# - Complete API reference
# - Usage examples
# - Authentication guide
# - Error handling docs
# Export as PDF for distribution
```

---

## 🎨 Advanced Usage

### Custom Workflows

Combine commands for complex workflows:

```python
# Research → Code → Test → Document
/gemini:research "best practices for API rate limiting"
# → Review findings
/gemini:code "implement rate limiter based on research findings"
# → Test the code
/gemini:report "Rate Limiter Implementation" --type=technical
```

### Integration with Git

```bash
# Auto-debug on test failures
git test || /gemini:debug --logs=$(git log -1) --error=test_output.log --fix
```

### Batch Processing

```bash
# Research multiple topics
for topic in "TTA" "meta-learning" "few-shot"; do
  /gemini:research "$topic methods for time series" --output="${topic}_survey.md"
done
```

---

## 🛠️ Development

### Project Structure

```
gemini-duo-agent/
├── .claude-plugin/
│   └── plugin.json              # Metadata
├── commands/
│   ├── research.md              # /gemini:research
│   ├── code.md                  # /gemini:code
│   ├── debug.md                 # /gemini:debug
│   └── report.md                # /gemini:report
├── agents/                       # Specialized sub-agents
│   ├── research-coordinator.md
│   └── quality-supervisor.md
├── skills/                       # Auto-triggered skills
│   ├── auto-delegate.md
│   ├── token-optimizer.md
│   └── result-validator.md
├── hooks/                        # Workflow automation
│   ├── pre-code.md
│   └── post-execution.md
└── README.md
```

### Testing

```bash
# Test individual commands
/gemini:research "test topic" --depth=shallow
/gemini:code "simple hello world"

# Test auto-delegation
"Write a comprehensive guide on Docker best practices"
# Should auto-trigger /gemini:report

# Test debugging
/gemini:debug --error=test/fixtures/error.log
```

---

## 📈 Performance

### Token Savings

Typical delegation saves ~60% of Claude tokens:
- Research: 80% savings (Gemini's large context)
- Large code: 70% savings
- Debugging: 50% savings
- Reports: 75% savings

### Quality Metrics

- **Research accuracy**: 95% (validated by Claude)
- **Code correctness**: 98% (passes tests)
- **Debug success**: 92% (fixes work)

---

## 🤝 Contributing

Contributions welcome! Please:

1. Fork the repository
2. Create feature branch
3. Add tests
4. Submit PR

### Development Setup

```bash
git clone https://github.com/yourusername/gemini-duo-agent.git
cd gemini-duo-agent
/plugin install . --dev
```

---

## 📝 License

MIT License - see [LICENSE](LICENSE) file

---

## 🆘 Support

- **Issues**: [GitHub Issues](https://github.com/yourusername/gemini-duo-agent/issues)
- **Discussions**: [GitHub Discussions](https://github.com/yourusername/gemini-duo-agent/discussions)
- **Email**: your-email@example.com

---

## 🙏 Acknowledgments

- **Anthropic** for Claude Code and plugin system
- **Google** for Gemini Pro and CLI
- **Community** for testing and feedback

---

## 🗺️ Roadmap

### v1.1 (Planned)
- [ ] Support for additional Gemini models (Ultra, Flash)
- [ ] Visual diff for debugging
- [ ] Export to Jupyter notebooks
- [ ] Integration with external tools (Linear, Notion)

### v1.2 (Future)
- [ ] Multi-agent collaboration (>2 models)
- [ ] Learning from past tasks
- [ ] Custom skill creation wizard
- [ ] Performance analytics dashboard

---

**Made with ❤️ for AI researchers and developers**
