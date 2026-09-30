# Semantic search: discovery and recovery

Use semantic search when the user describes behavior but the implementation names are unknown.
Results are leads. Read source and follow concrete identifiers before drawing conclusions.

## Check that semantic retrieval actually ran

Use an explicit target when evaluating semantic quality:

```json
{"target":"semantic","query":"HTTP request retries with exponential backoff","scope":{"languages":["csharp"]},"take":10}
```

Inspect the route, semantic state, and degradation message before assessing the results.
An explicit semantic request can fall back to fuzzy lexical search while the semantic tier is unavailable.
An automatic natural-language query can route to literal content search when semantic retrieval is unavailable.
Those results do not measure embedding quality.

The index can be disabled, building, or warming after idle eviction. Follow the reported state and recovery guidance.
Use inline progress or status to distinguish ongoing work from an unavailable tier. Avoid repeated queries while a long build remains incomplete.
A ready index can still return weak candidates. Readiness establishes availability, not retrieval quality.

Scout combines embedding and lexical rankings over code chunks when both are available.
Its fusion score is a rank score, not a similarity percentage, confidence, or probability that the result is relevant.
Neither a low score nor a missing result proves absence. Increasing `take` can expose more candidates without making the search exhaustive.

## Improve the query before changing the model

- State the behavior, domain, and distinguishing mechanism: `HTTP retry delay with exponential backoff` is more specific than `retry`.
- Try another formulation when the first result list is weak, such as `network failure retry policy before another request`.
- Narrow to a relevant subsystem or language when UI labels, tests, or generated content dominate results.
- Widen the scope if it excludes a plausible implementation area. Verify ignore and semantic inclusion settings when known files are missing.
- Read several candidates and extract actual identifiers, APIs, and configuration terms for strict searches.

For the retry example, a lexical cross-check might use:

```json
{"target":"content","query":"WaitAndRetry|Backoff|RetryPolicy|Retry-After","match":"regex","case":"insensitive","scope":{"languages":["csharp"]}}
```

Add terms discovered in this repository. This list is a starting point, not a complete catalogue of retry implementations.
Inspect the delay calculation, trigger, and call sites to distinguish network backoff from a UI retry button.
Use resolved call relationships when the installed tools provide them. Text matches alone do not establish those relationships.

If generated or vendor code crowds out results, inspect existing semantic include/exclude settings.
Explain proposed configuration changes and their indexing cost to the user when configuration work is requested.
Semantic filters cannot restore files excluded from workspace membership. Changing embedding filters can require rebuilding the semantic index.
Do not change filters to hide relevant code solely to improve the appearance of a result list.

## When model advice belongs

Changing the chat LLM does not change Scout's embedding model. A different chat model may formulate different queries; retrieval still uses Scout's configuration.
Do not recommend a larger chat model as a direct fix for poor Scout rankings.

Scout 4.2.0 automatically provisions `snowflake-arctic-embed-xs`. It does not offer a verified menu of larger replacement models.
Custom model-path settings exist, but files must match the backend, tokenizer, dimensions, and query encoding expected by that version.
Do not treat a configurable model name as evidence that any embedding model is compatible.
Check the installed version's documentation before proposing a custom model or claiming compatibility.

If quality remains poor across representative queries, treat model selection as a separate evaluation task:

1. Collect several real questions with known relevant locations, including cases current retrieval misses.
2. Compare the same query set, scope, index state, and result count with strict-search controls.
3. Assess relevant results, important misses, latency, memory use, and index build cost.
4. Recommend a supported change only when the measured benefit justifies its cost and compatibility requirements.

One query with one relevant result in five is useful feedback, not a general benchmark or a model recommendation.
Avoid promising fixed speed ratios between lexical, structural, and semantic modes from one repository's measurements.
