# Promptfoo Evaluation Suite

This project evaluates translation quality across multiple language models using [Promptfoo](https://www.promptfoo.dev).

## Setup

```bash
npm i -g promptfoo
cp .env.example .env
# Edit .env with your API keys
npm run eval
```

## Structure

| File | Description |
|---|---|
| `promptfooconfig.yaml` | Providers, prompts, tests, output paths |
| `prompts/direct.txt` | Direct translation prompt |
| `prompts/detailed.txt` | Translation prompt with tone and register instructions |
| `prompts/code_mixing.txt` | Translation prompt that preserves code, URLs, emails |
| `tests/translations.yaml` | 32 standard translation tests |
| `tests/edge_cases.yaml` | 14 edge-case tests |
| `outputs/` | Evaluation results (gitignored) |
| `.env.example` | Required API keys |

## Configuration

### Providers

| Provider | Model ID | Temp |
|---|---|---|
| OpenAI | `openai:gpt-4.1-mini` | 0.3 |
| OpenAI | `openai:gpt-4o` | 0.3 |
| OpenAI | `openai:o4-mini` | 0.3 |
| Anthropic | `anthropic:claude-sonnet-4-20250514` | 0.3 |
| Google | `google:gemini-2.5-flash` | 0.3 |

### Tests

- `tests/translations.yaml` — Standard translations: greetings, questions, requests, formal language, idioms
- `tests/edge_cases.yaml` — Edge cases: idioms, special characters, code snippets, numbers/currency, script preservation, negative assertions

## Commands

| Command | Description |
|---|---|
| `npm run eval` | Run full evaluation (all providers × all prompts) |
| `npm run eval:watch` | Re-run on file changes |
| `npm run publish` | Publish results to web dashboard |
| `npm run report` | View results locally |
| `npm run compare` | Compare two evaluation runs |

## Adding Tests

### Translation test (append to `tests/translations.yaml`)

```yaml
- vars:
    language: Portuguese
    input: Good evening
  assert:
    - type: contains
      value: boa noite
```

### Edge-case test (append to `tests/edge_cases.yaml`)

```yaml
- vars:
    language: French
    input: The path is C:\Users\foo\bar.txt
  assert:
    - type: regex
      value: "C:\\\\Users\\\\foo\\\\bar\\.txt"
```

## Adding a Provider

Add to `providers` in `promptfooconfig.yaml`:

```yaml
- id: openai:gpt-3.5-turbo
  label: GPT-3.5 Turbo
  config:
    temperature: 0.3
```

Export the corresponding API key, then run `npm run eval`.

### Local Providers

**Ollama** — requires [Ollama](https://ollama.ai) running locally:

```yaml
- id: ollama:llama3
  label: Llama 3 (local)
  config:
    temperature: 0.3
```

**LM Studio** — requires [LM Studio](https://lmstudio.ai) with local server running (default `http://localhost:1234`):

```yaml
- id: lmstudio:lm-studio-community/Meta-Llama-3-8B-Instruct-GGUF
  label: Llama 3 (LM Studio)
  config:
    temperature: 0.3
```

## Adding a Prompt

1. Create `prompts/new_strategy.txt`
2. Add to `prompts` in `promptfooconfig.yaml`:

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
- Check evaluation results before merging provider or prompt changes

## License

MIT
