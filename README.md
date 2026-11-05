# Research Paper Scout

Find recent papers and technical reports, inspect available evidence, and produce
an explained reading shortlist. Focus: image/video generation, editing, training,
data, conditioning and evaluation. Runs locally; no Hermes installation required.

## What it does

- Discovers papers through Hugging Face Daily Papers, alphaXiv and Emergent Mind.
- Reads official indexes/feeds from Krea, BFL, Runway, OpenAI, DeepMind, Google
  Research, Adobe Research, Meta AI, ByteDance Seed, Qwen, NVIDIA and Hugging Face.
- Merges arXiv duplicates, preserves source provenance and applies a seven-day window.
- Reads available article/paper excerpts, including methods, experiments and limitations.
- Asks an OpenAI-compatible model to assess every candidate, selecting up to nine.
- Explains the contribution, why to read it and what the evidence cannot establish.
- Writes a Markdown digest and a JSON report with source errors and rejection reasons.

There is no minimum digest size, popularity-based quality score or video quota.
A technical report or careful analysis can be more useful than a routine new model.
Generated analysis is in Russian; edit `RESEARCH_PROFILE` or use `--profile` to adapt it.
Changing the profile does not change discovery sources or their topic queries.

## Run

Python 3.10+ on Linux/macOS. Collection uses the Python standard library.

```sh
python3 papers_daily.py --collect-only --report-dir ./output
```

To analyze the collection, install the model client and configure your provider
in your shell. Never commit credentials.

```sh
python3 -m venv .venv
. .venv/bin/activate
pip install -r requirements.txt
# Set OPENAI_API_KEY and OPENAI_MODEL for your own provider.
# Optionally set OPENAI_BASE_URL for an OpenAI-compatible endpoint.
python papers_daily.py --rank-report ./output/report.json --report-dir ./analysis
```

Or collect and analyze in one invocation:

```sh
python papers_daily.py --report-dir ./output
python papers_daily.py --profile ./profile.example.md --report-dir ./custom-output
```

Model calls cost money according to your provider. `--collect-only` makes no model
calls. Optional `--max-cost-usd 0.03` refuses inference if its conservative estimate
exceeds the cap. This guard requires the provider's `/models` response to publish
`pricing.prompt` and `pricing.completion` in USD/token; it fails if that metadata
is unavailable. It is not a provider account spending limit. No automatic retries.

Each output directory includes `report.json`, collected source data under `raw/`,
and (after analysis) `digest.md`. Keep these local: fetched content may be copyrighted.
The script never sends messages or publishes results. Runs are independent; there
is no delivered/read history or cross-run suppression. Repeated runs may repeat papers.
Concurrent runs targeting the same output directory are blocked by a file lock.

## Limits and failure handling

- Discovery is bounded: up to six linked pages per publisher, ten nominations per
  source family and thirty model candidates. It is not comprehensive literature search.
- A JSON report records how many eligible papers were omitted by the candidate cap.
- Excerpts are partial, up to 6,000 characters per candidate. A summary is not a
  full-paper review or independent verification of author claims.
- When publisher pages are blocked but a dated RSS entry is available, evidence is
  marked `abstract_only` and source coverage is marked degraded.
- Qwen currently uses its legacy public blog, not its separate JavaScript research site.
- Source/API layouts may change. Failures are distinct from an empty result;
  if every source fails, analysis stops.
- Invalid model JSON, unknown IDs, inconsistent select/reject decisions and truncated
  model responses fail explicitly. Source text is treated as untrusted data.
- Flex/Batch support depends on your provider. This project does not assume that
  an accepted parameter or a model catalog entry proves discounted inference.
- `--include-chinese-blogs` optionally adds Chinese research-media write-ups as a
  separate block. It does not fetch WeChat or require social-network accounts.

## Next improvements

- Update publisher discovery where legacy feeds miss current research.
- Measure which useful papers are lost before model selection, separately from
  shortlist precision and summary quality.
- Deduplicate a technical report's publisher page and its arXiv version.
- Make the shortlist length and discovery budgets configurable.

## License

MIT for this project's code. Third-party papers, feeds, articles and model services
retain their own licenses and terms. No provider credentials, server configuration,
personal runtime logs or generated research data are included.
