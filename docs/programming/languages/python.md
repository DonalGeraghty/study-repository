# Python

Python is a dynamically typed, general-purpose language used for automation, web services, data processing, testing, and command-line tools. Its concise syntax makes small programs quick to write, but maintainable Python still depends on explicit boundaries, clear data models, tests, and controlled environments.

## Language Model

Names refer to objects; assignment binds a name rather than copying an object. Mutability therefore matters:

```python
original = ["api", "ui"]
alias = original
alias.append("mobile")

print(original)  # ['api', 'ui', 'mobile']
```

Numbers, strings, and tuples are immutable. Lists, dictionaries, sets, and most user-defined objects are mutable. Avoid mutable default arguments because the default object is created once:

```python
def add_tag(tag: str, tags: list[str] | None = None) -> list[str]:
    result = [] if tags is None else list(tags)
    result.append(tag)
    return result
```

Python uses dynamic typing at runtime. Type hints document intent and enable static analysis, but they do not normally enforce types during execution.

## Collections and Comprehensions

Choose collections by required behaviour:

| Need | Typical type |
| --- | --- |
| Ordered mutable sequence | `list` |
| Immutable sequence | `tuple` |
| Unique values and set operations | `set` |
| Key-value lookup | `dict` |

Comprehensions express simple transformations compactly:

```python
failed_names = [test.name for test in tests if test.status == "failed"]
```

Prefer a normal loop when the expression needs multiple branches, side effects, or extensive explanation.

## Functions, Iteration, and Resources

Functions are objects and may be passed to other functions. Iterators produce values lazily, while generators use `yield` to retain execution state between values.

Context managers provide deterministic cleanup:

```python
from pathlib import Path

with Path("results.txt").open(encoding="utf-8") as results:
    first_line = results.readline()
```

The file closes even when the block raises an exception. Create custom context managers when setup and cleanup must always be paired.

## Errors and Data Models

Raise exceptions for outcomes the current operation cannot handle. Catch specific exception types at a boundary that can recover, translate, retry, or add useful context.

```python
try:
    port = int(raw_port)
except ValueError as error:
    raise ConfigurationError("PORT must be an integer") from error
```

Use `dataclasses`, immutable value objects, or validation libraries for structured data instead of passing unrelated dictionaries through every layer.

## Environments and Packaging

A virtual environment isolates project dependencies from the system interpreter:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Record direct and transitive dependencies reproducibly with the project’s selected packaging workflow. Keep application configuration outside source code, never commit secrets, and run tools through the intended environment in both local development and CI.

## Concurrency

- Threads can suit blocking I/O, but shared mutable state still requires coordination.
- `asyncio` supports cooperative concurrency when libraries expose asynchronous APIs.
- Processes can provide parallelism and isolation for CPU-bound work.

Do not add concurrency until measurement identifies a need. Set timeouts, bound concurrency, propagate cancellation, and test failure paths.

## Testing and Debugging

Organise tests around observable behaviour. Keep pure logic separate from files, clocks, networks, and databases so it can be tested cheaply. Use fixtures deliberately and avoid shared state that makes tests order-dependent.

Useful checks include:

```bash
python -m compileall .
python -m unittest
```

Projects commonly add `pytest`, a formatter, a linter, and a type checker. Pin their configuration in the repository so local and CI results agree.

## Readiness Checklist

You should be able to:

- explain name binding, identity, equality, mutability, and scope;
- choose appropriate built-in collections and use iteration lazily;
- write typed functions with clear inputs, outputs, and error behaviour;
- manage resources with context managers;
- isolate dependencies and package a repeatable project;
- distinguish threads, processes, and asynchronous I/O;
- test business logic without unnecessary external dependencies;
- diagnose failures from tracebacks, logs, tests, and debugger state.

## Official References

- [Python tutorial](https://docs.python.org/3/tutorial/)
- [Python standard library](https://docs.python.org/3/library/)
- [Python typing documentation](https://docs.python.org/3/library/typing.html)
- [Python packaging guide](https://packaging.python.org/)

Return to the [Programming Languages](./README.md) guide.
