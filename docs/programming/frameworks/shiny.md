# Shiny for R

Shiny is an R framework for interactive web applications. A Shiny application defines a user interface and server logic connected through reactive values and outputs.

## Reactive Model

```text
input changes -> invalidated reactive expression -> dependent output recalculates
```

Use reactive expressions for reusable derived data, observers for side effects, and rendered outputs for presentation. Read reactive values only inside a reactive context.

## Application Design

- Validate uploaded files, column names, types, ranges, and missing values.
- Keep expensive calculations behind the narrowest reactive dependency graph.
- Separate data preparation and statistical logic from UI wiring.
- Avoid shared mutable global state because multiple sessions may use one R process.
- Provide useful empty, loading, validation, and failure states.
- Treat generated HTML and user-supplied labels as untrusted content.

For data visualisation, construct a stable data frame first and let plotting code express the selected view. Clustering, PCA, and maps also require domain checks; a reactive UI does not make an invalid analysis correct.

## Testing and Deployment

Test pure R transformations independently. Add targeted tests for reactive behaviour and browser-level tests only for critical interactions. Deployment must account for package versions, process memory, concurrent sessions, logs, and external data access.

## Project Connections

The Shiny repositories use R, Shiny, ggplot2, Leaflet, map libraries, clustering, PCA, uploaded CSV data, and shinyapps.io deployment metadata.

## Related Guides

- [R](../languages/r.md)
- [Data Analysis and Visualisation](../tooling/data-analysis-and-visualisation.md)
- [HTML](../web/html.md)

Return to [Frameworks and Libraries](./README.md).
