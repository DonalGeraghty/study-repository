# R

R is a language and environment for statistical computing, data analysis, and visualisation. Effective R work treats analysis as software: source data is preserved, transformations are explicit, results are reproducible, and important assumptions are tested.

## Vectors and Data Structures

Vector operations are central to R. Atomic vectors contain values of one basic type; coercion can occur when incompatible values are combined.

```r
scores <- c(72, 88, 91, NA)
mean(scores, na.rm = TRUE)
```

Common structures include:

| Structure | Use |
| --- | --- |
| Atomic vector | Values of one basic type |
| List | Values of different types |
| Matrix | Two-dimensional values of one type |
| Data frame | Tabular columns that may have different types |
| Factor | Categorical values with defined levels |

Missing values use `NA`. Test them with `is.na()` rather than comparing with `==`.

## Selection and Vectorisation

R is one-indexed. Square brackets select subsets, while data-frame operations should make row and column intent clear.

```r
passed <- results[results$score >= 70 & !is.na(results$score), ]
```

Vectorised operations are often clearer and faster than manually iterating over individual values. The `apply` family and functional tools can express repeated operations, but a readable loop is preferable to an obscure abstraction.

## Data Transformation

A reliable analytical pipeline separates stages:

```text
raw source -> validated import -> cleaned data -> analysis -> presentation
```

Preserve raw inputs, document column meanings and units, validate joins and row counts, and make the handling of missing values and outliers explicit. Never allow a visualisation step to silently become the only record of a transformation.

Base R and packages such as `dplyr` offer different interfaces for filtering, projection, grouping, joining, and aggregation. Pick conventions for a project and avoid mixing styles without a reason.

## Functions and Environments

Functions are values and use lexical scoping. Prefer functions that receive required data explicitly and return results instead of modifying global state.

```r
pass_rate <- function(scores, threshold = 70) {
  valid <- scores[!is.na(scores)]
  mean(valid >= threshold)
}
```

Validate assumptions near the boundary with conditions such as `stopifnot()` or clear errors from `stop()`.

## Statistical Reasoning

Code cannot compensate for a weak analytical design. Before selecting a model or test, identify:

- the question and target population;
- sampling and measurement limitations;
- variable types and dependence between observations;
- assumptions of the statistical method;
- uncertainty, effect size, and practical significance;
- possible confounding, leakage, and multiple comparisons.

Separate exploratory analysis from confirmatory analysis, and record decisions made after inspecting the data.

## Visualisation

A useful chart has a clear question, meaningful labels, appropriate scales, and an honest visual encoding. Prefer position and length over area or decorative effects when precise comparison matters. Check colour contrast and avoid relying on colour alone.

```r
plot(results$duration, results$score,
     xlab = "Duration (minutes)",
     ylab = "Score",
     main = "Score by duration")
```

## Reproducible Projects

- Use project-relative paths rather than machine-specific absolute paths.
- Record package dependencies and the R version.
- Set a random seed when reproducibility requires deterministic pseudo-random results.
- Keep generated outputs separate from source data and code.
- Render reports from code so tables and charts match the analysis.
- Avoid storing credentials or sensitive source data in Git.

## Testing and Validation

Test reusable functions with representative, boundary, missing, and invalid inputs. Add data-quality assertions for schema, uniqueness, allowed ranges, and join cardinality. For numerical results, compare with an appropriate tolerance rather than assuming exact floating-point equality.

## Readiness Checklist

You should be able to:

- explain vectors, recycling, coercion, missing values, and factors;
- select and transform tabular data without losing row-level meaning;
- write functions without hidden global dependencies;
- validate joins, aggregations, statistical assumptions, and outputs;
- create accessible visualisations that answer a defined question;
- reproduce an analysis in a clean environment;
- distinguish statistical significance from practical importance.

## Official References

- [An Introduction to R](https://cran.r-project.org/doc/manuals/r-release/R-intro.html)
- [R language definition](https://cran.r-project.org/doc/manuals/r-release/R-lang.html)
- [Writing R Extensions](https://cran.r-project.org/doc/manuals/r-release/R-exts.html)

Return to [Programming Languages](./README.md).
