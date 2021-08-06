# Cryptozombies Notes

Outlines core Solidity concepts covered in the
[Cryptozombies online course](https://cryptozombies.io/). Consists of a mixture
of lesson text and original summaries.

Check out the [Solidity Cheatsheet](cheatsheet.md) for a quick summary of some
of these concepts.

## Resources

- [Official Solidity Cheatsheet](https://docs.soliditylang.org/en/v0.8.6/cheatsheet.html)

## Table of Contents

- [Lesson 1: Solidity Basics](#lesson-1-solidity-basics)
  - [Contracts](#contracts)
  - [State Variables And Integers](#state-variables-and-integers)
    - [State Variables](#state-variables)
    - [Unsigned Integers: `uint`](#unsigned-integers-uint)
  - [Math Operations](#math-operations)
  - [Structs](#structs)
  - [Arrays](#arrays)
  - [Function Declarations](#function-declarations)
    - [Reference Types](#reference-types)
  - [Working with Structs And Arrays](#working-with-structs-and-arrays)
  - [Private And Public Functions](#private-and-public-functions)
  - [Internal And External Functions](#internal-and-external-functions)
  - [More on Functions](#more-on-functions)
    - [Return values](#return-values)
    - [Functions modifiers](#function-modifiers)
  - [Keccak256 And Typecasting](#keccak256-and-typecasting)
    - [`keccak256`](#keccak256)
    - [Typecasting](#typecasting)
  - [Events](#events)
  - [Web3.js](#web3.js)
- [Lesson 2: Beyond Basics](#lesson-2-beyond-basics)
  - [Mappings And Addresses](#mappings-and-addresses)
    - [Addresses](#addresses)
    - [Mappings](#mappings)
  - [Msg.sender](#msg.sender)
  - [Require](#require)
  - [Inheritance](#inheritance)
  - [Import](#import)
  - [Storage vs. Memory (Data Location)](#storage-vs-memory-data-location)
  - [Interacting With Other Contracts](#interacting-with-other-contracts)
  - [Using an Interface](#using-an-interface)
  - [Handling Multiple Return Values](#handling-multiple-return-values)
  - [If Statements](#if-statements)
- [Lesson 3: Advanced Solidity Concepts](#lesson-3-advanced-solidity-concepts)
  - [Immutability of Contracts](#immutability-of-contracts)
  - [Ownable Contracts](#ownable-contracts)
  - [Function Modifiers](#function-modifiers)
  - [Gas](#gas)
    - [What's Gas?](#what's-gas)
    - [Why Is Gas Necessary?](#why-is-gas-necessary)
    - [How to Save Gas by Packing Structs](#how-to-save-gas-by-packing-structs)
  - [Time Units](#time-units)
  - [Passing Structs as Arguments](#passing-structs-as-arguments)
  - [More on Function Modifiers](#more-on-function-modifiers)
  - [Saving Gas With `view` Functions](#saving-gas-with-view-functions)
  - [Storage Is Expensive](#storage-is-expensive)
    - [Declaring arrays in memory](#declaring-arrays-in-memory)
  - [For Loops](#for-loops)
- [Lesson 4](#lesson-4)

## Lesson 1: Solidity Basics

### [Contracts](https://cryptozombies.io/en/lesson/1/chapter/2)

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

### [State Variables And Integers](https://cryptozombies.io/en/lesson/1/chapter/3)

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

### [Math Operations](https://cryptozombies.io/en/lesson/1/chapter/4)

Solidity supports the same math operations you'd expect from a programming
language:

- Addition: x + y
- Subtraction: x - y,
- Multiplication: x \* y
- Division: x / y
- Modulus / remainder: x % y
- Exponents: x \*\* 2

### [Structs](https://cryptozombies.io/en/lesson/1/chapter/5)

A `struct` is a complex data type that has multiple properties:

```solidity
struct Person {
  uint age;
  string name;
}
```

### [Arrays](https://cryptozombies.io/en/lesson/1/chapter/6)

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

### [Function Declarations](https://cryptozombies.io/en/lesson/1/chapter/7)

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

#### Reference Types

In Solidity, a `string` is a reference type, while a `uint` is a value type.

There are two ways to pass arguments to Solidity functions:

- By **value** - the solidity creates a new copy of the param's value and
  passes it to the function. The value of the original parameter is not
  changed.
- By **reference** - The functions is called with a reference to the original
  value. If the function changes the value of the variable received, the
  original variable is also mutated.

### [Working with Structs And Arrays](https://cryptozombies.io/en/lesson/1/chapter/8)

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

### [Private And Public Functions](https://cryptozombies.io/en/lesson/1/chapter/9)

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

### [Internal And External Functions](https://cryptozombies.io/en/lesson/2/chapter/9)

Solidity also provides us with two more types of visibility for functions:
`internal` and `external`.

`internal` is the same as `private`, but with one bonus: it's also accessible
to contracts that inherit from this contract.

`external` is similar to `public`, but `external` functions can **only** be
called outside the contract. Unlike `public`, they can't be called by other
functions inside that contract.

### [More on Functions](https://cryptozombies.io/en/lesson/1/chapter/10)

#### Return values

If a function returns a value, you should also specify the type when declaring
the function:

```solidity
string greeting = "What's up dog";

function sayHello() public returns (string memory) {
  return greeting;
}
```

#### Function modifiers

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

### [Keccak256 And Typecasting](https://cryptozombies.io/en/lesson/1/chapter/11)

#### `keccak256`

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

### [Events](https://cryptozombies.io/en/lesson/1/chapter/13)

Events let your contract communicate with your app's frontend, which can listen
for certain events and perform actions when they happen.

```solidity
// declare the event
event IntegersAdded(uint x, uint y, uint result);

function add(uint _x, uint _y) public returns (uint) {
  uint result = _x + _y;
  // fire an event to let the app know the function was called:
  emit IntegersAdded(_x, _y, result);
  return result;
}
```

A JavaScript implementation for this event on the frontend could look something
like this:

```javascript
YourContract.IntegersAdded(function (error, result) {
  // do something with result
});
```

### [Web3.js](https://cryptozombies.io/en/lesson/1/chapter/14)

Ethereum has a JavaScript library called
[Web3.js](https://web3js.readthedocs.io/en/v1.2.11/index.html).

We'll go into this later, but here's an example of how a frontend could use the
library to interact with a smart contract.

**Contract**

```solidity
pragma solidity ^0.4.25;

contract ZombieFactory {

    event NewZombie(uint zombieId, string name, uint dna);

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;

    function _createZombie(string _name, uint _dna) private {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        emit NewZombie(id, _name, _dna);
    }

    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(abi.encodePacked(_str)));
        return rand % dnaModulus;
    }

    function createRandomZombie(string _name) public {
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }

}
```

**Frontend, using Web3.js**

```javascript
// Here's how we would access our contract:
var abi = /* abi generated by the compiler */
var ZombieFactoryContract = web3.eth.contract(abi)
var contractAddress = /* our contract address on Ethereum after deploying */
var ZombieFactory = ZombieFactoryContract.at(contractAddress)
// `ZombieFactory` has access to our contract's public functions and events

// some sort of event listener to take the text input:
$("#ourButton").click(function(e) {
  var name = $("#nameInput").val()
  // Call our contract's `createRandomZombie` function:
  ZombieFactory.createRandomZombie(name)
})

// Listen for the `NewZombie` event, and update the UI
var event = ZombieFactory.NewZombie(function(error, result) {
  if (error) return
  generateZombie(result.zombieId, result.name, result.dna)
})

// take the Zombie dna, and update our image
function generateZombie(id, name, dna) {
  let dnaStr = String(dna)
  // pad DNA with leading zeroes if it's less than 16 characters
  while (dnaStr.length < 16)
    dnaStr = "0" + dnaStr

  let zombieDetails = {
    // first 2 digits make up the head. We have 7 possible heads, so % 7
    // to get a number 0 - 6, then add 1 to make it 1 - 7. Then we have 7
    // image files named "head1.png" through "head7.png" we load based on
    // this number:
    headChoice: dnaStr.substring(0, 2) % 7 + 1,
    // 2nd 2 digits make up the eyes, 11 variations:
    eyeChoice: dnaStr.substring(2, 4) % 11 + 1,
    // 6 variations of shirts:
    shirtChoice: dnaStr.substring(4, 6) % 6 + 1,
    // last 6 digits control color. Updated using CSS filter: hue-rotate
    // which has 360 degrees:
    skinColorChoice: parseInt(dnaStr.substring(6, 8) / 100 * 360),
    eyeColorChoice: parseInt(dnaStr.substring(8, 10) / 100 * 360),
    clothesColorChoice: parseInt(dnaStr.substring(10, 12) / 100 * 360),
    zombieName: name,
    zombieDescription: "A Level 1 CryptoZombie",
  }
  return zombieDetails
}
```

## Lesson 2: Beyond Basics

### [Mappings And Addresses](https://cryptozombies.io/en/lesson/2/chapter/2)

#### Addresses

The Ethereum blockchain is made up of accounts (like bank accounts). Each
account has a balance of [Ether](https://en.wikipedia.org/wiki/Ethereum#Ether).
You can send and receive Ether payments to other accounts (just like a bank
account can wire money to and receive transfers from other accounts).

Each account has an `address` — similar to an account number. It looks like
this:

```
0x0cE446255506E92DF41614C46F1d6df9Cc969183
```

For now, know that each address is owned by a specific **user** or **smart
contract**.

#### Mappings

We've already covered **structs** and **arrays**. **Mappings** are another way
to store organized data in Solidity. They're essentially key-value pairs, like
JavaScript objects with only one entry.

```solidity
// For a financial app, storing a uint that holds the user's account balance:
mapping (address => uint) public accountBalance;
// Or could be used to store / lookup usernames based on userId
mapping (uint => string) userIdToName;
```

In the first example, the _key_ is an `address`, and the _value_ is a `uint`.
In the second example, the _key_ is a `uint`, and the _value_ is a `string`.

### [Msg.sender](https://cryptozombies.io/en/lesson/2/chapter/3)

In Solidity, there are certain **global variables** that are available to all
functions. One of these is `msg.sender`, which refers to the `address` of the
_person_ or _smart contract_ who called the current function.

`msg.sender` will always exist when calling a function, since all Solidity
function executions start with an external caller.

Here's an example of using `msg.sender` and updating a mapping:

```solidity
mapping (address => uint) favoriteNumber;

function setMyNumber(uint _myNumber) public {
  // Update our `favoriteNumber` mapping to store `_myNumber` under `msg.sender`
  favoriteNumber[msg.sender] = _myNumber;
  // ^ The syntax for storing data in a mapping is just like with arrays
}

function whatIsMyNumber() public view returns (uint) {
  // Retrieve the value stored in the sender's address
  // Will be `0` if the sender hasn't called `setMyNumber` yet
  return favoriteNumber[msg.sender];
}
```

In this example, anyone could call `setMyNumber` and store a `uint` in our
contract, which would then be mapped to their address. When they then call
`whatIsMyNumber`, the function will return the `uint` that they stored.

Using `msg.sender` gives you the security of the Ethereum blockchain. A
malicious party can only modify someone else's data if they steal the private
key associated with their Ethereum address.

### [Require](https://cryptozombies.io/en/lesson/2/chapter/4)

A `require` statement forces a function to throw an error and stop executing if
some condition is not true.

```solidity
function sayHiToVitalik(string memory _name) public returns (string memory) {
  // Compares if _name equals "Vitalik". Throws an error and exits if not true.
  // (Side note: Solidity doesn't have native string comparison, so we
  // compare their keccak256 hashes to see if the strings are equal)
  require(keccak256(abi.encodePacked(_name)) == keccak256(abi.encodePacked("Vitalik")));
  // If it's true, proceed with the function:
  return "Hi!";
}
```

If you call this function with `sayHiToVitalik("Vitalik")`, it will return
"Hi!". If you call it with any other input, it will throw an error and not
execute.

Therefore require is quite useful for verifying certain conditions that must be
true before running a function.

### [Inheritance](https://cryptozombies.io/en/lesson/2/chapter/5)

To avoid cramming a single contract full of related functions and logic, you
can split your code across multiple contracts. Solidity lets you do this easily
with contract **inheritance**.

```solidity
contract Doge {
  function catchphrase() public returns (string memory) {
    return "So Wow CryptoDoge";
  }
}

contract BabyDoge is Doge {
  function anotherCatchphrase() public returns (string memory) {
    return "Such Moon BabyDoge";
  }
}
```

BabyDoge inherits from Doge. That means if you compile and deploy BabyDoge, it
will have access to both catchphrase() and anotherCatchphrase() (and any other
public functions we may define on Doge).

This can be used for logical inheritance (such as with a subclass, a Cat is an
Animal). But it can also be used simply for organizing your code by grouping
similar logic together into different contracts.

### [Import](https://cryptozombies.io/en/lesson/2/chapter/6)

If you need to refer to functions in another contract (e.g. if you're creating
a contract that inherits from another, or if you need to access a public
function from a contract), you can use the `import` keyword.

```solidity
import "./someothercontract.sol";

contract newContract is SomeOtherContract {

}
```

### [Storage vs. Memory (Data Location)](https://cryptozombies.io/en/lesson/2/chapter/7)

In Solidity, there are two locations you can store variables — in storage and
in memory.

Storage refers to variables stored permanently on the blockchain. Memory
variables are temporary, and are erased between external function calls to your
contract. Think of it like your computer's hard disk vs RAM.

Solidity handles these by default, so you _usually_ don't need to use them.

- _State variables_ (variables declared outside functions) are by default
  `storage` and written permanently to the blockchain.
- _Variables declared inside functions_ are `memory` and will disappear when
  the function call ends.

However, you need to use these keywords when dealing with **structs** and
**arrays** within functions:

```solidity
contract SandwichFactory {
  struct Sandwich {
    string name;
    string status;
  }

  Sandwich[] sandwiches;

  function eatSandwich(uint _index) public {
    // Sandwich mySandwich = sandwiches[_index];

    // ^ Seems pretty straightforward, but solidity will give you a warning
    // telling you that you should explicitly declare `storage` or `memory` here.

    // So instead, you should declare with the `storage` keyword, like:
    Sandwich storage mySandwich = sandwiches[_index];
    // ...in which case `mySandwich` is a pointer to `sandwiches[_index]`
    // in storage, and...
    mySandwich.status = "Eaten!";
    // ...this will permanently change `sandwiches[_index]` on the blockchain.

    // If you just want a copy, you can use `memory`:
    Sandwich memory anotherSandwich = sandwiches[_index + 1];
    // ...in which case `anotherSandwich` will simply be a copy of the
    // data in memory, and...
    anotherSandwich.status = "Eaten!";
    // ...will just modify the temporary variable and have no effect
    // on `sandwiches[_index + 1]`. But you can do this:
    sandwiches[_index + 1] = anotherSandwich;
    // ...if you want to copy the changes back into blockchain storage.
  }
}
```

### [Interacting With Other Contracts](https://cryptozombies.io/en/lesson/2/chapter/10)

For one of your contracts to talk to another contract on the blockchain that
you don't own, you need to define an **interface**.

Here's a simple example. Let's imagine that there's a contract on the
blockchain called `LuckyNumber`:

```solidity
contract LuckyNumber {
  mapping(address => uint) numbers;

  function setNum(uint _num) public {
    numbers[msg.sender] = _num;
  }

  function getNum(address _myAddress) public view returns (uint) {
    return numbers[_myAddress];
  }
}
```

This is a simple contract where you can store your lucky number and associate
it with your Ethereum address. Anyone could then look up your lucky number
using your address.

Now let's imagine we have a contract that wants to read the data in this
contract using the _public_ `getNum` function.

First, we need to define an **interface** of the `LuckyNumber` contract:

```solidity
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}
```

This looks _a lot_ like we're defining a contract, but there are a few
differences:

- We're only declaring the functions we want to interact with — in this case
  `getNum` — and we don't mention any of the other functions or state
  variables.
- We're not defining the function bodies. Instead of curly braces (`{` and
  `}`), we're simply ending the function declaration with a semi-colon (`;`).

It does look like a contract skeleton. This is how the compiler knows it's an
interface.

By including this interface in our dapp's code our contract knows what the
other contract's functions look like, how to call them, and what sort of
response to expect.

### [Using an Interface](https://cryptozombies.io/en/lesson/2/chapter/11)

Now that we've defined our `NumberInterface` (see the above section), we can
use it in a contract:

```solidity
contract MyContract {
  address NumberInterfaceAddress = 0xab38...
  // ^ The address of the FavoriteNumber contract on Ethereum
  NumberInterface numberContract = NumberInterface(NumberInterfaceAddress);
  // Now `numberContract` is pointing to the other contract

  function someFunction() public {
    // Now we can call `getNum` from that contract:
    uint num = numberContract.getNum(msg.sender);
    // ...and do something with `num` here
  }
}
```

Using this pattern, our contracts can interact with any other contract on the
Ethereum blockchain, as long as they expose functions as `public` or
`external`.

### [Handling Multiple Return Values](https://cryptozombies.io/en/lesson/2/chapter/12)

How do we handle multiple return values? Like this:

```solidity
function multipleReturns() internal returns(uint a, uint b, uint c) {
  return (1, 2, 3);
}

function processMultipleReturns() external {
  uint a;
  uint b;
  uint c;
  // This is how you do multiple assignment:
  (a, b, c) = multipleReturns();
}

// Or if we only cared about one of the values:
function getLastReturnValue() external {
  uint c;
  // We can just leave the other fields blank:
  (,,c) = multipleReturns();
}
```

### [If Statements](https://cryptozombies.io/en/lesson/2/chapter/13)

`if` statements in Solidity look just like JavaScript:

```solidity
function eatBLT(string memory sandwich) public {
  // Remember with strings, we have to compare their keccak256 hashes
  // to check equality
  if (keccak256(abi.encodePacked(sandwich)) == keccak256(abi.encodePacked("BLT"))) {
    eat();
  }
}
```

## Lesson 3: Advanced Solidity Concepts

### [Immutability of Contracts](https://cryptozombies.io/en/lesson/3/chapter/1)

After you deploy a contract to Ethereum, it's **immutable**. It can never be
modified or updated again.

The initial code you deploy to a contract is there to stay, permanently, on the
blockchain. This is one reason security is such a huge concern in Solidity. If
there's a flaw in your contract code, there's no way for you to patch it later.
You would have to tell your users to start using a different smart contract
address that has the fix.

But this is also a feature of smart contracts. The code is law. If you read the
code of a smart contract and verify it, you can be sure that every time you
call a function it's going to do exactly what the code says it will do. No one
can later change that function and give you unexpected results.

### [Ownable Contracts](https://cryptozombies.io/en/lesson/3/chapter/2)

If you have a contract with external or public functions that let users change
crucial data like Ethereum addresses, you can make contracts `Ownable`, giving
them an owner (e.g. you) with special privileges.

It's similar to how certain platforms only allow admins to perform certain
actions.

Below is the Ownable contract taken from the OpenZeppelin Solidity library.
OpenZeppelin is a library of secure and community-vetted smart contracts that
you can use in your own DApps.

Here's
[some more on access control](https://docs.openzeppelin.com/contracts/2.x/access-control)
from OpenZeppelin's own documentation.

```solidity
/**
 * @title Ownable
 * @dev The Ownable contract has an owner address, and provides basic authorization control
 * functions, this simplifies the implementation of "user permissions".
 */
contract Ownable {
  address private _owner;

  event OwnershipTransferred(
    address indexed previousOwner,
    address indexed newOwner
  );

  /**
   * @dev The Ownable constructor sets the original `owner` of the contract to the sender
   * account.
   */
  constructor() internal {
    _owner = msg.sender;
    emit OwnershipTransferred(address(0), _owner);
  }

  /**
   * @return the address of the owner.
   */
  function owner() public view returns(address) {
    return _owner;
  }

  /**
   * @dev Throws if called by any account other than the owner.
   */
  modifier onlyOwner() {
    require(isOwner());
    _;
  }

  /**
   * @return true if `msg.sender` is the owner of the contract.
   */
  function isOwner() public view returns(bool) {
    return msg.sender == _owner;
  }

  /**
   * @dev Allows the current owner to relinquish control of the contract.
   * @notice Renouncing to ownership will leave the contract without an owner.
   * It will not be possible to call the functions with the `onlyOwner`
   * modifier anymore.
   */
  function renounceOwnership() public onlyOwner {
    emit OwnershipTransferred(_owner, address(0));
    _owner = address(0);
  }

  /**
   * @dev Allows the current owner to transfer control of the contract to a newOwner.
   * @param newOwner The address to transfer ownership to.
   */
  function transferOwnership(address newOwner) public onlyOwner {
    _transferOwnership(newOwner);
  }

  /**
   * @dev Transfers control of the contract to a newOwner.
   * @param newOwner The address to transfer ownership to.
   */
  function _transferOwnership(address newOwner) internal {
    require(newOwner != address(0));
    emit OwnershipTransferred(_owner, newOwner);
    _owner = newOwner;
  }
}
```

Some things inside this contract that we haven't seen before:

- **Constructors** - `function Ownable()` is a constructor: an optional special
  function with the same name as the contract. It is executed only one time,
  when the contract is first created.
- **Function modifiers** - `modifier onlyOwner()`. Modifiers are kind of
  similar to decorators in Flask or JavaScript. They're used to modify other
  functions, usually to check some requirements prior to execution.
  - In this contract, `onlyOwner` can be used to limit access so that _only the
    owner_ of the contract can run the modified function. (Note how it's used
    in `renounceOwnership` and `transferOwnership`)
- **Indexed keyword** - we'll go into this later.

`onlyOwner` is such a common requirement for contracts that most Solidity DApps
start with a copy/paste of this Ownable contract, and then their first contract
inherits from it.

### [Function Modifiers](https://cryptozombies.io/en/lesson/3/chapter/3)

Let's delve a little deeper into function modifiers.

A function modifier uses just like a function, but uses the keyword `modifier`
instead of the keyword `function`. It also can't be called directly like a
function. Instead, we can attach the modifier's name at the end of a function
definition to change that function's behavior.

Let's look at the `onlyOwner` modifier again, as well as the two functions that
use it:

```solidity
 modifier onlyOwner() {
    require(isOwner());
    _;
  }
  /**
   * @dev Allows the current owner to relinquish control of the contract.
   * @notice Renouncing to ownership will leave the contract without an owner.
   * It will not be possible to call the functions with the `onlyOwner`
   * modifier anymore.
   */
  function renounceOwnership() public onlyOwner {
    emit OwnershipTransferred(_owner, address(0));
    _owner = address(0);
  }

  /**
   * @dev Allows the current owner to transfer control of the contract to a newOwner.
   * @param newOwner The address to transfer ownership to.
   */
  function transferOwnership(address newOwner) public onlyOwner {
    _transferOwnership(newOwner);
  }
```

Notice the `onlyOwner` modifier on the `renounceOwnership` function. When you
call `renounceOwnership`, the code inside `onlyOwner` executes first. Then when
it hits the `_;` statement in `onlyOwner`, it goes back and executes the code
inside `renounceOwnership`.

So while there are other ways you can use modifiers, one of the most common
use-cases is to add a quick require check before a function executes.

In the case of onlyOwner, adding this modifier to a function makes it so only
the owner of the contract (you, if you deployed it) can call that function.

There's a careful balance as a developer between maintaining control over a
DApp such that you can fix potential bugs, and building an owner-less platform
that your users can trust to secure their data.

### [Gas](https://cryptozombies.io/en/lesson/3/chapter/4)

#### What's Gas? 💨

In Solidity, your users have to pay every time they execute a function on your
DApp using a currency called **gas**. Users buy **gas** with **Ether** (the
currency on Ethereum), so your users have to spend ETH in order to execute
functions on your DApp.

How much gas is required to execute a function depends on how complex that
function's logic is. Each individual operation has a gas cost based roughly on
how much computing resources will be required to perform that operation (e.g.
writing to storage is much more expensive than adding two integers). The total
gas cost of your function is the sum of the gas costs of all its individual
operations.

Because running functions costs real money for your users, _code optimization
is much more important in Ethereum than in other programming languages._ If
your code is sloppy, your users are going to have to pay a premium to execute
your functions — and this could add up to millions of dollars in unnecessary
fees across thousands of users.

#### Why Is Gas Necessary?

Ethereum is like a big, slow, but extremely secure computer. When you execute a
function, every single node on the network needs to run that same function to
verify its output — thousands of nodes verifying every function execution is
what makes Ethereum decentralized, and its data immutable and
censorship-resistant.

The creators of Ethereum wanted to make sure someone couldn't clog up the
network with an infinite loop, or hog all the network resources with really
intensive computations. So they made it so transactions aren't free, and users
have to pay for computation time as well as storage.

#### How to Save Gas by Packing Structs

Remember how there are different kinds of `uint`s?

Generally, Solidity reserves 256 bits of storage for any `uint` regardless of
its size. For example, `uint8` and `uint` (`uint256`) will both use the same
amount of gas.

Unless we're doing this inside a `struct`.

If you have multiple `uint`s inside a `struct`, using a smaller `uint` when
possible will let Solidity pack these variables together to take less storage.

For example:

```solidity
struct NormalStruct {
  uint a;
  uint b;
  uint c;
}

struct MiniMe {
  uint32 a;
  uint32 b;
  uint c;
}

// `mini` will cost less gas than `normal` because of struct packing
NormalStruct normal = NormalStruct(10, 20, 30);
MiniMe mini = MiniMe(10, 20, 30);
```

Here are two general strategies for saving gas with `uint`s inside `struct`s:

- Use the smallest integer sub-types you can get away with.
- Cluster identical data types together (put them next to each other) so that
  Solidity can minimize the required storage space. For example, a `struct`
  with fields `uint c; uint32 a; uint32 b;` will cost less gas than a struct
  with fields `uint32 a; uint c; uint32 b;` because the `uint32` fields are
  clustered together.

### [Time Units](https://cryptozombies.io/en/lesson/3/chapter/5)

Solidity provides a few native unites for dealing with time.

The variable `now` will return the current unix timestamp of the latest block
(the number of seconds that have passed since January 1st 1970). The current
unix time (as of this moment on August 5, 2021) is 1628168123.

Solidity also contains the time units `seconds`, `minutes`, `hours`, `days`,
`weeks` and `years`. These will convert to a `uint` of the number of seconds in
that length of time. So `1 minutes` is 60, `1 hours` is 3600 (`60 seconds` x
`60 minutes`), `1 days` is 86400 (`24 hours` x `60 minutes` x `60 seconds`),
etc.

Here's an example of how these time units can be useful:

```solidity
uint lastUpdated;

// Set `lastUpdated` to `now`
function updateTimestamp() public {
  lastUpdated = now;
}

// Will return `true` if 5 minutes have passed since `updateTimestamp` was
// called, `false` if 5 minutes have not passed
function fiveMinutesHavePassed() public view returns (bool) {
  return (now >= (lastUpdated + 5 minutes));
}
```

### [Passing Structs as Arguments](https://cryptozombies.io/en/lesson/3/chapter/6)

You can pass a storage pointer to a struct as an argument to a private or
internal function. This is useful, for example, for passing around structs
between functions.

Here's an example of the syntax (using the `Zombie` struct previously defined
in the exercises on the site):

```solidity
function _doStuff(Zombie storage _zombie) internal {
  // do stuff with _zombie
}
```

### [More on Function Modifiers](https://cryptozombies.io/en/lesson/3/chapter/7)

Previously we looked at a relatively simple function modifier:

```solidity
 modifier onlyOwner() {
    require(isOwner());
    _;
  }
```

However, function modifiers can also take arguments. For example:

```solidity
// A mapping to store a user's age:
mapping (uint => uint) public age;

// Modifier that requires this user to be older than a certain age:
modifier olderThan(uint _age, uint _userId) {
  require(age[_userId] >= _age);
  _;
}

// Must be older than 16 to drive a car (in the US, at least).
// We can call the `olderThan` modifier with arguments like so:
function driveCar(uint _userId) public olderThan(16, _userId) {
  // Some function logic
}
```

Notice that...

- The `olderThan` modifier takes arguments just like a function does
- The `driveCar` function passes its arguments to the modifier

### [Saving Gas With `view` Functions](https://cryptozombies.io/en/lesson/3/chapter/10)

View functions don't cost gas when called externally by a user.

This is because view functions don't actually change anything on the blockchain
– they only read the data. So marking a function with view tells web3.js that
it only needs to query your local Ethereum node to run the function, and it
doesn't actually have to create a transaction on the blockchain (which would
need to be run on every single node, and cost gas).

However, if a view function is called internally from another function in the
same contract that is not a view function, it will still cost gas. This is
because the other function creates a transaction on Ethereum, and will still
need to be verified from every node. So **view functions are only free when
they're called externally.**

### [Storage Is Expensive](#https://cryptozombies.io/en/lesson/3/chapter/11)

Using `storage` is one of the more expensive operations in Solidity,
particularly writes.

This is because every time you write or change a piece of data, it’s written
permanently to the blockchain. Forever! Thousands of nodes across the world
need to store that data on their hard drives, and this amount of data keeps
growing over time as the blockchain grows. So there's a cost to doing that.

In order to keep costs down, you want to avoid writing data to storage except
when absolutely necessary. Sometimes this involves seemingly inefficient
programming logic — like rebuilding an array in memory every time a function is
called instead of simply saving that array in a variable for quick lookups.

In most programming languages, looping over large data sets is expensive. But
in Solidity, this is _much cheaper_ than using storage if it's in an _external
view function_, since view functions don't cost your users any gas. (And gas
costs your users real money!).

#### Declaring arrays in memory

You can use the memory keyword with arrays to create a new array inside a
function without needing to write anything to storage. The array will only
exist until the end of the function call, and this is a lot cheaper gas-wise
than updating an array in storage — free if it's a view function called
externally.

Here's how to declare an array in memory:

```solidity
function getArray() external pure returns(uint[] memory) {
  // Instantiate a new array in memory with a length of 3
  uint[] memory values = new uint[](3);

  // Put some values to it
  values[0] = 1;
  values[1] = 2;
  values[2] = 3;

  return values;
}
```

This is a trivial example just to show you the syntax, but in the next chapter
we'll look at combining this with for loops for real use-cases.

_Note: memory arrays must be created with a length argument (in this example,
3). They currently cannot be resized like storage arrays can with array.push(),
although this may be changed in a future version of Solidity._

### [For Loops](https://cryptozombies.io/en/lesson/3/chapter/12)

As mentioned above, it can be more gas-efficient to use a `for` loop to build
the contents of an array in a function rather than simply saving that array to
storage.

The syntax of for loops in Solidity is similar to JavaScript.

Let's look at an example where we want to make an array of even numbers:

```solidity
function getEvens() pure external returns(uint[] memory) {
  uint[] memory evens = new uint[](5);
  // Keep track of the index in the new array:
  uint counter = 0;
  // Iterate 1 through 10 with a for loop:
  for (uint i = 1; i <= 10; i++) {
    // If `i` is even...
    if (i % 2 == 0) {
      // Add it to our array
      evens[counter] = i;
      // Increment counter to the next empty index in `evens`:
      counter++;
    }
  }
  return evens;
}
```

This function will return an array with the contents [2, 4, 6, 8, 10].

## Lesson 4
