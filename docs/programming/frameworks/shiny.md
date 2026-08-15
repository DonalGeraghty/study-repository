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

## Worked Application

Keep the transformation usable without a Shiny session:

```r
library(shiny)

filter_cars <- function(cars, minimum_mpg) {
  cars[cars$mpg >= minimum_mpg, c("mpg", "cyl", "wt")]
}

ui <- fluidPage(
  numericInput(
    "minimum_mpg",
    "Minimum miles per gallon",
    value = 20,
    min = 0
  ),
  tableOutput("matching_cars")
)

server <- function(input, output, session) {
  matching_cars <- reactive({
    req(is.numeric(input$minimum_mpg))
    filter_cars(mtcars, input$minimum_mpg)
  })

  output$matching_cars <- renderTable({
    matching_cars()
  })
}

shinyApp(ui, server)
```

Only the filtered data depends on the input, so unrelated outputs would not need to recalculate. The pure function can be tested directly:

```r
stopifnot(nrow(filter_cars(mtcars, 30)) == 4)
```

For expensive work triggered by a button, use an event-oriented reactive boundary so every intermediate input change does not start the calculation.

## Testing and Deployment

Test pure R transformations independently. Add targeted tests for reactive behaviour and browser-level tests only for critical interactions. Deployment must account for package versions, process memory, concurrent sessions, logs, and external data access.

## Common Failure Modes

- reading `input` outside a reactive consumer;
- placing all calculation inside `render*` blocks where it is hard to reuse or test;
- broad reactive dependencies that rerun expensive work unnecessarily;
- modifying process-global state from one user's session;
- trusting uploaded file extensions without validating content and limits;
- rendering user-controlled HTML without an intentional sanitisation boundary.

## Project Connections

The Shiny repositories use R, Shiny, ggplot2, Leaflet, map libraries, clustering, PCA, uploaded CSV data, and shinyapps.io deployment metadata.

## Related Guides

- [R](../languages/r.md)
- [Data Analysis and Visualisation](../tooling/data-analysis-and-visualisation.md)
- [HTML](../web/html.md)

Return to [Frameworks and Libraries](./README.md).
