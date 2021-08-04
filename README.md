# Cryptozombies Notes

Outlines core Solidity concepts covered in the
[Cryptozombies online course](https://cryptozombies.io/). Consists of a mixture
of lesson text and original summaries.

Check out the [Solidity Cheatsheet](cheatsheet.md) for a quick summary of some
of these concepts.

## Resources

- [Official Solidity Cheatsheet](https://docs.soliditylang.org/en/v0.8.6/cheatsheet.html)

## Lesson 1: Making the Zombie Factory

### [Contracts (Chapter 2)](https://cryptozombies.io/en/lesson/1/chapter/2)

Solidity's code is encapsulated in contracts. A contract is the fundamental
building block of Ethereum applications — all variables and functions belong to
a contract, and this will be the starting point of all your projects.

An empty contract named HelloWorld would look like this:

```solidity
contract HelloWorld {

}
```

All solidity source code should start with a **"version pragma"** — a
declaration of the version of the Solidity compiler this code should use. This
is to prevent issues with future compiler versions potentially introducing
changes that would break your code.

Here's an example of a smart contract that will be compiled by any compiler
with a version 0.7.3 or higher:

```solidity
pragma solidity >=0.7.3;

contract HelloWorld {

}
```

### [State Variables & Integers (Chapter 3)](https://cryptozombies.io/en/lesson/1/chapter/3)

#### State Variables

State variables are _permanently stored_ in contract storage. They're written
to the Ethereum blockchain. Permanently. (Unlike data stored in `memory`, which
we'll discuss later.)

When we work with smart contracts, we store and access data from the blockchain
rather than traditional databases. Therefore, using state variables in our
contracts is like the equivalent of storing this information in a database.

```solidity
contract Example {
  // This will be stored permanently in the blockchain
  uint myUnsignedInteger = 100;
}
```

In this example contract, we created a `uint` called `myUnsignedInteger` and
set it equal to 100.

#### Unsigned Integers: `uint`

The `uint` data type is an _unsigned integer_, meaning its value must be
_non-negative_. For signed integers, you can use the `int` type.

`uint` is actually equivalent to `uint256`, a 256-bit unsigned integer. You can
also use uints with fewer bits (`uint8`, `uint16`, `uint32`...) to be more
cost-efficient, which we'll also discuss later.

### [Math Operations (Chapter 4)](https://cryptozombies.io/en/lesson/1/chapter/4)

Solidity supports the same math operations you'd expect from a programming
language:

- Addition: x + y
- Subtraction: x - y,
- Multiplication: x \* y
- Division: x / y
- Modulus / remainder: x % y
- Exponents: x \*\* 2

### [Structs (Chapter 5)](https://cryptozombies.io/en/lesson/1/chapter/5)

A `struct` is a complex data type that has multiple properties:

```solidity
struct Person {
  uint age;
  string name;
}
```

### [Arrays (Chapter 6)](https://cryptozombies.io/en/lesson/1/chapter/6)

Arrays in Solidity are similar to arrays in JavaScript.

Solidity has two types of arrays:

- **Fixed arrays**: Have fixed lengths.
- **Dynamic arrays**: Have no fixed size and can contain any number of
  elements.

```solidity
// Array with a fixed length of 2 elements:
uint[2] fixedArray;
// another fixed Array, can contain 5 strings:
string[5] stringArray;
// a dynamic Array - has no fixed size, can keep growing:
uint[] dynamicArray;
```

You can also create an array of structs. Remember the `Person` struct from the
last lesson?

```solidity
Person[] people; // dynamic Array, we can keep adding to it
```

Since state variables are stored permanently in the blockchain, creating a
dynamic array of structs like this can be useful for storing structured data in
your contract — like a database, remember?

#### Public Arrays

You can declare an array as `public` , and Solidity will automatically create a
`getter` method for it. Here's what the syntax looks like:

```solidity
Person[] public people;
```

Other contracts could then **read** from this array, but _not_ **write** to it.
This makes it a useful pattern for storing public data in your contracts.

## [Function Declarations (Chapter 7)](https://cryptozombies.io/en/lesson/1/chapter/7)

Function declarations in Solidity look like this:

```solidity
function eatHamburgers(string memory _name, uint _amount) public {

}
```

The above function, `eatHamburgers`, takes two parameters:

- A `string` called `_name`
- A `uint` called `_amount`

Note that the function is `public`, meaning that it can be called internally or
via messages (externally).

_Why do both parameters start with an underscore?_ It's convention to start a
function parameter variable name with an underscore (e.g. `_name` instead of
`name`) _to differentiate them from global variables._

### Reference Types

In Solidity, a `string` is a reference type, while a `uint` is a value type.

There are two ways to pass arguments to Solidity functions:

- By **value** - the solidity creates a new copy of the param's value and
  passes it to the function. The value of the original parameter is not
  changed.
- By **reference** - The functions is called with a reference to the original
  value. If the function changes the value of the variable received, the
  original variable is also mutated.

## [Working with Structs & Arrays (Chapter 8)](https://cryptozombies.io/en/lesson/1/chapter/8)

Let's look back at the `Person` struct we created earlier:

```solidity
struct Person {
  uint age;
  string name;
}

Person[] public people;
```

Now let's create new `Person`s and add them to the `people` array:

```solidity
// create a New Person:
Person satoshi = Person(172, "Satoshi");

// Add that person to the Array:
people.push(satoshi);
```

Let's do this all in one line:

```solidity
people.push(Person(172, "Satoshi"));
```

Note that `array.push()` adds items to the end of an array.

```solidity
uint[] numbers;
numbers.push(5);
numbers.push(10);
numbers.push(15);
// numbers is now equal to [5, 10, 15]
```

## [Private & Public Functions](https://cryptozombies.io/en/lesson/1/chapter/9)

In Solidity, functions are `public` by default. This means that anyone (or any
other contract) can call your contract's function and execute its code.

This can create security concerns, as it potentially leaves your contract
vulnerable to attacks. It's a good practice to make your functions `private` by
default (meaning that they can only be accessed from within the same contract)
and then only mark a function public if you want to expose it to everyone.

Here's an example of how to declare a private function:

```solidity
uint[] numbers;

function _addToArray(uint _number) private {
  numbers.push(_number);
}
```

Only functions within this same contract will be able to call `_addToArray` and
add to the `numbers` array.

Note the `_` at the beginning of `\_addToArray. Just like with function
parameters, it's customary to start private functions with an underscore.

## [More on Functions](https://cryptozombies.io/en/lesson/1/chapter/10)

### Return values

If a function returns a value, you should also specify the type when declaring
the function:

```solidity
string greeting = "What's up dog";

function sayHello() public returns (string memory) {
  return greeting;
}
```

### Function modifiers

The above function (`sayHello`) doesn't modify state — it just returns a
pre-existing state variable. Therefore, it can be declared a `view` function:

```solidity
string greeting = "What's up dog";

function sayHello() public view returns (string memory) {
  return greeting;
}
```

You can also write `pure` functions, which don't access any data in the app.

```solidity
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

## [Keccak256 & Typecasting](https://cryptozombies.io/en/lesson/1/chapter/11)

### `keccak256`

`keccak256` is a hash function built into Ethereum and is a version of SHA3. It
has many purposes in Ethereum, but we'll use it to randomly generate a number.

Note hat `keccak256` expects a single parameter of type `bytes`. This requires
us to "pack" our parameters before calling `keccak256`:

```solidity
//6e91ec6b618bb462a4a6ee5aa2cb0e9cf30f7a052bb467b0ba58b8748c00d2e5
keccak256(abi.encodePacked("aaaab"));
//b1f078126895a1424524de5321b339ab00408010b7cf0e6ed451514981e58aa9
keccak256(abi.encodePacked("aaaac"));
```

### Typecasting

Solidity lets you convert between data types. For example:

```solidity
uint8 a = 5;
uint b = 6;
// throws an error because a * b returns a uint, not uint8:
uint8 c = a * b;
// we have to typecast b as a uint8 to make it work:
uint8 c = a * uint8(b);
```
