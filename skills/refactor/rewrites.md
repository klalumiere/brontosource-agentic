---
description: How to use `bronto::rewrite_*` rules.
---

# The pattern

To implement cleanups of expressions, use the pattern

```
struct MyStruct : bronto::rewrite_expr {
    template <typename T>
    BRONTO_BEFORE()
    auto before(T value) { return expr_using(value); }

    template <typename T>
    BRONTO_AFTER()
    auto after(T value) { return replacement_using(value); }
};
```

- If you don't need template parameters it can be a regular function.
- You can use C++20 concepts to constrain the types.

# What to do

1. Write this cleanup to a new header file. Make sure it has all the necessary
   includes to compile, including `<bronto/bronto.hpp>`.
2. Show the user the cleanup and ask for confirmation. Fix anything they
   request.
3. Run `${CLAUDE_SKILL_DIR}/scripts/run-bronto-refactor`, passing in the name of
   the created file as an argument. Also pass `--db <compile-commands-path>`.
