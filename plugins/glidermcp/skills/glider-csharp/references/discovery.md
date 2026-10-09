# Discovery and source

## Contract gate

The consolidated contract is unreleased. Use this section only when the installed schema advertises `get_source` and `find_code.matchMode`.
Otherwise, use the released-contract fallback below. Installed tool schemas remain authoritative.

## Select a symbol or file

`find_code` defaults to `intent=symbol` and `matchMode=closest`.
It selects the first nonempty exact, prefix, or substring tier after scope, namespace, accessibility, and kind filters.
Inspect ambiguous candidates and choose the intended key. A name match does not identify an overload by itself.
`matchMode=all` retains broad matches. `*` and `?` match whole symbol names; regex belongs in `search_text`.
Sort and page controls apply after filters and tier selection. Follow `paging.nextSkip`, including pages limited by the response budget.
Check `coverage.complete` and `skippedProjects` before interpreting zero matches as absence.

Use `scope.mode=project` with `scope.projectName` to select a project.
External search requires `scope.includeExternal=true` with solution or project scope. A source-only miss does not enable external search automatically.

`intent=fileOutline` accepts an exact path, a loaded C# filename, or a symbol name.
Select a returned full path when filenames collide. A linked file can require a project context.
This route rejects symbol filters, sorting, `matchMode=all`, and external scope.
Use `get_structure` directly for a known file and its outline controls.

Pass the selected `symbolKey` unchanged to relationship tools. Avoid another name search when the key already identifies the target.
Use `get_symbol_info` for signatures and declaration details. Use `view_external_definition` for external source.
Use `search_text` for literal text, or set `useRegex=true` for a .NET regular expression.

## Read source

`get_source` requires an exact opaque `symbolKey`; it does not accept a type or method name.
Optional `filePath` selects declaration parts before pagination. Defaults are `skip=0`, `take=10`, and `contextLines=0`.
Partial methods and properties include both definition and implementation parts. Follow `paging.nextSkip` for later declarations.
Compare `declarationCount` with `selectedDeclarationCount` to understand file selection.

Each part contains exact `source`, `sourceComplete`, and declaration, selected, and content spans.
`declarationSpan` identifies the declaration. `selectedSpan` identifies the requested declaration or body.
`contentSpan` describes returned text after context expansion and truncation.
Offsets and lengths use zero-based UTF-16 units. Lines and columns are one-based, with exclusive ends.

The default content bounds are `maxLines=200` and `maxChars=20000` per part.
Zero removes the respective bound; response limits still apply. A line, character, or response-budget cut sets `sourceComplete=false`.
Inspect `truncated` and `truncationReason`. Declaration pagination and source completeness are separate conditions.
`contextLines` accepts 0 through 20. `bodyOnly=true` requires zero context and supports methods, constructors, and accessors.
A method without a body returns its declaration. Other symbol kinds reject `bodyOnly=true`.

For a guarded full-declaration edit, require `sourceComplete=true`, zero context, `bodyOnly=false`, and equal declaration/content spans.
For `replace_member`, also compare the result with `get_symbol_info.replacementSpans`.
A shared-field key returns one `VariableDeclarator`; a single-variable field returns its full field declaration.
Use the declared replacement span when the source span does not match the intended edit.
Canonical root checks precede pagination. Use the containing solution for source outside the loaded root.

## Migration

| Earlier call | Consolidated call |
| --- | --- |
| `resolve_symbol` | `find_code` with `matchMode=closest` |
| `search_symbols` | `find_code` with `matchMode=all` |
| `get_method_source` or `get_type_source` | `get_source` with the selected key |
| `get_method_signature` | `get_symbol_info` with the selected overload key |
| `get_derived_types` | `get_type_hierarchy` with base types and interfaces disabled |
| `find_code` relationship intent | Discovery, then the direct relationship tool |
| `find_code` with `literalText` | `search_text` |

Rediscover constructor keys after updating to the consolidated contract. Pass the newly returned key unchanged.

Move the former `projectName` into project scope. Replace `sourceOnly=false` with external scope.
Replace `maxCandidates` with `take`; the discovery default is 50.
For derived-only hierarchy, set `includeBaseTypes=false`, `includeInterfaces=false`, and `includeDerivedTypes=true`.
The consolidated `find_code` removes `auto`, `literalText`, references, implementations, callers, and hierarchy intents.
`get_type_info` and cascade analysis remain separate tools.

## Released-contract fallback

Use this section when the installed schema lacks the consolidated contract.
Earlier `find_code` schemas support `symbol`, `fileOutline`, `literalText`, and relationship routes.
Their `auto` and `symbol` routes perform broad symbol searches; they do not infer filenames or regex from the query.
Use only the intents advertised by the installed schema.
Use `search_symbols` for namespace, accessibility, and sort controls. Use `resolve_symbol` for closest-name discovery when available.
Use `get_method_source` or `get_type_source` with a selected key for source. Prefer exact keys over name-based overload selection.
Use `get_symbol_info` for declaration details and direct relationship tools for known keys.
`search_text` remains the literal and regex text tool. External definitions use `view_external_definition`.
