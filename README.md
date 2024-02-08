# Function Dialect Starter
This is a starter project for developers who want to bring their custom C++ operators to Function.

> ⚠️: This is an extremely advanced Function use-case, targeted towards developers who want to accelerate their Function predictors with custom accelerator or hardware support.

## Background
At a high level, Function is a cross-platform compiler and optimizer for running stateless Python functions. The platform works in two steps:

1. Tracing: We generate a self-contained intermediate representation (IR) graph describing all operations in a developer's Python function.

2. Lowering: We lower the IR graph to hundreds or thousands of equivalent C++ implementations, each of which we then cross-compile for Android, iOS, macOS, Linux, WebAssembly, and Windows.

Our lowering process is powered by "Operators", which provide a mapping from a high-level Python operation into a concrete C++ implementation. We then collect these operators into a "Dialect".

## Defining Operators
An operator is simply a C++ functor decorated with Function-specific annotations (see [`Dialect.hpp`](https://github.com/fxnai/fxnc/blob/main/cxx/Dialect.hpp)). For example, below is an operator for the Python addition (`a + b`) operation:

```cpp
#include <Function/Function.hpp>

struct AddOp final {

    FXN_FUNCTION_OP("operator.add")
    FXN_OP_DESCRIPTION("Add two input variables.")
    FXN_OP_PLATFORM(FXN_PLATFORM_ANDROID | FXN_PLATFORM_IOS | FXN_PLATFORM_WASM)

    float operator() (float a, float b) const {
        return a + b;
    }
};
```

Function supports multiple kinds of operators, including type casts, AI model inference, and more. See [docs/Create.md](docs/Create.md) for more information about how to create dialects.

> Function maintains an internal dialect that provides highly-optimized C++ implementations for much of the Python language syntax; base objects like lists and dictionaries; and AI inference operations.

## Our Approach to Performance
Function is architected to completely separate code generation from performance optimization. This means that our platform is designed to search for the best performing operator for every prediction function for every unique device--all at runtime.

> 🤔 Why do this? We believe that the highest-performing programs aren't written--they are discovered.

When Function traces a Python function, we map each operation in Python to a set of equivalent C++ implementations based on the dialects we have available. For instance, we might have 10 separate C++ implementations of the `a + b` Python operation that use anything from the C++ addition operator to `armv8` inline assembly.

We then take all combinations across all nodes in the traced function, and compile each of these for each platform we support. At runtime, we use telemetry to discover which implementations work best for each unqiue device, and serve the best implementation.

Given how many implementations we emit, and how empirical performance optimization is, we choose to never make assumptions for how a given implementation might perform on a given device.

> Developers will be able to provide their custom dialects on [fxn.ai](https://fxn.ai).

___

## Useful Links
- [Discover predictors to use in your apps](https://fxn.ai/explore).
- [Join our Discord community](https://fxn.ai/community).
- [Check out our docs](https://docs.fxn.ai).
- Learn more about us [on our blog](https://blog.fxn.ai).
- Reach out to us at [hi@fxn.ai](mailto:hi@fxn.ai).

Function is a product of [NatML Inc](https://github.com/natmlx).