# Decisions

List 3-4 choices in total, drawn from across both tasks (A and B). For each: what you did, why, and one thing you chose NOT to do.

1. **SQL injection in the product query.**
Replaced the string-concatenated `category` in the SQL with `$wpdb->prepare()` and a `%s` placeholder.
The category comes from the shortcode attribute, which any post author (even a Contributor) can set, so it could be used to inject SQL. `prepare()` is WordPress's standard parameterised query: it escapes and quotes the value so it's always treated as data, never as SQL. I didn't add an allow-list of valid category names. `prepare()` already closes the hole, and a hard-coded list would need to be kept in sync with the database. I'd add one if categories became a fixed, known set.
2. **Escaping output.**
    Wrapped product name, lender and rate in `esc_html()` and formatted the rate to 2 decimal places.
    Values from the database were printed straight into HTML, so anything stored there could run as script in visitors' browsers (XSS). I escape at the point of output so every value is covered, whatever its source.
    I would also sanitise the data when it's saved but I can't, because that code isn't in this file. Escaping on output is the protection that matters for this table.
3. **Category filter built from the data.**
   The dropdown options come from the unique categories in products.json, and a small function turns "debt-consolidation" into "Debt consolidation" for display. I wrote it in short, named steps (and used `replaceAll` instead of a regex) so it's easy to read.
   A new category shows up in the filter automatically, with no code change, and the filter can never offer a category with no products. Readable code matters more than compact code for something a teammate will maintain.
   I didn't hard-code the category list with hand-written labels. It would give marketing exact control over wording, but someone would have to remember to update it whenever a category is added.
4. **Result count and empty state.**
   Show a live count ("3 products") and, when nothing matches, a short message with a "Clear filters" button.
   Users always know how many results they're looking at, and a dead end gives them a one-click way out instead of a blank space.
   I didn't add loading or error states for the data fetch. In a real widget I would, because a failed load currently shows "No products match your search", which is misleading.