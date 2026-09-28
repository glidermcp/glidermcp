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

Use `find_package_consolidation_candidates` to inspect version drift. It finds version alignment candidates, not unused packages.
If version alignment is requested, inspect compatibility and preview `consolidate_package_version` before applying a supported rewrite.
Do not turn a dependency review into an unsolicited package upgrade.

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
