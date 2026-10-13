# Package and project review

## Inspect external definitions

Use `view_external_definition` to inspect a referenced package's type or member rather than guessing its API or implementation.
Supply the discovered type or member name and relevant project context. An assembly hint orders candidates; it does not exclude other assemblies.
Verify the selected assembly and version. Follow `nextStartLine` and `nextStartColumn` for additional source pages.
A reference assembly can provide declarations without implementation. State that limit instead of treating missing bodies as actual behavior.
Use package metadata or project files for package configuration; this tool displays symbols, not an entire package manifest.

## Find package usage

Use `find_package_usages` for a NuGet package ID. It maps restored assets to compile assemblies and semantic source usages.
Provide a version when several versions occur in the selected projects. Inspect coverage and mapping limits before interpreting an empty result.
Use `find_external_dependency_usages` when the question concerns an assembly name or full metadata reference identity instead of a package ID.
Do not assume the package ID and assembly name are identical.

Glider has no general proof that a NuGet package is unused. Zero semantic usages identify a cleanup candidate, not permission to remove it.
Check for build targets, analyzers, source generators, runtime assets, reflection, configuration, and other non-source uses.
Inspect project files, central package declarations, and restored assets with appropriate file tools when necessary.
Missing or stale restore assets limit the evidence. Restore with the intended configuration when authorized, then reload and repeat the analysis.

Glider 13.1.0 can report false absence when a project uses a custom assets path.
For that version, compare the evaluated assets with assembly-level usage evidence. Treat conflicting results as incomplete evidence.

The following coverage contract is unreleased. Use it only when the installed tool returns `assetsCoverage`.
The tool uses the evaluated `ProjectAssetsFile` captured during workspace load. Reload after changes to project properties or restore state.
Inspect `assetsCoverage.complete` and each reported project's status and path before accepting an absence result.
Missing, unreadable, malformed, or unresolved assets can yield `foundPackage: null` and `packagePresence: unknown`.
Restore the affected projects with the intended configuration, then reload and retry. An ambiguous version requires a version selector.
Coverage lists at most 25 projects, with failures first. If the list is truncated, narrow the request with `projectName`.
A package without compile assemblies can still be present. Zero source usages do not establish that its other assets are unnecessary.


Use `find_package_consolidation_candidates` to inspect version drift. It finds version alignment candidates, not unused packages.
If version alignment is requested, inspect compatibility and preview `consolidate_package_version` before applying a supported rewrite.
Do not turn a dependency review into an unsolicited package upgrade.

## Audit package and asset evidence

For a dependency matrix, identify the requested packages, projects, target frameworks, configurations, and runtime identifiers first.
Reconcile every requested cell with the final report, whichever tools supply the evidence.
Report each cell as `direct`, `transitive`, `absent`, or `unknown`, with its evidence and limits.
A missing row is not evidence of absence. A package file is not evidence that a project selects or executes it.

**UNRELEASED:** Use `find_package_usages.audit` only when the installed schema advertises it.
Released Glider 13.1.1 retains the single-package semantic workflow above. Use project, restore, and build evidence to assemble other required facts.

Supply `audit.packageIds` and exact `audit.contexts` when the requested scope is known.
Each context contains `projectPath`, `targetFramework`, `configuration`, and `runtimeIdentifier`.
An empty RID selects portable evaluation. Null leaves the requested RID unspecified; inspect `evaluatedContext`.
Omitted contexts request discovery; inspect `contextScope` and `contextDiscoveryComplete` before treating that scope as complete.
An audit accepts at most 100 package IDs, 100 contexts, and 2000 package-context rows, including discovered contexts.
Pagination does not expand these limits. Split larger audits into smaller requests with explicit contexts.
Keep `audit` separate from the singular `packageId`, `packageVersion`, and `projectName` selectors.

```json
{
  "audit": {
    "packageIds": ["Example.Widget"],
    "contexts": [{
      "projectPath": "src/App/App.csproj",
      "targetFramework": "net10.0",
      "configuration": "Release",
      "runtimeIdentifier": ""
    }],
    "evaluateProjects": true,
    "includeAssetPaths": true
  }
}
```

The default `evaluateProjects=false` reads passive evidence conservatively. It cannot establish absence from unevaluated declarations.
The loader does not retain the actual evaluated configuration and RID, so passive selected workspace inputs remain unknown.
Explicit global properties do not prove correspondence: imports can override them with `TreatAsLocalProperty`.
A default configuration or output path also does not establish correspondence.

Explicit `evaluateProjects=true` queries MSBuild properties and items. It can load project imports but runs no targets or restore.
Use an exact context for separate evaluated-item evidence when passive workspace inputs remain unknown.
Compare requested contexts with `evaluatedContext`, `restoreCorrespondence`, provenance, and project/assets hashes.
`evaluatedDeclaration` records a manifest fact separately from resolved package presence. A direct declaration alone does not prove resolution.
Partial restore evidence or an unverified project-reference graph keeps presence unknown when correspondence cannot be established.
Restore for the intended context when authorized if evidence is missing or stale. Then reload and repeat the audit.

- `availableAssets` describes package assets available in restore evidence.
- `targetAssets` describes assets for the selected restore target.
- `selectedInputs` describes evaluated input roles only where evidence establishes them.
- `execution` remains unknown. Selection does not establish that a compiler, analyzer, generator, target, or runtime executed an asset.

The default `includeAssetPaths=false` returns counts and provenance with null paths. Null paths do not mean zero assets.
Set it to true when the full path lists answer a task question.
Check `requestedRows`, `coveredRows`, `skippedRows`, `presenceComplete`, `selectedInputsComplete`, and `pageComplete` separately.
Follow the returned continuation selector unchanged, including its `snapshotId`. Restart pagination if the snapshot changes.
A complete page does not establish complete presence or selected-input evidence.
Target-generated, native, or runtime evidence can remain unavailable. Report those limits without endless retries once the requested questions have supported answers.
Neither complete presence coverage nor zero C# usages establishes safe package removal.

## Inspect type relationships

Use `get_type_dependencies` with a source type key for declaration relationships and reverse references.
The `uses` direction inspects base types, interfaces, fields, properties, method returns, and parameters.
It omits method-body usages and filters most `System` types. The `used_by` direction follows symbol references.
Do not treat either result as a complete dependency inventory. Inspect relevant bodies when the task needs implementation dependencies.

## Inspect project references

Start with `get_project_graph` for direct edges, roots, leaves, transitive reachability, and cycles in the loaded workspace.
Load sufficient solution scope for the question. A project absent from this graph may simply be outside the loaded workspace.

Use `find_unused_project_references` to assess direct `ProjectReference` entries.
It removes each reference in memory and checks for new compiler errors in the referencing project. It does not modify project files.
It does not test `PackageReference` entries or prove that a whole project is unused.
Inspect the returned evidence, baseline errors, and configuration coverage before recommending removal.

A project with no incoming references can be an executable, test project, build tool, or separately published library.
Check solution membership, build scripts, CI, tests, deployment, and external consumers before proposing deletion of a project.
Runtime loading and build-order requirements can also require a reference without a direct source usage.
Treat independently removable references as separate candidates; removing several together can invalidate the earlier results.

## Apply and validate authorized cleanup

Review findings alone do not authorize dependency removal. Preserve the user's requested mutation scope.
For an authorized removal, edit the appropriate declaration, restore as needed, and reload the project graph.
Check diagnostics and run relevant builds and tests for affected configurations and target frameworks.
Validate runtime or packaging behavior when the dependency supplies those assets.
Report candidate status separately from a removal that passed validation.
