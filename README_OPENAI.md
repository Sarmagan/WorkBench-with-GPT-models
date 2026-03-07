# WorkBench — OpenAI-only Fork

> **Forked from [olly-styles/WorkBench](https://github.com/olly-styles/WorkBench)**  
> Original paper: *WorkBench: A Benchmark Dataset for Agents in a Realistic Workplace Setting*

This fork removes all LangChain dependencies and uses the **OpenAI Chat Completions API** (with tool calling) directly.  
The default model is **`gpt-5-nano`**, but any model in `AVAILABLE_LLMS` can be used.

---

## Quick Start

### 1. Install `uv` (if not already installed)
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 2. Create the environment and install dependencies
```bash
cd WorkBench
uv sync
```

### 3. Add your OpenAI API key
```bash
echo "sk-..." > openai_key.txt
```

### 4. Run the benchmark

**Single domain (e.g. calendar):**
```bash
uv run python scripts/inference/generate_results.py \
  --model_name gpt-5-nano \
  --queries_path data/processed/queries_and_answers/calendar_queries_and_answers.csv
```

**All domains at once:**
```bash
for domain in calendar email analytics project_management customer_relationship_manager multi_domain; do
  uv run python scripts/inference/generate_results.py \
    --model_name gpt-5-nano \
    --queries_path data/processed/queries_and_answers/${domain}_queries_and_answers.csv
done
```

**Use only domain-relevant tools per query (faster & cheaper):**
```bash
uv run python scripts/inference/generate_results.py \
  --model_name gpt-5-nano \
  --queries_path data/processed/queries_and_answers/calendar_queries_and_answers.csv \
  --tool_selection domains
```

---

## Available Models

Set `--model_name` to any of:

| Name | OpenAI model string |
|------|-------------------|
| `gpt-5-nano` | gpt-5-nano |

To add a new model, just add its name to `AVAILABLE_LLMS` in `src/evals/utils.py` — no other changes needed.

---

## Results

Results are saved to `data/results/<domain>/` as timestamped CSV files.  
Metrics are printed to stdout at the end of each run.

---

## Changes from upstream

| File | What changed |
|------|-------------|
| `src/tools/*.py` | Removed `@tool` LangChain decorator; added `.openai_schema`, `.name`, `.func` attributes to each function |
| `src/tools/toolkits.py` | Removed LangChain imports; toolkits are now plain Python lists |
| `src/evals/utils.py` | Replaced `initialize_agent` + LangChain LLMs with `openai.OpenAI` + a hand-rolled tool-calling loop |
| `scripts/inference/generate_results.py` | Removed LangChain warning suppression |
| `pyproject.toml` | New file — minimal deps for `uv` |



Accuracy: 86.36% (95 out of 110)