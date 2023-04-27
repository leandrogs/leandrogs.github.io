---
layout: post
title:  "TDD with Elixir"
date:   2023-04-27 12:00:00 +0000
categories: elixir development tdd
---

Test-Driven Development (TDD) is a software development approach where tests are written before the actual code is written. The TDD cycle usually consists of three steps: writing a failing test, writing the code to make the test pass, and then refactoring the code to improve its design.

TDD is often used in Elixir development to help ensure that code is correct and maintainable. By writing tests first, developers can focus on the desired behavior of the code, rather than on implementation details. This approach can also help catch bugs early in the development process, before they become more difficult and expensive to fix.

Elixir has a built-in testing framework called `ExUnit`, which makes it easy to write and run tests for Elixir applications. `ExUnit` provides a range of assertions and test helpers, making it simple to write effective and thorough tests.

Overall, TDD can be a valuable approach to Elixir development, helping to improve code quality, reduce bugs, and increase developer productivity.

## A well-written test

A good test should be reliable, repeatable, and focused on a specific behavior or feature. Here are some characteristics of a good test:

- A good test should be deterministic and repeatable: The test should always produce the same results when run, and should not depend on external factors such as network conditions or time of day.
- A good test should be isolated and independent: The test should not depend on the state of other tests or external systems, and should not have any side effects that could affect other tests.
- A good test should be easy to read and understand: The test should clearly and concisely describe the expected behavior of the code being tested, and should use descriptive names and comments to make it easy to understand.
- A good test should be maintainable: The test should be easy to update and modify as the code being tested changes, without requiring extensive rewrites.
- A good test should be fast: The test should run quickly, allowing developers to iterate quickly and get feedback on their changes.

Here's an example of a well-written Elixir test that meets these criteria:

```elixir
defmodule MyModuleTest do
  use ExUnit.Case

  test "adds two numbers" do
    result = MyModule.add(2, 3)
    assert result == 5
  end
end
```

This test is focused on a specific behavior (adding two numbers), is deterministic and repeatable, and is independent of other tests. The test is easy to read and understand, and is maintainable as the `MyModule` implementation changes. Additionally, the test runs quickly, allowing developers to iterate quickly and get feedback on their changes.

## Red-Green-Refactor!

The TDD cycle involves writing a failing test (Red), making the test pass by writing the minimum amount of code necessary (Green), and then refactoring the code to improve its quality (Refactor). By following this cycle, developers can ensure that their code is thoroughly tested and that it meets the requirements of the project. The process of writing tests before writing code encourages developers to think more deeply about the design of their software and can lead to more robust and maintainable code.

Let's say we are building a function that will take a list of integers and return the sum of those integers. Here are the steps we would take in a typical TDD cycle:

### Write a failing test (Red)

The first step in the TDD cycle is to write a test that fails. In this case, we might write a test that checks whether our sum function returns the correct value for a given input:

```elixir
defmodule MyModuleTest do
  use ExUnit.Case

  test "returns the sum of a list of integers" do
    input = [1, 2, 3]
    expected_output = 6
    assert MyModule.sum(input) == expected_output
  end
end
```

When we run this test, it will fail because we have not yet implemented the `sum` function.

### Write the code to make the test pass (Green)

The next step is to write the code to make the test pass. In this case, we might write a simple implementation of the sum function that adds up the elements in the list:

```elixir
defmodule MyModule do
  def sum(list) do
    Enum.sum(list)
  end
end
```

When we run the test again, it should pass.

### Refactor the code (Refactor)

Finally, we can refactor the code to improve its design. In this case, there isn't much we can do to improve the design of the sum function, but we might add a few additional tests to ensure that it works correctly in edge cases:

```elixir
defmodule MyModuleTest do
  use ExUnit.Case

  test "returns the sum of a list of integers" do
    input = [1, 2, 3]
    expected_output = 6
    assert MyModule.sum(input) == expected_output
  end

  test "returns 0 for an empty list" do
    input = []
    expected_output = 0
    assert MyModule.sum(input) == expected_output
  end

  test "returns the same value for a list with one element" do
    input = [42]
    expected_output = 42
    assert MyModule.sum(input) == expected_output
  end
end
```

By following this cycle, we can be confident that our code is correct and well-designed, and can quickly catch any errors that might arise.