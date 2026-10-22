# Decisions

List 3-4 choices in total, drawn from across both tasks (A and B). For each: what you did, why, and one thing you chose NOT to do.

1. **SQL injection in the product query.**
Replaced the string-concatenated `category` in the SQL with `$wpdb->prepare()` and a `%s` placeholder.
The category comes from the shortcode attribute, which any post author (even a Contributor) can set, so it could be used to inject SQL. `prepare()` is WordPress's standard parameterised query: it escapes and quotes the value so it's always treated as data, never as SQL. I didn't add an allow-list of valid category names. `prepare()` already closes the hole, and a hard-coded list would need to be kept in sync with the database. I'd add one if categories became a fixed, known set.
2.
3.
