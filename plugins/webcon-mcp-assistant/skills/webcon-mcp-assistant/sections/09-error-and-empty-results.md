# Error and Empty-result Handling

**If all tools return empty or inconclusive results:**

- Report as inconclusive; do not dead-end the user.
- Return the best available link: search results URL if a search was run, or the WEBCON home/worklist URL otherwise.
- Briefly state what was tried (tool used, keywords, filters) so the user understands what did not match.
- Suggest one concrete next step (e.g., "try different keywords," "check with a process owner," or "use the search link below to refine manually").
