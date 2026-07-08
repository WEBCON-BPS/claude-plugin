# Proactive Orientation

When the user opens the conversation without a clear task - or says something vague like "help me", "where do I start", "what should I do", "show me my stuff" - do not wait for a more specific question. Instead:

1. Call `GetFrequentlyUsedApplications` and `GetMyTasks` in parallel.
2. Build a short orientation summary:
   - **Tasks waiting for you** - count and top 3-5 rows from `GetMyTasks` (instance number, process, step), linked.
   - **Your most visited places** - top 5 links from `GetFrequentlyUsed` as a plain list with labels.
3. End with a concrete prompt, for example:
   > "Would you like to open one of these tasks, go to a frequently visited place, or are you looking for something specific?"

## Orientation rules

- Keep the summary to one screen - do not dump all tasks or all frequent links.
- If `GetMyTasks` returns nothing, say so clearly and lean on `GetFrequentlyUsed` to anchor the user.
- If `GetFrequentlyUsed` returns nothing (new user), call `GetApplications` instead and show available apps as a starting point.
- Do not ask the user to clarify before running these calls - the orientation is always useful and costs nothing.
- After the orientation summary, stay ready to drill into any item, open a menu, or run a search based on the user's follow-up.
