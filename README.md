# Promptfoo Evaluation Suite

This project evaluates translation quality across multiple language models using [Promptfoo](https://www.promptfoo.dev). It runs out of the box with local models via **Ollama** or **LM Studio** — no API keys required.

## Prerequisites

- [Node.js](https://nodejs.org) >= 18
- [Promptfoo](https://www.promptfoo.dev) (`npm install -g promptfoo`)
- [Ollama](https://ollama.ai) running locally with the tested model `gpt-oss:20b` pulled (for cloud providers, see [full config](#full-configuration))

## Quick Start

```bash
# 1. Install promptfoo globally
npm i -g promptfoo

# 2. Install project dependencies
npm install

# 3. Make sure Ollama is running with the tested model: ollama run gpt-oss:20b

# 4. Run the evaluation with local models
npm run eval

# 5. View results
npm run report
```

> **No API keys needed.** The default config uses local models only.

## Viewing Results

| Command | Description |
|---|---|
| `npm run report` | Open results table in your browser |
| `npm run publish` | Upload results to promptfoo web dashboard |
| `npm run compare` | Compare two evaluation runs side-by-side |

Results are saved to `outputs/results.json` and `outputs/results-latest.csv`.

## Project Structure

| File | Description |
|---|---|
| `promptfooconfig.yaml` | Default config — local model via Ollama |
| `promptfooconfig.full.yaml` | Full config — adds OpenAI, Anthropic, Google |
| `prompts/direct.txt` | Direct translation prompt |
| `prompts/detailed.txt` | Translation prompt with tone and register instructions |
| `prompts/code_mixing.txt` | Translation prompt that preserves code, URLs, emails |
| `tests/translations.yaml` | 32 standard translation tests |
| `tests/edge_cases.yaml` | 14 edge-case tests |
| `outputs/` | Evaluation results (gitignored) |
| `.env.example` | API keys template (for cloud providers) |

## Commands

| Command | Description |
|---|---|
| `npm run eval` | Run with local model (Ollama — `gpt-oss:20b`) |
| `npm run eval:full` | Run with all providers (needs API keys) |
| `npm run eval:watch` | Re-run on file changes |
| `npm run publish` | Publish results to web dashboard |
| `npm run report` | View results locally |
| `npm run compare` | Compare two evaluation runs |

## Configuration

### Default (local models)

| Provider | Model | Service Required |
|---|---|---|
| Ollama | `gpt-oss:20b` | [Ollama](https://ollama.ai) running locally |

### Full configuration

To compare against cloud providers, copy the env template and add your API keys:

```bash
cp .env.example .env
# Edit .env with your keys
npm run eval:full
```

| Provider | Model ID | Temp | API Key |
|---|---|---|---|
| OpenAI | `gpt-4.1-mini` | 0.3 | `OPENAI_API_KEY` |
| OpenAI | `gpt-4o` | 0.3 | `OPENAI_API_KEY` |
| OpenAI | `o4-mini` | 0.3 | `OPENAI_API_KEY` |
| Anthropic | `claude-sonnet-4-20250514` | 0.3 | `ANTHROPIC_API_KEY` |
| Google | `gemini-2.5-flash` | 0.3 | `GOOGLE_API_KEY` |

### Tests

- `tests/translations.yaml` — Standard translations: greetings, questions, requests, formal language, idioms
- `tests/edge_cases.yaml` — Edge cases: idioms, special characters, code snippets, numbers/currency, script preservation, negative assertions

## Customizing

### Adding a Provider

Add to `providers` in `promptfooconfig.yaml` (or `promptfooconfig.full.yaml`):

```yaml
- id: openai:gpt-3.5-turbo
  label: GPT-3.5 Turbo
  config:
    temperature: 0.3
```

For cloud providers, add the corresponding API key to `.env`.

### Adding a Test

#### Translation test (append to `tests/translations.yaml`)

```yaml
- vars:
    language: Portuguese
    input: Good evening
  assert:
    - type: contains
      value: boa noite
```

#### Edge-case test (append to `tests/edge_cases.yaml`)

```yaml
- vars:
    language: French
    input: The path is C:\Users\foo\bar.txt
  assert:
    - type: regex
      value: "C:\\\\Users\\\\foo\\\\bar\\.txt"
```

### Adding a Prompt

1. Create `prompts/new_strategy.txt`
2. Add to `prompts` in your config file:

```yaml
prompts:
  - file://prompts/new_strategy.txt
    label: New Strategy
```

## Notes

- Use `similar` or `regex` assertions for non-English scripts
- Use `temperature` of 0.1–0.3 for evaluation consistency
- Use full model IDs (e.g., `claude-sonnet-4-20250514`) rather than aliases
- Commit test files, not results in `outputs/`
- Review results before merging provider or prompt changes

## License

MIT
