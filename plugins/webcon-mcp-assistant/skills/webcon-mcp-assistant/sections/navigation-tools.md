# Navigation Tools

## GetApplications

- Returns the list of applications visible to the current user.
- Use when the user asks "what can I do here?", "show me available apps", or when app context is needed before calling `GetApplicationMenu`.

## SearchInNavigation

- Returns navigation elements matching keywords across all available applications: applications, processes, workflow start buttons, reports, report views, and dashboards.
- Unlike `GetApplicationMenu`, it does not require the application ID.
- Use as the default tool when the user asks to find/open a report, view, dashboard, process, or start button by name.
- Similar to `GetSearchResult`, but searches navigation structure rather than workflow instance content.
- If the query returns weak or empty results, use `GetFavorites` first, then `GetFrequentlyUsedApplications`, before broad app-menu browsing.

## GetApplicationMenu

- Returns the menu structure of a given application: reports, views, start buttons, dashboards.
- Use when the user wants to find where to start a process, open a view, or understand what a given application contains.
- Call `GetApplications` first if the application ID is unknown.
- Present results as a short grouped list (application -> sections -> items with links); skip empty sections.

## GetFavorites

- Returns links to items the user has manually starred/bookmarked in the portal.
- Use when the user asks about their favourites or when orienting them to their most relevant starting points.
- Treat as a curated shortlist - if a favourite matches the user's intent, prefer it over a broad search.

## GetFrequentlyUsedApplications

- Returns automatically ranked links to the portal locations the user visits most often.
- Use when the user is unsure where to go, wants to "get back to something", or when onboarding them to the portal.
- Combine with `GetMyTasks` for a full picture of where the user spends their time.
- Also use as navigation fallback when `SearchInNavigation` and `GetFavorites` do not produce a strong match.
