# Search modes and syntax

Read the installed `find` schema and `scout://guide` for version-specific options.
Examples below use `take`, `scope.pathPrefix`, `scope.globs`, and `scope.languages`.
Do not substitute unsupported arguments such as `limit` or a top-level `paths` list.

## Text and file matches

| Match | When to use it | Important limit |
| --- | --- | --- |
| `literal` | Known text or a path fragment | It finds the specified spelling, not equivalent behavior. |
| `word` | A whole identifier token or word | Word boundaries do not match `Retry` inside `RetryPolicy`. |
| `regex` | Alternatives, naming families, or text patterns | A text pattern cannot establish syntax or compiler identity. |
| `fuzzy` | Approximate filenames or text | Ranking can omit relevant results; it cannot prove absence. |
| `auto` | Accept the target's default | Files default to fuzzy; content defaults to literal. |

Smart case (`case: "auto"`) becomes case-sensitive when the query contains uppercase characters.
Set `case` explicitly when case affects the claim. Use `insensitive` for an intentional case-insensitive audit.

```json
{"target":"files","query":"RetryPolicy.cs","match":"fuzzy","take":10}
```

```json
{"target":"content","query":"WaitAndRetry","match":"literal","scope":{"languages":["csharp"]}}
```

```json
{"target":"content","query":"WaitAndRetry|Backoff|RetryPolicy|Retry-After","match":"regex","case":"insensitive","scope":{"languages":["csharp"]}}
```

```json
{"target":"content","query":"Retry","match":"word","case":"insensitive"}
```

Scope with workspace-relative prefixes, include/exclude globs, or language tokens.
For example, `scope: {"pathPrefix":"src","globs":["**/*.cs","!**/Generated/**"]}` searches that selected area.
Start in a plausible area, then widen when needed. A narrow miss says nothing about the rest of the repository.
Workspace membership exclusions can hide files before a search-specific scope applies.

## Automatic routing and declaration outlines

Automatic routing considers structural metavariables, path shapes, identifiers, and natural-language queries.
Inspect the returned route. Explicit targets make the intended search easier to reproduce.

- With `target: auto`, a strict match selects content search.
- `/pattern/` requests regex and a quoted query requests literal matching; these markers can override `match`.
- Explicit targets keep their route. Inspect `degraded` when a target does not use the requested match mode.
- Native `symbols` search filters declaration names by substring. It is not a fuzzy, regex, or exact compiler-symbol query.

```json
{"target":"symbols","query":"RetryPolicy","scope":{"languages":["csharp"]}}
```

The native outline supplies declaration kind, name, container, and source span where supported.
Supported declaration kinds depend on the grammar. A declaration outline does not enumerate usages or resolve overloads.
If Scout falls back to lexical identifiers, do not describe those hits as parsed declarations.

## Structural patterns

Use structural search for syntax that text matching handles poorly. Scope to the intended language and a useful directory.
Check the available grammars in `server_status` or the guide.

Patterns match whole syntax nodes. Include declaration modifiers and enough surrounding syntax for the parser to recognize the node.
`$NAME` captures one node; `$$$` or `$$$ARGS` captures a sequence. Capture names use uppercase letters.
Use the installed guide for patterns that differ across language grammars.

For a C# empty catch with a parenthesized exception declaration, try the enclosing statement:

```json
{"target":"structural","query":"try { $$$ } catch ($$$) { }","scope":{"languages":["csharp"]}}
```

A body with only a comment can match an empty body because the comment is not an executable statement.
Inspect the returned range. This one pattern need not cover catch filters, multiple catches, or every `finally` shape.
A standalone clause such as `catch ($E $VAR) { }` can parse differently from a clause inside a `try`.
Do not conclude that no catches exist merely because the standalone pattern returned zero results.
Empty catches are only one form of exception suppression. For all swallowed exceptions, enumerate catches lexically and inspect their behavior.
Nonempty catches can log, return a default, or suppress an error without rethrowing it.

For declarations, retain the relevant modifiers. The following pattern targets this specific method shape:

```json
{"target":"structural","query":"public async void $NAME($$$) { $$$ }","scope":{"languages":["csharp"]}}
```

It does not enumerate all `async void` declarations with different modifiers or expression bodies.
For a complete investigation, use lexical discovery or declaration outlines to find variants, then refine the structural patterns.
Similarly, a `.Result` access pattern identifies syntax only. Verify the receiver type before describing the match as a task access.

When a pattern returns no useful matches:

1. Check the root, language scope, grammar coverage, resolved route, and degradation notice.
2. Find a known positive example with literal search and read its enclosing construct.
3. Write a pattern for that complete construct, retaining modifiers. Replace variable nodes with captures.
4. Verify that the pattern finds the known example before widening the scope or making an absence claim.

If the pattern cannot compile for the scoped grammars, Scout can fall back to literal search.
A fallback hit does not validate the structural predicate. Stop using that pattern as evidence until it matches as structural search.
