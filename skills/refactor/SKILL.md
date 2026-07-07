---
description: Plan and execute a refactoring task
---

# One-time setup

If you already have a `compile_commands.json` file set up, skip this section.

Guide the user through producing a compile_commands.json.

- If this is a bazel project, they should probably use
  https://github.com/hedronvision/bazel-compile-commands-extractor
- If this is a CMake project, they can use
  [CMAKE_EXPORT_COMPILE_COMMANDS](https://cmake.org/cmake/help/latest/variable/CMAKE_EXPORT_COMPILE_COMMANDS.html).
- For most other projects, [Bear](https://github.com/rizsotto/bear) is the right
  choice.

# Planning

Ask the user about what they want to refactor. Break their response down into a
collection of changes, each of which can be described as "replacing all X with
Y". Stop exploring the codebase once you have enough information to describe the
changes. You do not need to find all call sites of a rewritten type or function.
The tool does that for you.

For each of these changes, further break down the task into steps with each
clearly labeled with its category for the list below.

- Addition - A change scoped to a single translation unit and only ever adds
  functions, type-definitions. Use this to add the new component the user wants
  to see.
- Reimplement - Implements one function in terms of another. Use this to ensure
  the old version can be described in terms of the new version.
- Propagate - Replaces a function call with its definition, or a type alias with
  its definition. Use this to push the new version to all of the users of the
  old version.
- Cleanup - Sometimes after propagation, expressions are correct but not what a
  human would have written. This is a dedicated step to clean up those cases.
- Deletion - Use this to clean up after yourself.

Check for common cleanups you'll want to do. If a propagation inlines a
function, there may be parts of the expression that are done inside the function
and then undone outside the function. These can be collapsed.

Once you have built a plan, show the user the sequence of steps, clearly marking
each step with its category.

## Propagation mechanisms

- You can propagate type-aliases with `using OldType BRONTO_INLINE() = NewType`.
- You can propagate function replacements with `BRONTO_INLINE()` annotating the
  function. The annotation goes after template parameters but before the
  return-type, as in these examples.

  ```
  BRONTO_INLINE()
  void OldFunc() { ... }
  ```

  ```
  template <typename T>
  BRONTO_INLINE()
  void OldFuncTemplate() { ... }
  ```

## Special cases

The following are some special cases where `bronto-refactor` has builtin
primitives that can be helpful.

- **Renamespacing.** - You can add `BRONTO_INLINE()` to a `using namespace` and
  all symbols will be requalified. Use this to add/remove/change namespaces.

# Executing

Once the user confirms the plan, for each step in the plan,

- For propagations, just run
  `${CLAUDE_SKILL_DIR}/scripts/run-bronto-refactor --db <compile-commands-path>`
  with no extra arguments. Don't check --help first.
- For cleanups, read `${CLAUDE_SKILL_DIR}/rewrites.md`, and follow it's
  instructions.
- For everything else
  - You may merge adjacent steps.
  - Reimplementations should
    - _Always_ be in the header,
    - _Always_ be marked as `inline`, and
    - _Always_ be annotated with `BRONTO_INLINE()`
  - Other than that, do exactly what is asked, but no more. The goal is make the
    change safely, not to improve the code.

# Key notes

Remember that `BRONTO_*` macros or anything in the `bronto` namespace require
`#include <bronto/bronto.hpp>`.
