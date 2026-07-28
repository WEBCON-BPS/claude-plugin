# Search Navigation

## Dependency boundary

- This section handles navigation lookup first.
- If navigation lookup is ambiguous or empty and you need workflow instance content search, load and apply `sections/05-search-content.md` for the fallback flow.

## Navigation-first shortcut

- Before running full content search, check whether the user is actually looking for a place in the portal (report, report view, dashboard, or start button).
- If the request can be answered by navigation item name alone (for example: "open sales report", "where is the complaints dashboard"), prefer navigation tools first:
  1. Use `SearchInNavigation` with strong intent keywords.
  2. Match candidate item names and return the best direct link.
  3. If no strong hit is found, call `GetFavorites` and check whether bookmarked items match the intent.
  4. If still no strong hit is found, call `GetFrequentlyUsedApplications` and try to recover likely destination from there.
  5. If needed, refine by element type (`Report`, `ReportView`, `Dashboard`, `StartNewInstance`) or `applicationId`.
- Use `GetApplications` + `GetApplicationMenu` only as a fallback when `SearchInNavigation` is too broad, empty, or needs app-specific browsing.
- In such cases, skip `GetSearchResult` unless name-based navigation matching is ambiguous or returns nothing.
- If multiple similarly named items exist, show a short disambiguation list (name, application, type).
