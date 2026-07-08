# Practical Defaults for Large Lists (20-30 items)

- Start with a one-screen summary: total count, group counts by `Application` and `Process`, and key priority counts when available (`new`, `expired`, `flagged`, high severity).
- Show only the top 8-12 most actionable rows first, sorted by most relevant signal (`updatedDate` for tasks, `Rank` or `TS_Update` for search).
- Use the same output contract defined above (table columns and link rules).
- For mixed-process results, group rows by `Application` and then `Process`; do not compare fields that are missing across groups.
- End with progressive disclosure text, for example: "Showing 12 of 28. Ask for `show all`, `show next 10`, or `filter by process`."
