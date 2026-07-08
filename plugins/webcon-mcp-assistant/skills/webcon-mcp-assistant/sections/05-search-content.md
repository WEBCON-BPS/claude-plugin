# Search Content

## Keywords vs filters

- `keywords` = business content to find.
- `filters` = WEBCON structure to narrow the search.
- Use `keywords` for names, phrases, instance numbers, invoice numbers, tax IDs, emails, or other identifiers.
- Use `filters` for `Application`, `Process`, `Workflow`, `Step`, `Form`, and `IsFinished`.
- Do not treat filter suggestions from `GetAllSearchFilters` as proof that matching items exist.

## Search flow

1. Extract the strongest identifiers from the request.
2. Call `GetAllSearchFilters`.
3. Choose search inputs:
   - If location is unknown: strong `keywords`, minimal filters, `orderType=Rank`
   - If business area is known: strong `keywords` plus one or two structural filters
   - If the user wants the latest items: keep the same search but use `orderType=TS_Update`
4. Call `GetSearchResult`.
5. If snippets are weak or empty, verify top hits with `GetElement`.
6. If evidence may be in files, use `GetAttachmentDetails`.
7. Retry at most once, changing only one thing: `keywords`, `filters`, or ordering.
8. If still weak, report the search as inconclusive and return the search link.

## Search rules

- Start broad enough not to miss valid hits.
- Prefer exact phrases or identifiers over generic single words.
- Prefer `Application`, `Process`, `Workflow`, or `Form` filters over highly specific `Step` filters.
- Add filters only when they clearly reduce noise.
- Mark each search hit as `Conclusive`, `Probable`, or `Inconclusive`.
- Remove one restrictive filter if the result set looks too narrow.
