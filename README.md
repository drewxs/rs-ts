# rust-ts

[![NPM](https://img.shields.io/npm/v/rust-ts?logo=npm)](https://www.npmjs.com/package/rust-ts)
[![Build](https://github.com/drewxs/rust-ts/actions/workflows/release.yml/badge.svg)](https://github.com/drewxs/rust-ts/actions/workflows/release.yml)
[![Docs](https://img.shields.io/github/deployments/drewxs/rust-ts/production?label=Docs&logo=vercel&logoColor=white)](https://rust-ts.vercel.app/)

TypeScript implementations of Rust `std` modules and some rust-like wrappers.

[Documentation](https://rust-ts.vercel.app/)

## Contents

-   [Installation](#Installation)
-   [Modules](#Modules)
    -   [Result](#Result)
    -   [Option](#Option)
    -   [match](#match)
    -   [if_let](#if_let)
    -   [fetchr](#fetchr)

## Installation

```bash
# npm
npm i rust-ts
# yarn
yarn add rust-ts
# pnpm
pnpm i rust-ts
```

## Modules

### Result

`Result<T, E>` is the type used for returning and propagating errors. It is an union type with the variants, `Ok<T, E>`, representing success and containing a value, and `Err<E>`, representing error and containing an error value.

```typescript
type Result<T, E> = Ok<T, E> | Err<T, E>;
```

##### Usage

```typescript
import {Ok, Err, Result} from "rust-ts";

const divide = (x: number, y: number): Result<number, string> =>
    y === 0 ? Err("Can't divide by zero") : Ok(x / y);

divide(10, 5)
    .and_then(z => divide(z, 2))
    .map(z => [z, z + 1])
    .match({
        ok: ([v, x]) => console.log(v + x),
        err: e => console.log(e),
    });
```

### Option

`Option<T>` represents an optional value: every `Option` is either `Some` and contains a value, or `None`, and does not.

```typescript
type Option<T> = Some<T> | None<T>;
```

##### Usage

```typescript
import {Some, None, Option, match} from "rust-ts";

const divide = (x: number, y: number): Option<number> => (y === 0 ? None() : Some(x / y));

const result = divide(2.0, 3.0);

match(result, {
    some: x => console.log(x),
    none: () => console.log("Cannot divide by zero"),
});
```

### match

`match` is available as a standalone function as well as defined methods for `Result` and `Option`.

##### Usage

```typescript
import {Some, None, Option, Err, Ok, Result, match} from "rust-ts";

const add = (x: number, y: number): Option<number> => (y === 0 ? None() : Some(x + y));

const divide = (x: number, y: number): Result<number, string> =>
    y === 0 ? Err("Cannot divide by zero") : Ok(x + y);

const option = add(3, 4);
option.match({
    some: x => console.log(x),
    none: () => console.log("none"),
});

const result = option.ok_or("error");
result.map(x => x + 1).and_then(x => divide(x, 2));
match(result, {
    ok: x => console.log(x),
    err: e => console.log(e),
});
```

### if_let

`if_let` is available as a standalone function as well as a defined method for `Option`.

##### Usage

```typescript
import {Some, None, Option, if_let} from "rust-ts";

const divide = (x: number, y: number): Option<number> => (y === 0 ? None() : Some(x / y));

const option = divide(2, 0);
option.if_let({
    some: x => console.log("Success"),
});
```

### fetchr

`fetchr` is a wrapper around `fetch` that returns a `Promise<Result<T, E>>` with the data or error values instead of a `Promise<Response>`.

It also resolves the `json`, `text`, or `blob` accordingly based on the `content-type`.

If you need more granular control, use `fetchx` instead, which returns a `Promise<Result<Response, E>>`.

##### Usage

```typescript
const url = "https://yourapiurl.com";
const res = await fetchr<ExpectedType, CustomErrorType>(url); // Optional generics for expected types, defaults to <unknown, Error>

// Fallback data with `unwrap_or`
const data = res.unwrap_or(FALLBACK_DATA);

// Match to handle each case
res.match({
    ok: data => /* do something with data */,
    err: err => /* handle errors */,
});
```

### Contributions

Are welcome!

Refer to the [docs](https://rust-ts.vercel.app/) for more details and examples.
