---
tags:
  - programming/languages
---

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

## Worked Prediction: Recycling and Missing Values

Predict each result before using the R console:

```r
c(10, 20, 30, 40) + c(1, 2)
mean(c(10, NA, 30))
mean(c(10, NA, 30), na.rm = TRUE)
mean(numeric(0))
```

**Check your reasoning:** The results are `11 22 31 42`, `NA`, `20`, and `NaN`. The shorter vector repeats to match the longer vector; exact-multiple recycling can happen without a warning. A non-multiple length normally warns but still computes, so warnings deserve attention.

Removing missing observations changes the denominator. In the `pass_rate` example, all-missing input leaves no observations and produces `NaN`, not a zero pass rate. Decide whether the contract should return `NA_real_` or reject an empty valid sample, then test that choice. For vectors meant to pair row-for-row, assert equal lengths before arithmetic rather than relying on recycling.

## Interview Questions

> [!question] Interview Questions
> - What's vector recycling, and why can incompatible assumptions about vector lengths silently produce incorrect values?
> - How does R's handling of missing values (`NA`) change the result of a straightforward aggregation?
> - Why would you validate join cardinality before trusting the row count of a merged data frame?
> - How would you reproduce an analysis result in a clean environment months later?
> - What's the difference between a statistically significant result and a practically important one?

## Official References

- [An Introduction to R](https://cran.r-project.org/doc/manuals/r-release/R-intro.html)
- [R language definition](https://cran.r-project.org/doc/manuals/r-release/R-lang.html)
- [Writing R Extensions](https://cran.r-project.org/doc/manuals/r-release/R-exts.html)

Return to [Programming Languages](./README.md).
