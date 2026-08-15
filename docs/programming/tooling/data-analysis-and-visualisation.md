# Data Analysis and Visualisation Libraries

The projects use focused Python and R libraries to clean tabular data, calculate results, and produce charts or maps. Libraries accelerate implementation, but data meaning, validation, and statistical assumptions remain application responsibilities.

## NumPy and pandas

NumPy provides typed multidimensional arrays and vectorised numerical operations. pandas provides labelled tables, indexing, joins, grouping, missing-value handling, and CSV-oriented workflows.

- Define expected columns, types, units, uniqueness, and missing-value policy before transformation.
- Avoid accidental chained mutation and unclear implicit type conversion.
- Inspect join cardinality so duplicate keys do not multiply rows unexpectedly.
- Keep raw input separate from cleaned and derived data.
- Test empty inputs, malformed rows, duplicate records, locale-sensitive values, and date boundaries.

### Worked pandas Transformation

```python
import pandas as pd


def revenue_by_category(orders, products):
    required_orders = {"product_id", "quantity", "unit_price"}
    required_products = {"product_id", "category"}

    if not required_orders.issubset(orders.columns):
        raise ValueError("orders schema is incomplete")
    if not required_products.issubset(products.columns):
        raise ValueError("products schema is incomplete")

    merged = orders.merge(
        products.loc[:, ["product_id", "category"]],
        on="product_id",
        how="left",
        validate="many_to_one",
        indicator=True,
    )

    if (merged["_merge"] != "both").any():
        raise ValueError("an order references an unknown product")

    return (
        merged.assign(
            revenue=merged["quantity"] * merged["unit_price"]
        )
        .groupby("category", as_index=False)["revenue"]
        .sum()
        .sort_values("revenue", ascending=False)
    )
```

`validate="many_to_one"` turns an accidental duplicate product key into an error instead of silently multiplying order rows. The merge indicator detects missing reference data. Tests should also fix the numeric type and rounding policy if `unit_price` represents exact money.

## ggplot2

ggplot2 builds R graphics from data, aesthetic mappings, statistical transformations, scales, coordinates, and layers. Prepare tidy data before plotting and label units and transformations. A visually plausible chart can still encode an invalid comparison.

## Leaflet and Mapping Libraries

Leaflet renders interactive web maps. R integrations can combine it with spatial and map-data packages. Validate coordinate reference systems, longitude/latitude order, missing geometry, attribution, and the licensing and availability of tile providers.

## Analytical Applications

Interactive PCA, clustering, and regression controls should constrain invalid values and explain what a result means. Separate the calculation from Shiny reactivity so algorithms can be tested with known datasets.

## Common Failure Modes

- allowing a join to multiply rows without checking cardinality;
- letting inferred object/string columns enter numeric calculations;
- replacing missing data with zero without a domain reason;
- presenting correlation, clusters, or components as causal explanations;
- using colour alone or omitting units in a chart;
- mutating the raw data frame in place and losing provenance;
- testing only a screenshot instead of calculation and data invariants.

## Project Connections

The crawler uses pandas for daily CSV comparison. Weathercraft's asset-cleaning helper uses Pillow and NumPy. The R Shiny projects use ggplot2 and a set of Leaflet and spatial mapping packages.

## Related Guides

- [Python](../languages/python.md)
- [R](../languages/r.md)
- [Shiny](../frameworks/shiny.md)
- [Testing](../../quality-engineering/testing.md)

Return to [Development Tooling](./README.md).
