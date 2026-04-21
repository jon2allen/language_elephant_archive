# Language Elephant Archive: Article Translation Comparison Project

> **A comprehensive dataset and automated evaluation framework for comparing LLM translations of the Declaration of the Rights of Man and of the Citizen across 9 languages.**

## Overview

This repository contains canonical translations of the **Declaration of the Rights of Man and of the Citizen (1789)** across 10 articles and 9 languages, along with a **ChatDSL** script for automated LLM translation quality evaluation. The project enables systematic comparison of machine translations against authoritative human translations.

**Supported Languages:** French, English, Spanish, German, Chinese, Russian, Arabic, Italian, Portuguese, Japanese

## Repository Structure

```
language_elephant_archive/
├── README.md                          # This file
├── LICENSE                           # MIT License
├── lang_compare.chatdsl              # Main ChatDSL automation script
├── article_1.md                      # Article 1 overview
├── article_2.md                      # Article 2 overview
├── ...
├── article_10.md                     # Article 10 overview
├── article_1/                        # Article 1 files
│   ├── english/
│   │   └── article01_english.txt      # Canonical English text
│   ├── french/
│   │   └── article01_french.txt       # Canonical French text
│   ├── spanish/
│   │   └── article01_spanish.txt      # Canonical Spanish text
│   │   ...
│   ├── trans/                        # Translation outputs
│   │   ├── article01_french_llama1.txt          # LLM translation
│   │   ├── article01_french_llama1_comparison.txt # Comparison report
│   │   └── ...
│   └── ...                           # Other language directories
├── article_2/                        # Article 2 structure (same pattern)
│   └── ...
└── article_10/                       # Article 10 structure (same pattern)
    └── ...
```

### Data Statistics
- **10 Articles** from the Declaration of the Rights of Man and of the Citizen
- **9-10 Languages** per article (French, English, Spanish, German, Chinese, Russian, Arabic, Italian, Portuguese, Japanese)
- **290+ text files** containing canonical and translated content

## Purpose

This project serves as:

1. **Benchmark Dataset**: Canonical translations for evaluating LLM translation quality
2. **Evaluation Framework**: Automated comparison methodology using ChatDSL
3. **Research Tool**: Systematic analysis of translation fidelity across languages
4. **Legal-Philosophical Testbed**: Specialized evaluation for legal/philosophical texts

The **lang_compare.chatdsl** script automates the process of:
- Translating English source text to all target languages using a specified LLM
- Comparing LLM translations against canonical versions
- Generating detailed evaluation reports with scoring across multiple criteria
- Producing summary comparisons across all languages

## ChatDSL Script: lang_compare.chatdsl

The main script (`lang_compare.chatdsl`) is designed for use with **[chatybot](https://github.com/jon2allen/chatybot)**, a CLI tool that provides a domain-specific language for LLM interaction and automation.

### Prerequisites

- [chatybot](https://github.com/jon2allen/chatybot) installed and configured
- API keys for your preferred LLM providers (OpenAI, Anthropic, Mistral, etc.)
- Python 3.11+

### Installation of chatybot

```bash
# Install chatybot from PyPI
pip install chatybot

# Or from source
git clone https://github.com/jon2allen/chatybot.git
cd chatybot
pip install -e .

# Configure your API keys
nano src/chatybot/chat_config.toml
```

### ChatDSL Language Basics

ChatDSL is a scripting language for automating LLM interactions. Key concepts:

- **Variables**: `set var_name = "value"` with substitution via `${var_name}`
- **File Commands**: `/file path.txt` loads files, `/save path.txt` saves output
- **File Banks**: `/filebank1-5 path.txt` loads up to 5 persistent context files
- **Model Switching**: `/model model_name` changes the active LLM
- **Multi-line Input**: `/multiline` ... `;;` for complex prompts
- **Conditionals**: `if ${condition} then /command`

### Usage

#### Basic Execution

```bash
# Navigate to the repository
cd language_elephant_archive

# Run with chatybot, using default parameters (Article 1, llama1 translator, mistral_1 judge)
chatybot /script lang_compare.chatdsl
```

#### Custom Parameters

The script accepts three parameters:

| Parameter | Variable | Default | Description |
|-----------|----------|---------|-------------|
| `x` | article_num | 1 | Article number (1-10) to translate |
| `y` | model_translator | llama1 | LLM model for translation |
| `z` | model_judge | mistral_1 | LLM model for evaluation |

```bash
# Translate Article 5 using gpt4 as translator and judge
chatybot /script lang_compare.chatdsl x=5 y=gpt4 z=gpt4

# Translate Article 3 using mistral as translator, gpt4 as judge
chatybot /script lang_compare.chatdsl x=3 y=mistral z=gpt4

# Translate Article 10 (note: uses article10, not article010)
chatybot /script lang_compare.chatdsl x=10 y=llama2 z=gpt4
```

### Script Workflow

The `lang_compare.chatdsl` script performs the following operations:

1. **Parameter Handling**: Reads `x`, `y`, `z` parameters for article number, translator model, and judge model
2. **Path Setup**: Configures paths to English source and canonical translations
3. **Sequential Translation**: For each of the 9 target languages:
   - Loads the English source text
   - Uses the translator model to generate a translation
   - Saves the LLM translation to `trans/` directory
   - Loads the canonical translation
   - Uses the judge model to compare LLM vs canonical using 4 criteria:
     - **Fidelity (0-10)**: Accuracy in conveying original meaning
     - **Language Comprehension (0-10)**: Natural, idiomatic, grammatically correct
     - **Intent Preservation (0-10)**: Legal-philosophical intent and nuances
     - **Novel/Incorrect Deviations**: Specific additions, omissions, divergences
   - Saves comparison report to `trans/` directory
4. **Summary Report**: Generates a comprehensive summary comparing all 9 languages:
   - Overall fidelity scores
   - Most significant deviations
   - General quality assessment per language
   - Common error patterns
   - Overall translator model rating
   - Strengths, weaknesses, recommendations

### Output Files

All output is saved to the `article_N/trans/` directory:

| File Pattern | Description |
|--------------|-------------|
| `articleNN_language_translator.txt` | LLM-generated translation |
| `articleNN_language_translator_comparison.txt` | Detailed comparison report |
| `articleNN_summary_translator_comparison.txt` | Multi-language summary report |

Where:
- `NN` = Article number (01-10, with special handling for 10)
- `language` = Target language name
- `translator` = Translator model name

## Evaluation Criteria

The judge model evaluates each translation using four standardized criteria:

### 1. Fidelity (0-10)
Does the LLM translation accurately convey the exact meaning of the original?
- **10**: Perfect semantic equivalence
- **7-9**: Minor deviations, meaning preserved
- **4-6**: Some meaning loss, but generally correct
- **0-3**: Significant meaning divergence

### 2. Language Comprehension (0-10)
Is the translation natural, idiomatic, and grammatically correct in the target language?
- **10**: Native-quality, idiomatic expression
- **7-9**: Fluent with occasional unnatural phrasing
- **4-6**: Understandable but clearly machine-generated
- **0-3**: Frequent grammatical errors, unnatural

### 3. Intent Preservation (0-10)
Does it preserve the legal-philosophical intent and nuances?
- **10**: Complete preservation of legal concepts and philosophical subtleties
- **7-9**: Most intent preserved, minor conceptual drift
- **4-6**: Some loss of nuance, conceptual inaccuracies
- **0-3**: Fundamental misunderstanding of legal/philosophical concepts

### 4. Novel/Incorrect Deviations
Documentation of specific differences between LLM and canonical translations.

## Project Inspiration: The "Elephant" Reference

The name "Language Elephant" references the **Elephant test** concept - a test for recognizing something that is hard to describe but unmistakable when seen. In the context of translation quality, the "elephant" is the difference between good and exceptional translation - difficult to define quantitatively, but evident in the nuances of language, intent, and cultural adaptation.

This project aims to make that "elephant" visible through systematic, multi-dimensional evaluation.

## Example Use Cases

### 1. Model Benchmarking
```bash
# Compare multiple models on the same article
chatybot /script lang_compare.chatdsl x=1 y=llama1 z=gpt4
chatybot /script lang_compare.chatdsl x=1 y=mistral z=gpt4
chatybot /script lang_compare.chatdsl x=1 y=gpt4 z=gpt4
```

### 2. Language-Specific Evaluation
```bash
# Test performance across all articles for a specific language
for i in {1..10}; do
  chatybot /script lang_compare.chatdsl x=$i y=gpt4 z=gpt4
done
```

### 3. Continuous Integration Testing
Integrate into CI/CD pipelines to track model performance over time.

## Directory Contents

### Article Directories
Each `article_N/` directory (N = 1-10) contains:
- Language subdirectories with canonical translations (e.g., `french/`, `spanish/`, etc.)
- `trans/` subdirectory for LLM-generated translations and comparison reports

### Root Files
- `lang_compare.chatdsl`: Main automation script
- `article_N.md`: Overview and context for each article

## Data Sources

Canonical translations are sourced from:
- Official government archives (France, Mexico, Germany, etc.)
- Historical repositories (UNESCO, Project Gutenberg)
- National libraries (National Diet Library of Japan, etc.)
- United Nations Regional Information Centre

## Contributing

Contributions are welcome in the following areas:

1. **Additional Languages**: Add canonical translations for new languages
2. **New Articles**: Expand beyond the first 10 articles
3. **Evaluation Improvements**: Enhance the comparison criteria and prompts
4. **Automation**: Add batch processing capabilities
5. **Analysis Tools**: Statistical analysis of results

## License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

## Acknowledgments

- [chatybot](https://github.com/jon2allen/chatybot) - The CLI tool that powers ChatDSL execution
- The various government and historical archives that preserved these translations
- The open-source LLM community for making this research possible

---

**Repository**: [language_elephant_archive](https://github.com/jon2allen/language_elephant_archive)
**Maintainer**: Jon Allen
**Last Updated**: April 2026
