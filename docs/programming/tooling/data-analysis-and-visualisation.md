# Data Analysis and Visualisation Libraries

The projects use focused Python and R libraries to clean tabular data, calculate results, and produce charts or maps. Libraries accelerate implementation, but data meaning, validation, and statistical assumptions remain application responsibilities.

## NumPy and pandas

NumPy provides typed multidimensional arrays and vectorised numerical operations. pandas provides labelled tables, indexing, joins, grouping, missing-value handling, and CSV-oriented workflows.

- Define expected columns, types, units, uniqueness, and missing-value policy before transformation.
- Avoid accidental chained mutation and unclear implicit type conversion.
- Inspect join cardinality so duplicate keys do not multiply rows unexpectedly.
- Keep raw input separate from cleaned and derived data.
- Test empty inputs, malformed rows, duplicate records, locale-sensitive values, and date boundaries.

## ggplot2

ggplot2 builds R graphics from data, aesthetic mappings, statistical transformations, scales, coordinates, and layers. Prepare tidy data before plotting and label units and transformations. A visually plausible chart can still encode an invalid comparison.

## Leaflet and Mapping Libraries

Leaflet renders interactive web maps. R integrations can combine it with spatial and map-data packages. Validate coordinate reference systems, longitude/latitude order, missing geometry, attribution, and the licensing and availability of tile providers.

## Analytical Applications

Interactive PCA, clustering, and regression controls should constrain invalid values and explain what a result means. Separate the calculation from Shiny reactivity so algorithms can be tested with known datasets.

## Project Connections

The crawler uses pandas for daily CSV comparison. Weathercraft's asset-cleaning helper uses Pillow and NumPy. The R Shiny projects use ggplot2 and a set of Leaflet and spatial mapping packages.

## Related Guides

- [Python](../languages/python.md)
- [R](../languages/r.md)
- [Shiny](../frameworks/shiny.md)
- [Testing](../../quality-engineering/testing.md)

Return to [Development Tooling](./README.md).
