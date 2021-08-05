# Solidity Cheatsheet

- [4 Types of Visibility for Functions & State Variables](#4-types-of-visibility-for-functions-&-state-variables)
- [Function Modifiers](#function-modifiers)
- [Storage vs. Memory](#storage-vs-memory)

## [4 Types of Visibility for Functions & State Variables](https://docs.soliditylang.org/en/v0.8.6/contracts.html)

- `public` - Default for all contracts.
  - Public _functions_ are part of the contract interface and can be either
    called internally or via messages.
  - For public _state variables_, an automatic getter function is generated.
- `private` - Private _functions_ and _state variables_ are only visible for
  the contract they are defined in — not for derived contracts.
- `external` - Only possible for _functions_, **not** _state variables_. Public
  functions are part of the contract interface, meaning that they can be called
  from other contracts and via transactions. They **cannot be accessed
  internally.**
  - An external function `f` cannot be called internally: `f()` would not work,
    but `this.f()` would.
- `internal` - Internal _functions_ and _state variables_ can only be accessed
  internally (from within the current contract or derived contracts) without
  using `this.
  - This is the **default** visibility level for _state variables_.

**Note:** The code inside your contract is visible to anyone. These settings
only control whether other contracts can read or modify the information inside
it.

Remember that unlike functions state variables _only have three levels of
visibility_:

- `public`
- `private`
- `internal` (default)

### Differences between `public` and `external` functions

- `external` functions cannot be accessed internally. `public` functions can be
  accessed internally or externally.
- `public` functions use more gas than `external` functions when dealing with
  large arrays of data.
  - This is because `public` functions copy arguments to `memory`, while
    `external` functions read from `calldata`, which is cheaper than memory
    allocation.
  - However, the performance benefits of `external` are only really noticeable
    when you call a function externally and pass in large arrays.
- As a best practice, use `external` if you expect that the function will only
  be called externally, and `public` if you need to call it internally`.
- [Read more here](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices)

### Differences between `private` and `internal` functions and state variables

- `private` functions and state variables can only be accessed from the
  contract they're defined in.
- `internal` functions and state variables can be accessed from the contract
  they're defined in, as well as any derived contracts.

## Function Modifiers

`view` functions view data but do not modify it.

```solidity
string greeting = "What's up dog";

function sayHello() public view returns (string memory) {
  return greeting;
}
```

`pure` functions don't access any data in the app.

```solidity
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

## Storage vs. Memory

In Solidity, there are two locations you can store variables — in storage and
in memory.

Storage refers to variables stored permanently on the blockchain. Memory
variables are temporary, and are erased between external function calls to your
contract. Think of it like your computer's hard disk vs RAM.
